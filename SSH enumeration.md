## What is SSH?
------------------

[![Untitled](https://secybr.com/assets/img/pitcures/ssh/ssh.png)](https://secybr.com/assets/img/pitcures/ssh/ssh.png)

SSH or Secure Shell is a network communication protocol that enables two computers to communicate (c.f http or hypertext transfer protocol, which is the protocol used to transfer hypertext such as web pages) and share data. An inherent feature of ssh is that the communication between the two computers is encrypted meaning that it is suitable for use on insecure networks.

```
PORT     STATE SERVICE               VERSION
22/tcp   open  ssh                   OpenSSH4.7p1 Debian 8ubuntu1 (protocol 2.0)
```

The protocol works in the client-server model, which means that the connection is established by the SSH client connecting to the SSH server. The SSH client drives the connection setup process and uses public key cryptography to verify the identity of the SSH server. After the setup phase the SSH protocol uses strong symmetric encryption and hashing algorithms to ensure the privacy and integrity of the data that is exchanged between the client and server.

The figure below presents a simplified setup flow of a secure shell connection.

[![client- ssh-server connection](https://secybr.com/assets/img/pitcures/ssh/ssh1.png)

## *Default Configuration
----------------------------
* The [sshd_config](https://www.ssh.com/academy/ssh/sshd_config) file, responsible for the OpenSSH server, has only a few of the settings configured by default. However, the default configuration includes X11 forwarding, which contained a command injection vulnerability in version 7.2p1 of OpenSSH in 2016. Nevertheless, we do not need a GUI to manage our servers.
* [# How To Configure SSH Key-Based Authentication on a Linux Server](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server)

## Enumeration
---------------------
### *Identifying an FTP Server

You can use `Nmap` to check if there's an SSH server on a target host like this:

```
nmap -p 22 X.X.X.X
```

### *Banner Grabbing

You can use `Netcat` to find out what service is running and its version by looking at the welcome message it shows when you connect. This method is called Banner Grabbing.

```
nc -vn X.X.X.X 22
```

### *Automated audit with ssh-audit

"ssh-audit" is a tool for analyzing SSH connections, providing details on banners, OS/software recognition, compression detection, algorithm information and security recommendations.

```
ssh-audit X.X.X.X 22
```

 [ssh-audit](https://github.com/jtesta/ssh-audit). It checks the client-side and server-side configuration and shows some general information and which encryption algorithms are still used by the client and server. Of course, this could be exploited by attacking the server or client at the cryptic level later.

  Linux Remote Management Protocols
  
```shell-session
akerashadow@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
akerashadow@htb[/htb]$ ./ssh-audit.py 10.129.14.132

# general
(gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
(gen) software: OpenSSH 8.2p1
(gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
(gen) compression: enabled (zlib@openssh.com)                                   

# key exchange algorithms
(kex) curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76                            
(kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp384                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp521                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) diffie-hellman-group-exchange-sha256 (2048-bit) -- [info] available since OpenSSH 4.4
(kex) diffie-hellman-group16-sha512         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
(kex) diffie-hellman-group18-sha512         -- [info] available since OpenSSH 7.3
(kex) diffie-hellman-group14-sha256         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73

# host-key algorithms
(key) rsa-sha2-512 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) rsa-sha2-256 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) ssh-rsa (3072-bit)                    -- [fail] using weak hashing algorithm
                                            `- [info] available since OpenSSH 2.5.0, Dropbear SSH 0.28
                                            `- [info] a future deprecation notice has been issued in OpenSSH 8.2: https://www.openssh.com/txt/release-8.2
(key) ecdsa-sha2-nistp256                   -- [fail] using weak elliptic curves
                                            `- [warn] using weak random number generator could reveal the key
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(key) ssh-ed25519                           -- [info] available since OpenSSH 6.5
...SNIP...
```
### Identify Authentication Methods with Nmap

`ssh-auth-methods` is an Nmap script used to identify the authentication methods supported by an SSH server.

```
nmap --script ssh-auth-methods --script-args="ssh.user=username" -p 22 X.X.X.X
```
### SSH Connection Tools
-------------------------------
Linux & Mac
```
1. ssh username@10.10.x.x #If ssh is running on another port, the -p parameter should be used.
2. #IPv6:
3. ssh username@fe80::b0ec:afd2:a932:252c%9
```

Windows:

- Putty
- Filezilla
- Winscp
### *SSH Brute Forcing
----------------------
```
1. #Metasploit ssh brute force
2. msf > use auxiliary/scanner/ssh/ssh_login
3. 
4. #Metasploit username enumeration with brute force
5. use auxiliary/scanner/ssh/ssh_enumusers
6. 
7. #Nmap Brute Force
8. nmap -p 22 --script ssh-brute --script-args userdb=users.list,passdb=pass.list \--script-args ssh-brute.timeout=4s 10.10.x.x
9. 
10. #Other Brute Forcing Method
11. hydra -L users.txt -P passwords.txt ssh://10.10.x.x -t 8
12. medusa -t 2 -T2 -U /root/Desktop/user.txt -P /root/Desktop/pass.txt -H /root/Desktop/host.txt -n 22 -M ssh
13. ncrack -g CL=2 -U users.list -P passwords.list -iL targetIPs.list -p ssh:22 -oA Results.txt
14. patator ssh_login host=10.10.x.x user=FILE0 password=FILE1 0=users.list 1=passwords.list persistent=0 -x ignore:mesg=’Authentication failed.’
15. /guess-who -l username -h 10.10.x.x -p 22 -2 < passwords.list (https://packetstormsecurity.com/groups/teso/guess-who-0.44.tgz)

```
### List of some static Host and Authentication keys
---------------------------------------------------------

[GitHub - rapid7/ssh-badkeys: A collection of static SSH keys (public and private) that have made their way into software and hardware products.](https://github.com/rapid7/ssh-badkeys)  
_GitHub Repo for static Host and Authentication keys_
## *Rsync
----------
[Rsync](https://linux.die.net/man/1/rsync) is a fast and efficient tool for locally and remotely copying files. It can be used to copy files locally on a given machine and to/from remote hosts. It is highly versatile and well-known for its delta-transfer algorithm. This algorithm reduces the amount of data transmitted over the network when a version of the file already exists on the destination host. It does this by sending only the differences between the source files and the older version of the files that reside on the destination server. It is often used for backups and mirroring. It finds files that need to be transferred by looking at files that have changed in size or the last modified time. By default, it uses port `873` and can be configured to use SSH for secure file transfers by piggybacking on top of an established SSH server connection.

owever, the misconfiguration of this utility can lead to an easy foothold for attackers to compromise the service. An attacker can look for weak configurations like anonymous access and write permissions to gain unauthorized access to the running server.

That’s what we are going to do today 😈. We will take advantage of an insecure **Rsync** service and obtain a shell with SSH. Learn how to perform [[Rsync enumeration]].
[How To Copy Files With Rsync Over SSH](https://www.digitalocean.com/community/tutorials/how-to-copy-files-with-rsync-over-ssh)
### Running a command over SSH with a key
---------------------------------------------

```
ssh -o PasswordAuthentication=no -o BatchMode=yes -v -p 22 -i key -l username 10.10.x.x 'command'
```

### *__Additional Resources__
 * [# SSH Penetration Testing](https://medium.com/@oumasydney2000/ssh-penetration-testing-cd6570335743)
* [# Hacking the SSH server](https://www.hackercoolmagazine.com/hacking-the-ssh-server-again/)

> **_Thanks for Reading!_**
> * [ LinkedIn](https://www.linkedin.com/in/soufiane-bourziq-391213266/)