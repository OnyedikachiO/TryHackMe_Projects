# Traverse

Bob is a security engineer at a firm and works closely with the software and DevOps teams to develop a tourism web application. After the website was moved from the QA environment to Production, the team noticed that it was being hacked daily and wanted to understand the exact reasons behind this. Bob consulted the blue team for assistance but has not been successful in resolving the issue. As a result, he has decided to enroll in the Software Security pathway at THM to determine if he is making any mistakes in his approach.

To deploy the machine, click the Start Machine button in the top right corner. Access the website by visiting the URL [`https://10.81.149.8`](https://10.81.149.8/realadmin/) through your VPN connection or the AttackBox.

Can you assist Bob in identifying the vulnerabilities and restoring the website?

The user interface of the website.

![chrome_PM6w5AMwm5.png](chrome_PM6w5AMwm5.png)

 Next, we need to inspect the website using the developer tools. From the top of the tools, we can see a JavaScript file with the name`custom.min.js`

![chrome_ll4DL9wJC9.png](chrome_ll4DL9wJC9.png)

Let's check the network console to view it

![chrome_nSDV0O87pU.png](chrome_nSDV0O87pU.png)

From the response payload, there is likely an encoding left by the attacker. Using CyberChef to decode, we can retrieve the information.

![chrome_tiv3E6Ezw4.png](chrome_tiv3E6Ezw4.png)

Questions 

1. What type of encoding is used by the hackers to obfuscate the JavaScript file?
    
    `HEX`
    
2. What is the flag value after deobfuscating the file?
    
    `DIRECTORY LISTING IS THE ONLY WAY`
    

![chrome_lIwrEGW1mF.png](chrome_lIwrEGW1mF.png)

1. Logging is an important aspect. What is the name of the file containing email dumps?
    
    `email_dump.txt`
    

Let's use a map to uncover URLs on websites. We will leverage `ZAP Spider` for this purpose, along with other techniques. If you have ZAP Proxy installed on your personal machine, open it by typing "`zaproxy.`" On the dashboard, click on the tool that shows additional options and select the "`Spider`" option as shown

![chrome_VqmxUG04rY.png](chrome_VqmxUG04rY.png)

Start with the website URL, then tick the options for recursive and subtree, and click start to enumerate the site and uncover other sub-URLs.

![chrome_oyboXCIqP2.png](chrome_oyboXCIqP2.png)

From the results, we can see the file that contains the email dump.

![chrome_NdthDbrWM2.png](chrome_NdthDbrWM2.png)

1. The logs folder contains email logs and includes a message for the software team lead. What is the name of the directory that Bob created?. Since we have access to the file, we can right-click it to open the entry in a browser. Although any browser can be used, I will be using Firefox

![chrome_LmdOEWQiNL.png](chrome_LmdOEWQiNL.png)

![chrome_VwBojvVhvZ.png](chrome_VwBojvVhvZ.png)

1. What is the key file for opening the directory that Bob has created for Mark?
    
    `THM{100100111}`
    
    ![image.png](image.png)
    
2. What is the email address for ID 5 using the leaked API endpoint? The email address is [`john@traverse.com](mailto:john@traverse.com).` So, using the answer from question 4, we can visit the URL `http://Machine_IP/Q4`. The page loads, and we use the answer to question 5 as our password to access the page.

![chrome_OX18C7nBIx.png](chrome_OX18C7nBIx.png)

This provides us with a sample instruction for answering the question.

![chrome_nM9jokQxJr.png](chrome_nM9jokQxJr.png)

Please note: If you have `Postman` installed on your machine, you can start it by simply typing `"Postman"` in your search bar. Below is a sample from my setup, but I prefer to continue testing using my browser.

![chrome_qVVSVELcfQ.png](chrome_qVVSVELcfQ.png)

Using the instruction sample to find ID5 

![chrome_qlFeT0Ykip.png](chrome_qlFeT0Ykip.png)

1. What is the ID for the user with admin privileges?

`3`

![chrome_GpTg87qy1A.png](chrome_GpTg87qy1A.png)

1. What is the endpoint for logging in as the **admin**? Mention the last endpoint instead of the URL. For example, if the answer is URL is tryhackme.com/admin - Just write **/admin**.

`/realadmin`

![chrome_dxGse5IHcE.png](chrome_dxGse5IHcE.png)

1. The attacker uploaded a web shell and renamed a file used for managing the server. Can you find the name of the web shell that the attacker has uploaded?
    
    `thm_shell.php`
    
    It looks like we are going to use the endpoint we received in question 7 along with the credentials to access the admin page [`https://10.81.149.8/realadmin/`](https://10.81.149.8/realadmin/) 
    
    ![chrome_iH40Aqkd0d.png](chrome_iH40Aqkd0d.png)
    

Note: The credentials needed to access the admin page were provided in question 8

Upon examining the admin access page, we notice that we have execute privileges, which include a dropdown menu with options for the System Owner and Current Directory. We can use this to view the content; however, the results indicate that we need additional methods to execute more commands in order to obtain the information we seek.

![chrome_K5KhgJVnso.png](chrome_K5KhgJVnso.png)

Let's use the developer options to see if we can access more commands. From the results, we can see that there’s a possible way to do this by simply adding command to the provided code structure.

![explorer_7x1gOpm675.png](explorer_7x1gOpm675.png)

To do this, right-click on the `select name="commands"` and click "`Edit as HTML."` Add any additional command, but first, let’s add the command to list all content: `ls -la`.

![chrome_DpNRkDH9Rr.png](chrome_DpNRkDH9Rr.png)

Select the list content and execute; we can see the web shell uploaded by the attacker.

![chrome_0jwAovOvWW.png](chrome_0jwAovOvWW.png)

1. What is the name of the file renamed by the attacker for managing the web server?
    
    `renamed_file_manager.php`
    
2. Can you use the file manager to restore the original website by removing the "**FINALLY HACKED**" message? What is the flag value after restoring the main website?
    
    `THM{WEBSITE_RESTORED}`
    
    To complete this task, we need to access the original Admin page using the information provided in question 10. The password can be found in that question. We will access the original page by using the renamed_file_manager.php as the access endpoint, as mentioned in question 10. [`https://10.81.149.8/](https://10.81.149.8/realadmin/)renamed_file_manager.php`
    
    ![chrome_8F9wrSljev.png](chrome_8F9wrSljev.png)
    
    Please note: The password can be found in question 10 of the list content.
    

![chrome_gWAmtxwFSr.png](chrome_gWAmtxwFSr.png)

As we look ahead, we'll focus on the `index.php` file that we need to edit. Inside the editor, we’ll encounter the header message "FINALLY HACKED." Further along in the PHP code, there’s a condition that states if the `$message` variable does not equal "FINALLY HACKED," the final flag will be displayed. To proceed, let's remove the `$message` variable, save the file, and then reload the home page of the web application.

![chrome_bpFqGv8r7k.png](chrome_bpFqGv8r7k.png)

Let’s refresh the homepage, and just like that, the website is restored.

![chrome_6ANwCSqMW4.png](chrome_6ANwCSqMW4.png)