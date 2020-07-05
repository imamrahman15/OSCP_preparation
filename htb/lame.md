# Hackthebox Lame
- [PDF](https://github.com/imamrahman15/tryharder/blob/master/Hackthebox%20Lame.pdf)
- [Video] --> coming soon
# 1.	High Level Summary
When performing reconnaissance and enumeration steps, there are several vulnerabilities identified on the Lame machine that can be used to gain access to the target.
- Samba
  
  Samba with version smbd 3.0.20-Debian has a vulnerability and was recorded in CVE 2007-2447, we use this vulnerability to do a reverse shell and gain root access on the target machine.
- Distcc
  
  With the nmap distcc-cve2004-2687.nse script, we use it to create a reverse shell and it will get the user daemon. we need to escalate privilege that user, and that can be done with vulnerabilities in linux kernel 2.6 that are identified on the target machine
  
# 2.	Methodology 
## 2.1.	Phase 1 – Reconnaissance
Here the results from scanning ports against target machine, you can see additional resource for the detail scan method.

Table 1 Reconnaissance - Scanning Results
```markdown
Port	State	Service	Version
21/tcp	open	ftp	vsftpd 2.3.4
22/tcp	open	ssh	OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp	open	netbios-ssn	Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp	open	netbios-ssn	Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp	open	distccd	distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
```
## 2.2.	Phase 2 - Enumeration
### 2.2.1.	FTP – VSFTPD
Table 2 Enumeration - FTP
```markdown
mr@kali:~/htb/lame$ sudo ls /usr/share/nmap/scripts/ftp-*
/usr/share/nmap/scripts/ftp-anon.nse
/usr/share/nmap/scripts/ftp-bounce.nse
/usr/share/nmap/scripts/ftp-brute.nse
/usr/share/nmap/scripts/ftp-libopie.nse
/usr/share/nmap/scripts/ftp-proftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-syst.nse
/usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse
mr@kali:~/htb/lame$ sudo nmap --script ftp-proftpd-backdoor.nse -p 21 10.10.10.3
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-05 14:57 WIB
Nmap scan report for 10.10.10.3 (10.10.10.3)
Host is up (0.26s latency).

PORT   STATE SERVICE
21/tcp open  ftp

Nmap done: 1 IP address (1 host up) scanned in 4.94 seconds
```
### 2.2.2.	SSH – OpenSSH
Table 3 Enumeration - SSH
```markdown
mr@kali:~/htb/lame$ sudo ls /usr/share/nmap/scripts/ssh*
/usr/share/nmap/scripts/ssh2-enum-algos.nse
/usr/share/nmap/scripts/ssh-auth-methods.nse
/usr/share/nmap/scripts/ssh-brute.nse
/usr/share/nmap/scripts/ssh-hostkey.nse
/usr/share/nmap/scripts/ssh-publickey-acceptance.nse
/usr/share/nmap/scripts/ssh-run.nse
/usr/share/nmap/scripts/sshv1.nse
```
### 2.2.3.	Samba
Table 4 Enumeration - Samba
```markdown
mr@kali:~/htb/lame$ sudo smbclient -L //10.10.10.3/ --option='client min protocol=NT1'
Enter WORKGROUP\root's password:
Anonymous login successful

        Sharename Type   Comment
        ---------       ----      -------
        print$          Disk   Printer Drivers
        tmp             Disk    oh noes!
        opt              Disk
        IPC$           IPC     IPC Service (lame server (Samba 3.0.20-Debian))
        ADMIN$    IPC     IPC Service (lame server (Samba 3.0.20-Debian))
Reconnecting with SMB1 for workgroup listing.
Anonymous login successful

        Server             Comment
        ---------            -------

        Workgroup      Master
        ---------            -------
        WORKGROUP            LAME



mr@kali:~/htb/lame$ sudo smbmap -H 10.10.10.3
[+] IP: 10.10.10.3:445  Name: 10.10.10.3
        Disk                                                  Permissions       Comment
        ----                                                    -----------            -------
        print$                                                NO ACCESS     Printer Drivers
        tmp                                                   READ, WRITE  oh noes!
        opt                                                    NO ACCESS      
        IPC$                                                 NO ACCESS      IPC Service (lame server (Samba 3.0.20-Debian))
        ADMIN$                                          NO ACCESS      IPC Service (lame server (Samba 3.0.20-Debian))
mr@kali:~/htb/lame$ searchsploit samba 3.0.20
-------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                        |  Path
-------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Samba 3.0.10 < 3.3.5 - Format String / Security Bypass                                                                           | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)                           | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                                                                                                 | linux/remote/7701.txt
Samba < 3.0.20 - Remote Heap Overflow                                                                                                 | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC)                                                                                         | linux_x86/dos/36741.py
-------------------------------------------------------------------------------------------------------------------------------------- ----------------------------
Shellcodes: No Results
```
### 2.2.4.	DISTCCD
Table 5 Enumeration – DISTCC
```markdown
mr@kali:~/htb/lame$ ls /usr/share/nmap/scripts/distcc*
/usr/share/nmap/scripts/distcc-cve2004-2687.nse
mr@kali:~/htb/lame$ sudo nmap --script distcc-cve2004-2687.nse -p 3632 10.10.10.3
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-05 14:58 WIB
Nmap scan report for 10.10.10.3
Host is up (0.30s latency).
PORT     STATE SERVICE
3632/tcp open  distccd
| distcc-cve2004-2687:
|   VULNERABLE:
|   distcc Daemon Command Execution
|     State: VULNERABLE (Exploitable)
|     IDs:  CVE:CVE-2004-2687
|     Risk factor: High  CVSSv2: 9.3 (HIGH) (AV:N/AC:M/Au:N/C:C/I:C/A:C)
|       Allows executing of arbitrary commands on systems running distccd 3.1 and
|       earlier. The vulnerability is the consequence of weak service configuration.
|
|     Disclosure date: 2002-02-01
|     Extra information:
|
|     uid=1(daemon) gid=1(daemon) groups=1(daemon)
|
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2004-2687
|       https://nvd.nist.gov/vuln/detail/CVE-2004-2687
|_      https://distcc.github.io/security.html
Nmap done: 1 IP address (1 host up) scanned in 16.57 seconds
mr@kali:~/htb/lame$ searchsploit vsftpd
-------------------------------------------------------------------------------------------------------------------------------------- 
 Exploit Title                                                                                                                        |  Path
-------------------------------------------------------------------------------------------------------------------------------------- 
vsftpd 2.0.5 - 'CWD' (Authenticated) Remote Memory Consumption                               | linux/dos/5814.pl
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (1)                                          | windows/dos/31818.sh
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (2)                                          | windows/dos/31819.pl
vsftpd 2.3.2 - Denial of Service                                                                                           | linux/dos/16270.c
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                | unix/remote/17491.rb
-------------------------------------------------------------------------------------------------------------------------------------- 
Shellcodes: No Results
```
## 2.3.	Phase 3 - Penetration
### 2.3.1.	Samba
### 2.3.2.	DISTCCD
# 3.	Additional Resource
## 3.1.	Initial Scan
## 3.2.	Full Scan
## 3.3.	UDP Scan
-------------------------------------------------------------------------
## Welcome to Lame Pages

You can use the [editor on GitHub](https://github.com/imamrahman15/OSCP_preparation/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/imamrahman15/OSCP_preparation/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
