# Burpsuite: Intruder

# Tryhackme Practical Challenge

I will attempt to gain access to the support portal at `http://10.10.150.180/support/login`. This portal follows a typical login structure. Upon inspecting its source code, we find that no protective measures have been implemented:

---

```html
<form method="POST">
  <div class="form-floating mb-3">
    <input
      class="form-control"
      type="text"
      name="username"
      placeholder="Username"
      required
    />
    <label for="username">Username</label>
  </div>
  <div class="form-floating mb-3">
    <input
      class="form-control"
      type="password"
      name="password"
      placeholder="Password"
      required 
    />
    <label for="password">Password</label>
  </div>
  <div class="d-grid">
    <button class="btn btn-primary btn-lg" type="submit">Login!</button>
  </div>
</form>
```

Since there are no protective measures, we could exploit this form in several ways, including a cluster bomb attack to brute-force the credentials. But we have an easier option.

Three months ago, Bastion Hosting suffered a cyber attack that exposed employee usernames, email addresses, and plaintext passwords. The affected employees were told to change their passwords immediately, but some may have ignored this advice.

Since we have a list of known usernames and their corresponding passwords, we can use a credential-stuffing attack instead of a brute-force approach. This method is more effective and much faster, especially when using the rate-limited version of Intruder. To access the leaked credentials, download the file from the target machine with this command in the AttackBox: `wget http://10.10.150.180:9999/Credentials/BastionHostingCreds.zip`

Follow these steps to conduct a credential-stuffing attack with Burp Macros:

1. Download and prepare wordlists:
    - Download and extract the BastionHostingCreds.zip file.
    - In the extracted folder, find these wordlists:
        - emails.txt
        - usernames.txt
        - passwords.txt
        - combined.txt
        
        These files contain leaked emails, usernames, and passwords. The last file combines the email and password lists. We'll use `usernames.txt` and `passwords.txt`.
        
2. Navigate to `http://10.10.150.180/support/login` in your browser. Turn on Burp Proxy and try logging in to capture the request. Any credentials will work for this step.
3. Send the captured request to Intruder by right-clicking and selecting "Send to Intruder" or pressing `Ctrl + I`.
4. In the "Positions" sub-tab, make sure only the username and password parameters are selected. Clear any other selections, like session cookies.
5. Set the attack type to "Pitchfork."

![Screenshot_2025-10-21_08-28-30.png](Screenshot_2025-10-21_08-28-30.png)

1. Move to the "Payloads" sub-tab. You'll see two payload sets—one for the username field and one for the password field. 

/

![Screenshot_2025-10-21_08-39-31.png](57f51512-4047-4f27-8104-d960dc48cd1b.png)

- For the first payload set (usernames), go to "Payload Options," click "Load," and select the `usernames.txt` file.
- For the second payload set (passwords), repeat the process using the `passwords.txt` file.

1. Click **Start Attack** to begin the credential-stuffing attack. A warning about rate-limiting may appear—click **OK** to proceed. The attack will take a few minutes to complete in Burp Community.
2. Once the attack starts, a new window will display the results. Since Burp sent 100 requests, we need to identify which ones were successful.The response status codes won't help us here—all attempts return 302 redirects. Instead, we'll use the response length to distinguish successful logins from failed ones.

![Screenshot_2025-10-21_09-07-24.png](d8b2d054-fa05-4c37-8c8c-1aa8960f6d5d.png)

1. Click the "Length" column header to sort results by byte length. Look for the request with a shorter response—this indicates a successful login.
2. To confirm the successful login, use the credentials from the request with the shorter response length to log in.

![Screenshot_2025-10-21_09-02-09.png](Screenshot_2025-10-21_09-02-09.png)

# Challenge 2

Now that we've gained access to the support system, we can explore its features and see what actions are available.

The home interface displays a table of tickets. Clicking any row takes us to a page showing the full ticket details. The URL structure reveals a simple numbering system:

`http://10.10.215.71/support/ticket/NUMBER`

The tickets use sequential integer identifiers rather than complex, hard-to-guess IDs. This suggests two possible scenarios:

