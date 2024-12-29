### __*What is Enumeration?__
----------------------------------

In Enumeration, an attacker or a Pen Tester performs calculated queries to gather more detailed information about the target. Usually, enumeration is performed on the services running on the target (open ports) with the purpose of **gaining access** to the target system.

### __What information does enumeration reveal?__
-------------------------------------------------------------

Enumeration can reveal valuable information like Network shares, usernames and passwords, version of the application running, users and groups, machine names, service settings and other network resources

### __Which services can be enumerated?__
------------------------------------------

![Tux, the Linux mascot](https://github.com/sbourziq1337/Enumeration-guide-for-beginners/blob/main/map_protcol.png)

Although all services running on the target system can be enumerated upon, there are some specific services which are regularly enumerated to retrieve useful information

#### *Let’s learn about each of these services in detail
--------------------------------------------------

1. *__FTP__

	* The **`File Transfer Protocol` (`FTP`)** is one of the oldest protocols on the Internet. The FTP runs within the application layer of the TCP/IP protocol stack. Thus, it is on the same layer as `HTTP` or `POP`. These protocols also work with the support of browsers or email clients to perform their services. There are also special FTP programs for the File Transfer Protocol.   Learn how to perform [FTP Enumeration](https://github.com/sbourziq1337/Enumeration-guide-for-beginners/blob/main/FTP%20Enumeration.md)

2. *__SMB__

	* Just like NetBIOS, **Server Message Block (SMB)** is a protocol that allows applications and computers in a local network talk to each other. The only difference between them is that NetBIOS is an API whereas SMB is a protocol. Starting from Windows 2000, SMB which earlier ran on top of NetBIOS was made to operate on top of TCP and it got a dedicated port 445.
	* It also enables network services like file, printer and device sharing. Enumerating SMB service can reveal information like host names, lists shares, checking for null session, users, operating system details, password policies, info groups and printers connected etc.  Learn how to perform [[SMB enumeration]]

3. *__NetBIOS__

	* **NetBIOS** service allows programs and computers on a local area network to communicate with each other. These include services like files, printers and device shares. Enumerating NetBIOS can reveal information like list of computers in a specific domain, lists of shares, policies and Passwords etc. Learn how to perform [NetBIOS enumeration](https://www.hackercoolmagazine.com/netbios-enumeration-for-beginners/).

4. *__NFS__
 
	* **NFS** stands for “Network File System” and allows a system to share directories and files with others over a network. By using NFS, users and programs can access files on remote systems almost as if they were local files. It does this by mounting all, or a portion of a file system on a server. The portion of the file system that is mounted can be accessed by clients with whatever privileges are assigned to each file. Learn how to perform [[NFS enumetation]]

5. __*DNS__

	* The Domain Name System **(DNS)** is a hierarchical and decentralized naming system for computers, services, and other resources connected to a network. It associates different information with domain names assigned to each of the participating entities. Most prominently, it translates more easily memorized domain names to the numerical IP addresses needed for identifying computer services and devices with the underlying network protocols. Learn how to perform [[DNS enumeration]]

6. *__SMTP__

	* **Simple Mail Transfer Protocol (SMTP)** is a TCP/IP protocol that is used to send email. It is mostly used by email clients but most of the organizations have their own Email Servers to send mail. Enumerating SMTP Service can reveal the list of valid users on the SMTP Servers. Learn how to perform [[SMTP enumeration]].

7. *__IMAP/ POP3__

	1. **IMAP** is an application-layer protocol used by email clients to retrieve messages from a mail server. It was designed to manage multiple email clients, therefore clients generally leave messages on the server until the user explicitly deletes them.
	
	2.  **POP, or POP3 (POP version 3)**, is an application-layer protocol used by email clients to retrieve messages from a mail server. It provides access via IP to mailboxes maintained on a server.
		
		Because POP was designed for temporary Internet connection, clients connect, retrieve messages, store them on the client, and finally delete them from the server. Clients also have the option to leave messages on the server. By contrast, IMAP was designed to normally leave all messages on the server allowing multiple client applications as online and offline modes

	* Learn how to perform [[IMAP-POP3 enumeration]]
	
8. *__SNMP__

	* **Simple Network Management Protocol (SNMP)** is a protocol that is used to monitor and manage computer systems in the same network. Enumerating SNMP can reveal information about network resources like hosts, routes, shares, ARP tables, routing tables, etc. Learn how to perform SNMP enumeration. Learn how to perform [[SNMP enumeration]]

9. __*MySQL

	* ***MySQL** is an open-source relational database management system (**RBDMS**) that uses structured query language (**SQL**) for accessing and managing data. It is one of the most common widely used RDBMS in the world. Learn how to perform [[MySQL enumeration]]

10. __*MSSQL

	* **MS SQL** Server is a relational database management system **(RDBMS)** developed by Microsoft. This product is built for the basic function of storing retrieving data as required by other applications. It can be run either on the same computer or on another across a network. This tutorial explains some basic and advanced concepts of SQL Server such as how to create and restore data, create login and backup, assign permissions, etc. Each topic is explained using examples for easy understanding.
	* Learn how to perform [[MSSQL enumeration]]

11. __*SSH

	* ***Secure Shell (SSH)** is a protocol used to securely connect to another computer over a network. It allows you to log into another computer, execute commands, and transfer files, all in a secure manner. This is because SSH encrypts your connection, making it difficult for hackers to intercept and understand the data being exchanged. Learn how to perform [[SSH enumeration]]

12. __*RDP 

	* Remote Desktop Protocol (RDP) is a proprietary protocol developed by Microsoft which provides a user with a graphical interface to connect to another computer over a network connection.

	* A user need to be part of the "Remote Desktop Users" in order to login to the host via RDP. learn how to [[RDP enumeration]]

#  *__Happy Hacking! 🎩💻
