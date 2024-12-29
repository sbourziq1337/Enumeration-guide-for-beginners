SMB stands for Server Message Block. Its mainly used for providing shared access to files, printers and miscellaneous communications between nodes on a network. It also provides an authenticated inter-process communication mechanism. It is a predecessor of Common Internet File system (CIFS). To know more about SMB please [go here](https://www.samba.org/cifs/docs/what-is-smb.html).

## Connect
-------------------------------

In order to initiate the process, it's imperative to establish a connection to the Server Message Block (SMB) server.

To list all available shares on the target server, utilize the following command:

```
smbclient -L //target-ip
```

#### Software Application for Implementation of SMB Protocol
----------------------------------------------

The open-source software called SAMBA that can run best on UNIX environment and able to communicate with Microsoft Window’s Clients so that SAMBA is able to provide the sharing service by employing this common internet file system. In general words, SAMBA server allows resource sharing between a system running Microsoft OS and systems running UNIX.

**SAMBA INSTALLATION / CONFIGURATION**  
**On Terminal**
```
sudo apt update
sudo apt install samba
```

![samba](https://media.geeksforgeeks.org/wp-content/uploads/1-356.png)

#### Default Configuration

```shell-session
cat /etc/samba/smb.conf | grep -v "#\|\;" 
```
#### Restart Samba

```shell-session
root@samba:~# sudo systemctl restart smbd
```

Now we can display a list (`-L`) of the server's shares with the `smbclient` command from our host. We use the so-called `null session` (`-N`), which is `anonymous` access without the input of existing users or valid passwords.

#### SMBclient - Connecting to the Share

```shell-session
[!bash!]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```
We can see that we now have five different shares on the Samba server from the result. Thereby `print$` and an `IPC$` are already included by default in the basic setting, as we have already seen. Since we deal with the `[notes]` share, let us log in and inspect it using the same client program. If we are not familiar with the client program, we can use the `help` command on successful login, listing all the possible commands we can execute.

```shell-session
[!bash!]$ smbclient //10.129.14.128/notes
```

### __SMB Enumeration for Hostname
----------------------------------------

There are plenty of tools that can enumerate Hostname, here to demonstrate we are using nmblookup and nbtscan. Nmblookup tool makes use of queries of the NetBIOS [[NetBIOS enumeration]] names and maps them to their related [IP addresses](https://www.geeksforgeeks.org/what-is-an-ip-address/) in a network.

**1. Nmblookup:**

```
nmblookup -A <Target IP>
```

![nmblookup](https://media.geeksforgeeks.org/wp-content/uploads/20220917131836/smbenum.png)

Here, you can see that we have enumerated the hostname to CAJA.

**2. Nbtscan:**

```
nbtscan <target IP>
```

![nbtscan](https://media.geeksforgeeks.org/wp-content/uploads/20220917132407/smbenum2.png)

Here, you can see that we have enumerated the hostname to CAJA as the same as the above scan through nmblookup.

### **SMB Enumeration for Share and Null Session:**==
----------------------------------

In this part, we are going to enumerate shares of the host or target system. We can perform this enumeration with many tools, for this article we are going to use smbmap, smbclient, Nmap, and Metasploit for different ways of performing this share enumeration.

**1. Smbmap:** Smbmap allows the attacker to enumerate samba share drives on the IP address. It also lists drive permissions and upload/download functionality.

```
 smbmap -H <target IP>
```

![smbmap](https://media.geeksforgeeks.org/wp-content/uploads/20220917184841/smbshare.png)

 Also, you can scan for specific user shares using the credentials by using the below command
```
 smbmap -H <target IP> -u username -p password
```
 https://github.com/ShawnDEvans/smbmap

**2. smbclient:** It is a samba-client, and it is useful to test connectivity to windows shares.

```
$ smbclient -L <target IP>
```

![smbclient](https://media.geeksforgeeks.org/wp-content/uploads/20220917190103/smbclient.png)

**3. Nmap:**  [Nmap](https://www.geeksforgeeks.org/nmap-command-in-linux-with-examples/) provides smb-enum-shares NSE script which can be used to enumerate the shares.
```
$ nmap --script smb-enum-shares -p 139,445 <Target IP>
```

![nmap](https://media.geeksforgeeks.org/wp-content/uploads/20220917190937/smbnmap.png)

We can see from the results that it is not very much that Nmap provided us with here. Therefore, we should resort to other tools that allow us to interact manually with the SMB and send specific requests for the information. One of the handy tools for this is `rpcclient`. This is a tool to perform MS-RPC functions.

The [Remote Procedure Call](https://www.geeksforgeeks.org/remote-procedure-call-rpc-in-operating-system/) (`RPC`) is a concept and, therefore, also a central tool to realize operational and work-sharing structures in networks and client-server architectures. The communication process via RPC includes passing parameters and the return of a function value. Learn how to perform [[RPC enumeration]]

An alternative to this would be a Python script from [Impacket](https://github.com/SecureAuthCorp/impacket) called [samrdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/samrdump.py).

#### *Impacket - Samrdump.py

```shell-session
akerashadow@htb[/htb]$ samrdump.py 10.129.14.128

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.129.14.128
Found domain(s):
 . DEVSMB
 . Builtin
[*] Looking up users in domain DEVSMB
Found user: mrb3n, uid = 1000
Found user: cry0l1t3, uid = 1001
mrb3n (1000)/FullName: 
mrb3n (1000)/UserComment: 
mrb3n (1000)/PrimaryGroupId: 513
mrb3n (1000)/BadPasswordCount: 0
mrb3n (1000)/LogonCount: 0
mrb3n (1000)/PasswordLastSet: 2021-09-22 17:47:59
mrb3n (1000)/PasswordDoesNotExpire: False
mrb3n (1000)/AccountIsDisabled: False
mrb3n (1000)/ScriptPath: 
cry0l1t3 (1001)/FullName: cry0l1t3
cry0l1t3 (1001)/UserComment: 
cry0l1t3 (1001)/PrimaryGroupId: 513
cry0l1t3 (1001)/BadPasswordCount: 0
cry0l1t3 (1001)/LogonCount: 0
cry0l1t3 (1001)/PasswordLastSet: 2021-09-22 17:50:56
cry0l1t3 (1001)/PasswordDoesNotExpire: False
cry0l1t3 (1001)/AccountIsDisabled: False
cry0l1t3 (1001)/ScriptPath: 
[*] Received 2 entries.
```

The information we have already obtained with `rpcclient` can also be obtained using other tools. For example, the [SMBMap](https://github.com/ShawnDEvans/smbmap) and [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) tools are also widely used and helpful for the enumeration of SMB services.

#### SMBmap

  SMB

```shell-session
akerashadow@htb[/htb]$ smbmap -H 10.129.14.128

[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        home                                                    NO ACCESS       INFREIGHT Samba
        dev                                                     NO ACCESS       DEVenv
        notes                                                   NO ACCESS       CheckIT
        IPC$                                                    NO ACCESS       IPC Service (DEVSM)
```

#### CrackMapExec

  SMB

```shell-session
akerashadow@htb[/htb]$ crackmapexec smb 10.129.14.128 --shares -u '' -p ''

SMB         10.129.14.128   445    DEVSMB           [*] Windows 6.1 Build 0 (name:DEVSMB) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.14.128   445    DEVSMB           [+] \: 
SMB         10.129.14.128   445    DEVSMB           [+] Enumerated shares
SMB         10.129.14.128   445    DEVSMB           Share           Permissions     Remark
SMB         10.129.14.128   445    DEVSMB           -----           -----------     ------
SMB         10.129.14.128   445    DEVSMB           print$                          Printer Drivers
SMB         10.129.14.128   445    DEVSMB           home                            INFREIGHT Samba
SMB         10.129.14.128   445    DEVSMB           dev                             DEVenv
SMB         10.129.14.128   445    DEVSMB           notes           READ,WRITE      CheckIT
SMB         10.129.14.128   445    DEVSMB           IPC$                            IPC Service (DEVSM)
```

Another tool worth mentioning is the so-called [enum4linux-ng](https://github.com/cddmp/enum4linux-ng), which is based on an older tool, enum4linux. This tool automates many of the queries, but not all, and can return a large amount of information.

## Enum4Linux-ng - Installation
---------------------------------------------

```shell-session
akerashadow@htb[/htb]$ git clone https://github.com/cddmp/enum4linux-ng.git
akerashadow@htb[/htb]$ cd enum4linux-ng
akerashadow@htb[/htb]$ pip3 install -r requirements.txt
```


. As the name suggests, it is a tool used for enumeration of Linux. To see all the options of this tool, just type “_**enum4linux -h**_“. Using this tool, first let us see the users of the SMB service. Open terminal and type command “_**enum4linux -U 192.168.25.129**_” as shown below.

![Enum4linux1](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux1.jpg)

As we can see above, this system is part of a workgroup. Know the [difference between domain and workgroup](http://www.dummies.com/how-to/content/network-basics-workgroups-versus-domains.html). We can see below that it has listed all the SMB users present on the target.

![Enum4linux2](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux2.jpg)

Of all the usernames the tool got us, I am assuming only three usernames are useful to us: user,root and msfadmin since others seem more like processes but we will keep our fingers crossed.

![Enum4linux3](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux3.jpg)

Before we check for validity of these credentials, let us perform a full enumeration with enum4linux. In the terminal type command “_**enum4linux 192.178.25.129**_” i.e without any options. As you can see below, it lists us Nbtstat information of what services are active on the target.

![SMB enumeration](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux5.jpg)

It also provides us with the OS information.

![Enum4linux6](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux6.jpg)

And crucial info about Shares, i.e which user has what rights on the target.

![Enum4linux7](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux7.jpg)

![Enum4linux8](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux8.jpg)

It provides us password policy info, in case we don’t get the credentials and want to crack them.

![Enum4linux9](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux9.jpg)

Groups present on the system.

![Enum4linux10](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux10.jpg)

It will also display users based on RID cycling.

![Enum4linux11](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux11.jpg)

It seems there are no printers connected to the target.

![Enum4linux12](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/enum4linux12.jpg)

Ok, now we know the users. Let’s try to find out the passwords for the usernames we seem to have got. We will use a tool called acccheck for this purpose. It is a password dictionary attack tool that targets [windows authentication](https://www.hackercoolmagazine.com/how-windows-authentication-works/) via the SMB protocol. We will see more about [password cracking](https://www.hackercoolmagazine.com/password-cracking-for-beginners/) later. First I will try it with the user “user”. In Kali Linux, most of the password dictionaries are present in “usr/share/dirb” directory. So I specify a dictionary which consists of most common passwords used.

Here, I am just guessing that the user may be using a common password. After specifying all the options, Hit Enter. The cracking process starts as shown below.

![Acccheck1](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/acccheck1.jpg)

![Acccheck2](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/acccheck2.jpg)

Once the tool gets the correct password, it stops the scan and displays a success message as shown below. Voila … the password of the user “user” is “user” only.

![Acccheck2](https://www.hackercoolmagazine.com/wp-content/uploads/2016/07/acccheck2.jpg)

#### Enum4Linux-ng - Enumeration
```shell-session
akerashadow@htb[/htb]$ ./enum4linux-ng.py 10.129.14.128 -A

ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.14.128
[*] Username ......... ''
[*] Random Username .. 'juzgtcsu'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Service Scan on 10.129.14.128    |
 =====================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    NetBIOS Names and Workgroup for 10.129.14.128    |
 =====================================================
[+] Got domain/workgroup name: DEVOPS
[+] Full NetBIOS names information:
- DEVSMB          <00> -         H <ACTIVE>  Workstation Service
- DEVSMB          <03> -         H <ACTIVE>  Messenger Service
- DEVSMB          <20> -         H <ACTIVE>  File Server Service
- ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE>  Master Browser
- DEVOPS          <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
- DEVOPS          <1d> -         H <ACTIVE>  Master Browser
- DEVOPS          <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
- MAC Address = 00-00-00-00-00-00

 ==========================================
|    SMB Dialect Check on 10.129.14.128    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
SMB 1.0: false
SMB 2.02: true
SMB 2.1: true
SMB 3.0: true
SMB1 only: false
Preferred dialect: SMB 3.0
SMB signing required: false

 ==========================================
|    RPC Session Check on 10.129.14.128    |
 ==========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user session
[+] Server allows session using username 'juzgtcsu', password ''
[H] Rerunning enumeration with user 'juzgtcsu' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.14.128    |
 ====================================================
[+] Domain: DEVOPS
[+] SID: NULL SID
[+] Host is part of a workgroup (not a domain)

 ============================================================
|    Domain Information via SMB session for 10.129.14.128    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DEVSMB
NetBIOS domain name: ''
DNS domain: ''
FQDN: htb

 ================================================
|    OS Information via RPC for 10.129.14.128    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Windows 7, Windows Server 2008 R2
OS version: '6.1'
OS release: ''
OS build: '0'
Native OS: not supported
Native LAN manager: not supported
Platform id: '500'
Server type: '0x809a03'
Server type string: Wk Sv PrQ Unx NT SNT DEVSM

 ======================================
|    Users via RPC on 10.129.14.128    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[+] Found 2 users via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 2 users via 'enumdomusers'
[+] After merging user results we have 2 users total:
'1000':
  username: mrb3n
  name: ''
  acb: '0x00000010'
  description: ''
'1001':
  username: cry0l1t3
  name: cry0l1t3
  acb: '0x00000014'
  description: ''

 =======================================
|    Groups via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating local groups
[+] Found 0 group(s) via 'enumalsgroups domain'
[*] Enumerating builtin groups
[+] Found 0 group(s) via 'enumalsgroups builtin'
[*] Enumerating domain groups
[+] Found 0 group(s) via 'enumdomgroups'

 =======================================
|    Shares via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating shares
[+] Found 5 share(s):
IPC$:
  comment: IPC Service (DEVSM)
  type: IPC
dev:
  comment: DEVenv
  type: Disk
home:
  comment: INFREIGHT Samba
  type: Disk
notes:
  comment: CheckIT
  type: Disk
print$:
  comment: Printer Drivers
  type: Disk
[*] Testing share IPC$
[-] Could not check share: STATUS_OBJECT_NAME_NOT_FOUND
[*] Testing share dev
[-] Share doesn't exist
[*] Testing share home
[+] Mapping: OK, Listing: OK
[*] Testing share notes
[+] Mapping: OK, Listing: OK
[*] Testing share print$
[+] Mapping: DENIED, Listing: N/A

 ==========================================
|    Policies via RPC for 10.129.14.128    |
 ==========================================
[*] Trying port 445/tcp
[+] Found policy:
domain_password_information:
  pw_history_length: None
  min_pw_length: 5
  min_pw_age: none
  max_pw_age: 49710 days 6 hours 21 minutes
  pw_properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
domain_lockout_information:
  lockout_observation_window: 30 minutes
  lockout_duration: 30 minutes
  lockout_threshold: None
domain_logoff_information:
  force_logoff_time: 49710 days 6 hours 21 minutes

 ==========================================
|    Printers via RPC for 10.129.14.128    |
 ==========================================
[+] No printers returned (this is not an error)

Completed after 0.61 seconds
```

We need to use more than two tools for enumeration. Because it can happen that due to the programming of the tools, we get different information that we have to check manually. Therefore, we should never rely only on automated tools where we do not know precisely how they were written.

##### __For more;
----------
[SMB enumeration](https://www.hackercoolmagazine.com/smb-enumeration-with-kali-linux-enum4linuxacccheck-smbmap/)