1. **Access Control**: The endpoint properly restricts access to tickets assigned to our user only.
2. **IDOR Vulnerability**: The endpoint lacks proper access controls, creating an **Insecure Direct Object Reference** (IDOR) vulnerability. If this exists, we could read all tickets regardless of who they're assigned to.

We'll use Intruder to fuzz the `/support/ticket/NUMBER` endpoint. This will reveal whether proper access controls are in place or if an IDOR vulnerability exists.

**Note:** Capture the request while logged in.

Configure the appropriate position and payload—tickets are stored at values between 1 and 100—then start the attack.

## Steps:

```python
for i in range(101):
    print(i)
```

- Save code into `100numbers.py`.
- Then run

```bash
python3 100numbers.py > numbers.txt
```

This command prints numbers from **0 to 100** and saves them to **numbers.txt**.

![Screenshot_2025-10-21_20-07-22.png](Screenshot_2025-10-21_20-07-22.png)

![Screenshot_2025-10-21_20-06-46.png](Screenshot_2025-10-21_20-06-46.png)

Configure the positions and set the attack type to "Pitchfork".

Now switch over to the Payloads tab and load the number.txt we created and then launch attack.

![Screenshot_2025-10-21_20-16-39.png](Screenshot_2025-10-21_20-16-39.png)

Either using the **Response** tab in the **Attack Results** window or by looking at each successful (i.e. 200 code) request manually in your browser, find the ticket that contains the flag.

![Screenshot_2025-10-21_20-10-54.png](Screenshot_2025-10-21_20-10-54.png)

What is the flag?

![Screenshot_2025-10-21_20-15-03.png](Screenshot_2025-10-21_20-15-03.png)

# Extra Mile Challenge

### Catching the Request

Begin by capturing a request to`http://10.10.201.21/admin/login/` and reviewing the response.

```html
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Wed, 22 Oct 2025 11:00:27 GMT
Content-Type: text/html; charset=utf-8
Connection: keep-alive
Set-Cookie: session=eyJ0b2tlbklEIjoiNjlhMjlmOGNlYWQ1ODBkMjVlNGY5ZmYxMzY2ZDE2M2QifQ.aPi5Sw.KZa8qHL4qTCJ68en4cl8nI4kFkY; HttpOnly; Path=/
Vary: Cookie
Front-End-Https: on
Content-Length: 3992
---
<form method="POST">
                  <div class="form-floating mb-3">
                        <input class="form-control" type="text" name=username  placeholder="Username" required>
                              <label for="username">Username</label>
                               </div>
                                <div class="form-floating mb-3">
                                    <input class="form-control" type="password" name=password  placeholder="Password" required>
                                    <label for="password">Password</label>
                                </div>
                                <input type="hidden" name="loginToken" value="6b8418df80d44539cebd23970101906e">
                                <div class="d-grid"><button class="btn btn-warning btn-lg" type="submit">Login!</button></div>
                            </form>
```

We observe that the login form now returns a session cookie and a hidden CSRF token (`loginToken`) in addition to the username and password fields. Refreshing the page shows both the session cookie and the `loginToken` change on every request —so each login attempt requires freshly extracted values for both.

To handle this reliably, create a Burp Macro that runs before each attack request. The macro should fetch the login page, extract the current session cookie and the hidden `loginToken`, and inject those values into the subsequent request parameters (and cookie header). In short: run the macro to obtain unique tokens, parameterize those values, and replace them in every attack request so each attempt uses a valid session cookie and CSRF token.

Point your browser to `http://10.10.201.21/admin/login/`. In Burp’s **Proxy** tab enable **Intercept**, then attempt to log in so Burp captures the request. Send the captured request to **Intruder**.

In Intruder, configure the attack as follows —mirror the setup we used for brute-forcing the support login:

1. Set **Attack type** to **Pitchfork**.
2. Clear any predefined positions so no extra payload markers remain.
3. Mark **only** the `username` and `password` form fields as positions — do not add positions for the session cookie or the CSRF token. Our Burp Macro will fetch and populate the session cookie and `loginToken` automatically for each attempt.

This ensures Intruder varies only the credentials while the macro supplies fresh session and CSRF values for every request.

![Screenshot_2025-10-22_07-13-34.png](Screenshot_2025-10-22_07-13-34.png)

Switch to the **Payloads** tab and load the same username and password wordlists used for the support-login attack.

