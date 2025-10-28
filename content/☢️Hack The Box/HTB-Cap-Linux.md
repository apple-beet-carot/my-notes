---
title: HTB-Cap-Linux
date: 2025-10-21
tags:
  - htb
  - hacking
  - linux
  - dfir
draft: false
---
### How to check opened ports?
I tired port scanning with rustscan.
### Where did I find the password?
The PCAP file contains sensative data as the password.
### How did you get root privileges?
You need to find the binary had special capabilities. You can run "linpeas" on kali to help find it, and use the "getcap" command to check the binary's capabilities.
### References
- Target : https://app.hackthebox.com/machines/Cap
- RUSTSCAN : https://github.com/bee-san/RustScan/releases/download/2.4.1/x86_64-linux-rustscan.tar.gz.zip
- GTFOBins : https://gtfobins.github.io/
- LOLABS : https://lolbas-project.github.io/

