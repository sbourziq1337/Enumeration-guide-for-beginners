## __*What is RDP?__
-------------------
![[Pasted image 20241229051414.png]]
* The [Remote Desktop Protocol](https://docs.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol) (`RDP`) is a protocol developed by Microsoft for remote access to a computer running the Windows operating system. This protocol allows display and control commands to be transmitted via the GUI encrypted over IP networks. RDP works at the application layer in the TCP/IP reference model, typically utilizing TCP port 3389 as the transport protocol. However, the connectionless UDP protocol can use port 3389 also for remote administration.

* ***Default port:** 3389

```
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
```

* RDP is widely used for Windows remote connections, but you can also access and interact with the graphical user interface of a remote Linux server by using a tool like [xrdp](http://xrdp.org/), an open-source implementation of the RDP server.
## *configure xrdp on Linux
-----------------------------------
* *Understanding how to establish access to a remote Linux server by configuring and using an RDP connection.
* [# How To Enable Remote Desktop Protocol Using xrdp](https://www.digitalocean.com/community/tutorials/how-to-enable-remote-desktop-protocol-using-xrdp-on-ubuntu-22-04)
* [# How to deploy and configure xrdp on Linux](https://www.techtarget.com/searchvirtualdesktop/tip/How-to-deploy-and-configure-xrdp-on-Linux)

## __*Connect
--------------------------
### Connect Using Remote Desktop Connection

You can connect to an RDP server using the Remote Desktop Connection utility on Windows or through third-party clients on other operating systems.

```
mstsc /v:<target-ip>:<port>
```

### *Connect Using xfreerdp
********************************
xfreerdp is an open-source RDP client available for various platforms, including Linux.

```
xfreerdp /v:<target-ip>:<port>
```

# Enumeration
-------------------------
## *Nmap

* Scanning the RDP service can quickly give us a lot of information about the host. For example, we can determine if `NLA` is enabled on the server or not, the product version, and the hostname

* Find encryption type:

* `nmap -p 3389 --script rdp-enum-encryption <target>`

* Enumerates information from remote RDP services with CredSSP (NLA) authentication enabled:

* `nmap -p 3389 --script rdp-ntlm-info <target>`

* Check if vulnerable to MS12-020:

* `nmap -sV --script=rdp-vuln-ms12-020 -p 3389 <targe`

* __For exmple:__
  
				  `Windows Remote Management Protocols`
```shell-session
akerashadow@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --script rdp*

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 15:45 CET
Nmap scan report for 10.129.201.248
Host is up (0.036s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-enum-encryption: 
|   Security layer
|     CredSSP (NLA): SUCCESS
|     CredSSP with Early User Auth: SUCCESS
|_    RDSTLS: SUCCESS
| rdp-ntlm-info: 
|   Target_Name: ILF-SQL-01
|   NetBIOS_Domain_Name: ILF-SQL-01
|   NetBIOS_Computer_Name: ILF-SQL-01
|   DNS_Domain_Name: ILF-SQL-01
|   DNS_Computer_Name: ILF-SQL-01
|   Product_Version: 10.0.17763
|_  System_Time: 2021-11-06T13:46:00+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.26 seconds
```
#### RDP Security Check
-----------------------
  *Windows Remote Management Protocols

```shell-session
akerashadow@htb[/htb]$ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
akerashadow@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248
```

* The rdp-sec-check tool checks which encryption algorithms and authentication methods are used, as well as some other security settings. At the end of the check, rdp-sec-check summarizes the potential security issues of the Remote Desktop Service
* Install rdp-sec-check on Kali Linux

```
sudo cpan
install Encoding::BER
Ctrl+d
wget https://raw.githubusercontent.com/portcullislabs/rdp-sec-check/master/rdp-sec-check.pl
chmod +x rdp-sec-check.pl
./rdp-sec-check.pl --help
```

* The command to run is very simple:

```
rdp-sec-check HOST
```

* For example:

```
rdp-sec-check 192.168.0.101
```

* In the screenshot you can see the security methods used on the remote RDP server. You can see that after the line [+] Summary of security issues there is nothing, then there are no obvious problems.

* Let's check another, less secure host:

```
rdp-sec-check 192.168.0.89
```

* Here we see the following RDP server security issues:

```
[+] Summary of security issues

[-] 192.168.0.89:3389 has issue NLA_NOT_SUPPORTED_DOS
[-] 192.168.0.89:3389 has issue SSL_SUPPORTED_BUT_NOT_MANDATED_MITM
```

* They say that NLA is not used and therefore a DOS attack is possible. I’ll add from myself that if the NLA is not used, then a man-in-the-middle attack is also possible. It is further stated that SSL is supported, but not required, which makes the MITM (man-in-the-middle) attack possible.

* __Authentication and connection to such RDP servers can be made in several ways. For example, we can connect to RDP servers on Linux using `xfreerdp`, `rdesktop`, or `Remmina` and interact with the GUI of the server accordingly.

* __*For exmple:

 ```shell-session
xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
```

* After successful authentication, a new window will appear with access to the server's desktop to which we have connected.

# __*WinRM__
----------------

* [Windows Remote Management (WinRM)](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426(v=vs.85).aspx) is highlighted as a **protocol by Microsoft** that enables the **remote management of Windows systems** through HTTP(S), leveraging SOAP in the process. It's fundamentally powered by WMI, presenting itself as an HTTP-based interface for WMI operations.

* The presence of WinRM on a machine allows for straightforward remote administration via PowerShell, akin to how SSH works for other operating systems. To determine if WinRM is operational, checking for the opening of specific ports is recommended:

```
1. 5985/tcp (HTTP)
2. 5986/tcp (HTTPS)
```
An open port from the list above signifies that WinRM has been set up, thus permitting attempts to initiate a remote session

### Info

* WinRM uses the PSRemoting (PowerShell remote) to login to a host and execute commands.

* You can enable WinRM by running `Enable-PSRemoting`

* By default WinRM uses port 5985 for sending traffic over HTTP (But it's still encrypted), and port 5986 for SSL.

* If you can make a HTTP request (GET) to `/wsman` and you get 200 back, WinRM is enabled (on port 5985).
### Enumeration WinRM

```
ping 10.4.30.175

nmap --top-ports 7000 10.4.30.175
```

```
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
```

```
nmap -sV -p 5985 10.4.30.175
```

```
PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
```
#### Nmap WinRM

  Windows Remote Management Protocols

```shell-session
akerashadow@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 16:31 CET
Nmap scan report for 10.129.201.248
Host is up (0.030s latency).

PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.34 seconds
```

* if we want to find out whether one or more remote servers can be reached via WinRM, we can easily do this with the help of PowerShell. The [Test-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2) cmdlet is responsible for this, and the host's name in question is passed to it. In Linux-based environments, we can use the tool called [evil-winrm](https://github.com/Hackplayers/evil-winrm), another penetration testing tool designed to interact with WinRM.

 *  Windows Remote Management Protocols

```shell-session
akerashadow@htb[/htb]$ evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!

Evil-WinRM shell v3.3

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Cry0l1t3\Documents>
```

## WMI
----------------------------

* Windows Management Instrumentation (`WMI`) is Microsoft's implementation and also an extension of the Common Information Model (`CIM`), core functionality of the standardized Web-Based Enterprise Management (`WBEM`) for the Windows platform. WMI allows read and write access to almost all settings on Windows systems. Understandably, this makes it the most critical interface in the Windows environment for the administration and remote maintenance of Windows computers, regardless of whether they are PCs or servers. WMI is typically accessed via PowerShell, VBScript, or the Windows Management Instrumentation Console (`WMIC`). WMI is not a single program but consists of several programs and various databases, also known as repositories.

---

## Footprinting the Service
************************************

* The initialization of the WMI communication always takes place on `TCP` port `135`, and after the successful establishment of the connection, the communication is moved to a random port. For example, the program [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py) from the Impacket toolkit can be used for this.

#### WMIexec.py

 *  Windows Remote Management Protocols

```shell-session
akerashadow@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] SMBv3.0 dialect used
ILF-SQL-01
```

* Again, it is necessary to mention that the knowledge gained from installing these services and playing around with the configurations on our own Windows Server VM for gaining experience and developing the functional principle and the administrator's point of view cannot be replaced by reading manuals. Therefore, we strongly recommend setting up your own Windows Server, experimenting with the settings, and scanning these services repeatedly to see the differences in the results.

### *__Additional Resources__
 * https://blog.syselement.com/ine/courses/ejpt/hostnetwork-penetration-testing/1-system-attack/windows-attacks/winrm
 * https://blog.syselement.com/ine/courses/ejpt/hostnetwork-penetration-testing/1-system-attack/windows-attacks/rdp
 * https://www.cyberlibrary.fr/en/docs/old/59855986-pentesting-winrm
 * https://hackviser.com/tactics/pentesting/services/rdp
 * https://book.hacktricks.xyz/network-services-pentesting/5985-5986-pentesting-winrm
 * 

> **_Thanks for Reading!_**
> * [ LinkedIn](https://www.linkedin.com/in/soufiane-bourziq-391213266/)