![Screenshot_2025-10-22_07-14-09.png](Screenshot_2025-10-22_07-14-09.png)

So far Intruder is configured nearly identically to our previous credential-stuffing run — but now we must also capture the site’s changing `loginToken` and session cookie. Because the login flow issues a redirect, a “recursive grep” inside Intruder won’t reliably extract these values. Instead, we’ll create a Burp **Macro** that performs a GET to `/admin/login/` and extracts the fresh token and cookie before each request.

Creating the macro is simple:

1. Open **Settings** (top-right) and select **Sessions**.
2. Scroll to the **Macros** section and click **Add**.
3. From the request history list choose the `GET http://10.10.201.21/admin/login/` entry.
    - If it’s not present, load `http://10.10.201.21/admin/login/` in your browser and try again.
4. Click **OK**, give the macro a descriptive name, and click **OK** to finish.

When run before each Intruder request, this macro will fetch the current `loginToken` and session cookie so they can be injected into the subsequent attack requests.

![Screenshot_2025-10-22_07-15-10.png](Screenshot_2025-10-22_07-15-10.png)

![Screenshot_2025-10-22_07-16-53.png](Screenshot_2025-10-22_07-16-53.png)

![Screenshot_2025-10-22_07-17-30.png](Screenshot_2025-10-22_07-17-30.png)

With the macro created, the next step is to tell Burp how and when to use it by adding a Session Handling Rule.

1. While still in **Settings → Sessions**, scroll up to **Session Handling Rules** and click **Add** to create a new rule.
2. A dialog will open with two tabs: **Details** and **Scope**. The rule opens on the **Details** tab by default — this is where you’ll configure the actions the rule will perform (for example, running the macro and extracting token/cookie values).

![Screenshot_2025-10-22_07-19-46.png](Screenshot_2025-10-22_07-19-46.png)

![Screenshot_2025-10-22_07-16-02.png](Screenshot_2025-10-22_07-16-02.png)

Give the rule a clear description, then open the **Scope** tab.

- Under **Tools scope**, deselect every checkbox except **Intruder** — this rule only needs to run for Intruder attacks.
- Under **URL scope**, choose **Use suite scope** so the rule only applies to hosts already in Burp’s global scope.
    - If you haven’t configured a global scope, keep **Use custom scope** and add `http://10.10.201.21/` (replace `MACHINE_IP` with the target IP) to the custom scope.

Switch back to the **Details** tab and add the rule action:

1. In **Rule Actions** click **Add** and choose **Run a macro**.
2. In the popup, select the macro you created earlier and confirm.

At this point the macro will run before each Intruder request and may overwrite many values. Narrow its effects so it only updates the CSRF token and the session cookie:

1. Choose **Update only the following parameters and headers**, then click **Edit**.
2. In **Enter a new item** type `loginToken`, click **Add**, then **Close**.
3. Choose **Update only the following cookies**, then click the corresponding **Edit**.
4. In **Enter a new item** type `session`, click **Add**, then **Close**.
5. Click **OK** to save the action and finish the rule.

Now the macro will fetch fresh values before every Intruder request, and the session-handling rule will inject only the `loginToken` parameter and the `session` cookie into each attempt.

Click **OK** to finish the rule— the macro is now active and will insert the CSRF token and session cookie for each request. Switch back to **Intruder** and start the attack.

A few things to expect and check:

- Successful requests should return **HTTP 302** (Redirect). If you begin seeing **403** responses, the macro is not supplying valid tokens/cookies.
- As before, sort responses by **length** to help spot valid credentials. Response lengths will vary, but a successful login typically produces a **noticeably shorter** response than the others.
- The signal here may be noisier than the support-login run — you may see several different lengths — but the correct result should still stand out.

![Screenshot_2025-10-22_07-21-38.png](Screenshot_2025-10-22_07-21-38.png)

When you find the likely credentials, refresh the login page to get a fresh token and session cookie, then use those credentials to log in manually.

![Screenshot_2025-10-22_11-51-58.png](Screenshot_2025-10-22_11-51-58.png)

Which username and password combination indicates a successful login attempt?

Provide your answer in the following format:

**`username:password`**

(o.bennett:bella1)