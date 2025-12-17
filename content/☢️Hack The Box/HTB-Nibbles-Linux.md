---
title: HTB-Nibbles-Linux
date: 2025-12-09
tags:
  - htb
  - linux
draft: false
---
### How many open TCP ports are listening on Nibbles?
```rust
┌──(kali㉿kali)-[~/htb]
└─$ ./rustscan -a 10.129.15.22                    
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Please contribute more quotes to our GitHub https://github.com/rustscan/rustscan

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.129.15.22:22
Open 10.129.15.22:80
[~] Starting Script(s)
[~] Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-08 23:46 EST
Initiating Ping Scan at 23:46
Scanning 10.129.15.22 [4 ports]
Completed Ping Scan at 23:46, 0.24s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:46
Completed Parallel DNS resolution of 1 host. at 23:46, 0.04s elapsed
DNS resolution of 1 IPs took 0.04s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 23:46
Scanning 10.129.15.22 [2 ports]
Discovered open port 80/tcp on 10.129.15.22
Discovered open port 22/tcp on 10.129.15.22
Completed SYN Stealth Scan at 23:46, 0.45s elapsed (2 total ports)
Nmap scan report for 10.129.15.22
Host is up, received reset ttl 63 (0.27s latency).
Scanned at 2025-12-08 23:46:24 EST for 0s

PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.82 seconds
           Raw packets sent: 6 (240B) | Rcvd: 6 (236B)
```
### What is the relative path on the webserver to a blog?
You can find the path on the view source of the main page.
```html
<b>Hello world!</b>

<!-- /nibbleblog/ directory. Nothing interesting here! -->
```
### What content management system (CMS) is being used by the blog?
The page says "Powered by" in the bottom.
```
Powered by Nibbleblog
```
### What is the relative path to an XML file that contains the admin username?
You need to run scanner like dirsearch or feroxbuster. I'm familiar with the dirsearch. But dirsearch wasn't better than feroxbuster in this case.
```shell
┌──(venv)─(kali㉿kali)-[~/htb/dirsearch]
└─$ feroxbuster -u http://10.129.15.22/nibbleblog/ -w /usr/share/wordlists/dirb/common.txt -x xml       
                                                                                                                                                
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.15.22/nibbleblog
 🚩  In-Scope Url          │ 10.129.15.22
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/wordlists/dirb/common.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.13.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 💲  Extensions            │ [xml]
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       32w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET       11l       32w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET        9l       28w      317c http://10.129.15.22/nibbleblog => http://10.129.15.22/nibbleblog/
301      GET        9l       28w      323c http://10.129.15.22/nibbleblog/admin => http://10.129.15.22/nibbleblog/admin/
200      GET       27l       96w     1401c http://10.129.15.22/nibbleblog/admin.php
200      GET       51l       99w      902c http://10.129.15.22/nibbleblog/admin/js/functions.js
200      GET       15l       16w      468c http://10.129.15.22/nibbleblog/admin/boot/feed.bit
200      GET       13l       15w      430c http://10.129.15.22/nibbleblog/admin/boot/ajax.bit
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/uploader.php
200      GET       21l       20w      618c http://10.129.15.22/nibbleblog/admin/boot/admin.bit
200      GET        1l        2w       13c http://10.129.15.22/nibbleblog/admin/ajax/mobile.php
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/categories.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/plugin.class.php
200      GET       71l      116w     1240c http://10.129.15.22/nibbleblog/admin/js/ajax_form.bit
200      GET        1l       21w      277c http://10.129.15.22/nibbleblog/admin/js/system.php
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/settings.php
200      GET        9l       10w      248c http://10.129.15.22/nibbleblog/admin/ajax/security.bit
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/posts_get_video_info.php
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/pages.php
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/uploader%20(copy).php
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/comments.php
200      GET        1l        3w       86c http://10.129.15.22/nibbleblog/admin/ajax/posts.php
200      GET        1l       47w     3532c http://10.129.15.22/nibbleblog/admin/js/tinymce/jquery.tinymce.min.js
200      GET       96l      152w     2439c http://10.129.15.22/nibbleblog/admin/views/page/list.bit
200      GET       30l       37w      563c http://10.129.15.22/nibbleblog/admin/views/page/new.bit
200      GET       33l      107w     1360c http://10.129.15.22/nibbleblog/admin/views/categories/edit.bit
200      GET       78l      194w     2720c http://10.129.15.22/nibbleblog/admin/templates/easy4/index.bit
200      GET       90l      172w     2458c http://10.129.15.22/nibbleblog/admin/views/categories/list.bit
200      GET       33l       66w     1014c http://10.129.15.22/nibbleblog/admin/controllers/categories/edit.bit
200      GET       27l       51w      754c http://10.129.15.22/nibbleblog/admin/controllers/categories/list.bit
200      GET       14l       23w      530c http://10.129.15.22/nibbleblog/admin/controllers/comments/list.bit
200      GET       14l       22w      526c http://10.129.15.22/nibbleblog/admin/controllers/comments/settings.bit
200      GET       17l       24w      531c http://10.129.15.22/nibbleblog/admin/controllers/dashboard/view.bit
200      GET       14l       25w      524c http://10.129.15.22/nibbleblog/admin/controllers/post/list.bit
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/defensio/Defensio.php
200      GET      109l      229w     3125c http://10.129.15.22/nibbleblog/admin/controllers/post/edit.bit
200      GET       89l      156w     2079c http://10.129.15.22/nibbleblog/admin/controllers/post/new.bit
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/filesystem.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/number.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/blog.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/cookie.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/post.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/social.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/url.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/page.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/category.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/plugin.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/language.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/resize.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/text.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/html.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/crypt.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/pager.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/email.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/date.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/redirect.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/image.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/validation.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/net.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/video.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/helpers/session.class.php
200      GET       11l       22w      305c http://10.129.15.22/nibbleblog/admin/views/user/send_forgot.bit
200      GET       25l       74w     1127c http://10.129.15.22/nibbleblog/admin/views/user/login.bit
200      GET       19l       53w      763c http://10.129.15.22/nibbleblog/admin/views/user/forgot.bit
200      GET       67l      162w     2713c http://10.129.15.22/nibbleblog/admin/views/settings/regional.bit
200      GET       97l      309w     4774c http://10.129.15.22/nibbleblog/admin/views/settings/general.bit
200      GET      125l      346w     5337c http://10.129.15.22/nibbleblog/admin/views/settings/seo.bit
200      GET       59l      179w     3220c http://10.129.15.22/nibbleblog/admin/views/settings/notifications.bit
200      GET       19l       45w      728c http://10.129.15.22/nibbleblog/admin/views/plugins/config.bit
200      GET       25l       34w      385c http://10.129.15.22/nibbleblog/admin/views/dashboard/view.bit
200      GET       64l      130w     1845c http://10.129.15.22/nibbleblog/admin/views/dashboard/notifications.bit
200      GET       38l       48w      886c http://10.129.15.22/nibbleblog/admin/controllers/settings/themes.bit
200      GET       11l       14w      352c http://10.129.15.22/nibbleblog/admin/controllers/settings/notifications.bit
200      GET       36l       45w      698c http://10.129.15.22/nibbleblog/admin/views/post/edit.bit
200      GET       14l       14w      356c http://10.129.15.22/nibbleblog/admin/controllers/plugins/list.bit
200      GET       19l       34w      549c http://10.129.15.22/nibbleblog/admin/controllers/plugins/install.bit
200      GET       16l       19w      355c http://10.129.15.22/nibbleblog/admin/controllers/plugins/uninstall.bit
200      GET       71l      127w     1745c http://10.129.15.22/nibbleblog/admin/controllers/page/edit.bit
200      GET       30l       46w      740c http://10.129.15.22/nibbleblog/admin/controllers/settings/regional.bit
200      GET       95l      156w     2287c http://10.129.15.22/nibbleblog/admin/views/post/list.bit
200      GET       93l      172w     2327c http://10.129.15.22/nibbleblog/admin/views/post/new_video.bit
200      GET       13l       34w      412c http://10.129.15.22/nibbleblog/admin/controllers/settings/general.bit
200      GET        4l     1304w    83615c http://10.129.15.22/nibbleblog/admin/js/jquery/jquery.js
200      GET       25l       24w      767c http://10.129.15.22/nibbleblog/admin/boot/blog.bit
200      GET      160l      375w     4744c http://10.129.15.22/nibbleblog/admin/js/reveal/jquery.reveal.js
200      GET      130l      224w     3049c http://10.129.15.22/nibbleblog/admin/views/comments/list.bit
200      GET       81l      265w     4729c http://10.129.15.22/nibbleblog/admin/views/comments/settings.bit
200      GET       64l      208w     3738c http://10.129.15.22/nibbleblog/admin/views/settings/image.bit
200      GET      112l      227w     2925c http://10.129.15.22/nibbleblog/admin/boot/rules/98-blog.bit
200      GET      105l      209w     2647c http://10.129.15.22/nibbleblog/admin/boot/rules/98-plugins.bit
200      GET       98l      230w     3382c http://10.129.15.22/nibbleblog/admin/boot/rules/3-variables.bit
200      GET       27l       39w      470c http://10.129.15.22/nibbleblog/admin/boot/rules/5-regional.bit
200      GET      182l      373w     3933c http://10.129.15.22/nibbleblog/admin/boot/rules/8-posts_pages.bit
200      GET       89l      195w     3102c http://10.129.15.22/nibbleblog/admin/boot/rules/1-fs_php.bit
200      GET       91l      333w     7130c http://10.129.15.22/nibbleblog/admin/boot/rules/11-admin.bit
200      GET       18l       20w      148c http://10.129.15.22/nibbleblog/admin/boot/rules/10-session.bit
200      GET       77l      144w     1870c http://10.129.15.22/nibbleblog/admin/boot/rules/8-posts_pages_feed.bit
200      GET       16l       21w      175c http://10.129.15.22/nibbleblog/admin/boot/rules/4-blacklist.bit
200      GET       37l       53w      824c http://10.129.15.22/nibbleblog/admin/boot/rules/98-constants.bit
200      GET       96l      325w     3796c http://10.129.15.22/nibbleblog/admin/boot/rules/2-objects.bit
200      GET       31l       50w      711c http://10.129.15.22/nibbleblog/admin/boot/rules/10-seo.bit
200      GET       15l       19w      112c http://10.129.15.22/nibbleblog/admin/boot/rules/99-misc.bit
200      GET       61l      153w     1869c http://10.129.15.22/nibbleblog/admin/boot/rules/5-url.bit
200      GET       54l       95w     1250c http://10.129.15.22/nibbleblog/admin/boot/rules/98-comments.bit
200      GET       75l      176w     2090c http://10.129.15.22/nibbleblog/admin/boot/rules/10-pager.bit
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_settings.class.php
200      GET       40l       77w      795c http://10.129.15.22/nibbleblog/admin/boot/rules/4-remove_magic.bit
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_tags.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_posts.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/nbxml.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_pages.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_comments.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_notifications.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_users.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/db/db_categories.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/api/comment.class.php
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/admin/kernel/api/login.class.php
200      GET       10l     3958w   260266c http://10.129.15.22/nibbleblog/admin/js/tinymce/tinymce.min.js
200      GET       28l       48w      932c http://10.129.15.22/nibbleblog/admin/views/settings/themes.bit
200      GET       51l      158w     2365c http://10.129.15.22/nibbleblog/admin/views/settings/username.bit
200      GET       26l       69w     1278c http://10.129.15.22/nibbleblog/admin/views/plugins/list.bit
200      GET       27l       52w      794c http://10.129.15.22/nibbleblog/admin/views/dashboard/last_comments.bit
200      GET       21l       66w     1265c http://10.129.15.22/nibbleblog/admin/views/dashboard/quick_start.bit
200      GET       21l       39w      638c http://10.129.15.22/nibbleblog/admin/views/dashboard/drafts.bit
200      GET       13l       34w      441c http://10.129.15.22/nibbleblog/admin/controllers/settings/advanced.bit
200      GET       33l       41w      620c http://10.129.15.22/nibbleblog/admin/views/post/new_simple.bit
200      GET       38l       83w     1068c http://10.129.15.22/nibbleblog/admin/templates/login/index.bit
200      GET       31l       38w      593c http://10.129.15.22/nibbleblog/admin/views/post/new_quote.bit
200      GET       19l       23w      570c http://10.129.15.22/nibbleblog/admin/controllers/settings/image.bit
200      GET       47l      110w     1583c http://10.129.15.22/nibbleblog/admin/controllers/settings/username.bit
200      GET       11l       14w      352c http://10.129.15.22/nibbleblog/admin/controllers/settings/seo.bit
200      GET       65l      111w     1556c http://10.129.15.22/nibbleblog/admin/controllers/page/new.bit
200      GET       25l       40w      747c http://10.129.15.22/nibbleblog/admin/controllers/page/list.bit
200      GET       44l       59w     1164c http://10.129.15.22/nibbleblog/admin/controllers/user/send_forgot.bit
200      GET       41l       80w     1502c http://10.129.15.22/nibbleblog/admin/controllers/user/login.bit
200      GET        7l        4w       75c http://10.129.15.22/nibbleblog/admin/controllers/user/logout.bit
200      GET       51l      100w     1484c http://10.129.15.22/nibbleblog/admin/controllers/user/forgot.bit
200      GET       59l      118w     1969c http://10.129.15.22/nibbleblog/admin/controllers/plugins/config.bit
301      GET        9l       28w      325c http://10.129.15.22/nibbleblog/content => http://10.129.15.22/nibbleblog/content/
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/content/private/keys.php
200      GET        2l       50w     1936c http://10.129.15.22/nibbleblog/content/private/config.xml
200      GET        0l        0w        0c http://10.129.15.22/nibbleblog/content/private/shadow.php
200      GET        2l        6w       93c http://10.129.15.22/nibbleblog/content/private/posts.xml
200      GET        2l        6w       95c http://10.129.15.22/nibbleblog/content/private/pages.xml
200      GET        2l        6w       97c http://10.129.15.22/nibbleblog/content/private/tags.xml
200      GET        2l       17w      503c http://10.129.15.22/nibbleblog/content/private/users.xml
...
```
You can find the username in the XML file.
The XML file: `http://target/nibbleblog/content/private/users.xml`
```XML
<users>
	<user username="admin">
		<id type="integer">0</id>
		<session_fail_count type="integer">0</session_fail_count>
		<session_date type="integer">1514544131</session_date>
	</user>
	<blacklist type="string" ip="10.10.10.1">
		<date type="integer">1512964659</date>
		<fail_count type="integer">1</fail_count>
	</blacklist>
	<blacklist type="string" ip="10.10.16.11">
		<date type="integer">1765262909</date>
		<fail_count type="integer">1</fail_count>
	</blacklist>
</users>
```
### What is the admin user's password to log into the blog?
We can confirm the administrator login page at `admin.php`, as shown below. However, Nibbleblog has protection mechanisms that block access after several incorrect password attempts. Given the whole process, finding the password is just one step. Is it even worthwhile to use an automated tool with a wordlist for a brute-force attack? Since this is just a transitional step, shouldn't we assume the password is an obvious value that can be easily guessed?
![[../Images/content/☢️Hack The Box/HTB-Nibbles-Linux/HTB-Nibbles-Linux-20251215134654720.png]]
### What version of nibble blog is running on the target machine? Do not include the "v".
You can find the version number in setting menu after successfully logging in with admin credentials.
![[../Images/content/☢️Hack The Box/HTB-Nibbles-Linux/HTB-Nibbles-Linux-20251215140035380.png]]
### What is the 2015 CVE ID for an authenticated code execution by file upload vulnerability in this version of NibbleBlog.
You can get the CVE ID after googling Productname with version number `CVE-2015-6967`.
![[../Images/content/☢️Hack The Box/HTB-Nibbles-Linux/HTB-Nibbles-Linux-20251215140444715.png]]
### Which user the Nibbleblog instance is running on the target machine?
![[../Images/content/☢️Hack The Box/HTB-Nibbles-Linux/HTB-Nibbles-Linux-20251215141815819.png]]
### Submit the flag located in the nibbler user's home directory.
![[../Images/content/☢️Hack The Box/HTB-Nibbles-Linux/HTB-Nibbles-Linux-20251215141751482.png]]
### What is the name of the script that nibbler can run as root on Nibbles?
You can get `monitor.sh` when you unzipped the personal.zip in home path.
### Enter the permission set on `monitor.sh`? User the Linux file permissions format, like `-rw-rw-r--`.
`-rwxrwxrwx`
![[../Images/content/☢️Hack The Box/HTB-Nibbles-Linux/HTB-Nibbles-Linux-20251216135946296.png]]
### Submit Root Flag
![[../Images/content/☢️Hack The Box/HTB-Nibbles-Linux/HTB-Nibbles-Linux-20251216140028502.png]]
### References
- https://lonelynova.tistory.com/317
- https://www.revshells.com/