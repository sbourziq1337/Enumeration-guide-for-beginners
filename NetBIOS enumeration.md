#### What is NetBIOS service?
---------------------------------

NetBIOS stands for Network Basic Input/Output System. It is used to allow applications and programs on separate computers to communicate with each other and establish sessions. This can be used to access shared resources.

NetBIOS can be used to provide three distinct services. They are NetBIOS Name Service, (NetBIOS-NS), NetBIOS Datagram distribution services (NetBIOS-DGM) and NetBIOS Session service (NetBIOS-SSN). These services run on ports 137,138 and 139 respectively.

**_What information does NetBIOS enumeration reveal?

It can provide information such as list of computers belonging to a particular domain, list of shares on target systems and sometimes even policies and passwords etc.

**_How to perform NetBIOS enumeration?

There are many tools that can be used to perform NetBIOS enumeration. Let’s see some of them.

**__1. nmblookup

The nmblookup command in Linux allows users to query NetBIOS names and maps them to IP addresses in a network using NetBIOS over TCP/IP queries.

[![netbios_enumeration_1](https://www.hackercoolmagazine.com/wp-content/uploads/2023/09/NetBIOS_Enumeration_1.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/09/NetBIOS_Enumeration_1.jpg)

__**2. NBTscan

NBTscan is a program that is used to scan IP networks for NetBIOS name information. It works by sending a NetBIOS status query to target system and lists received information in human readable form.

[![NetBIOS Enumeration 2](https://www.hackercoolmagazine.com/wp-content/uploads/2023/09/NetBIOS_Enumeration_2.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/09/NetBIOS_Enumeration_2.jpg)

[![NetBIOS Enumeration 34](https://www.hackercoolmagazine.com/wp-content/uploads/2023/09/NetBIOS_Enumeration_34.jpg)

### *__Additional Resources__

* [# SMB & NetBIOS Enumeration and Exploitation](https://medium.com/@yashpawar1199/smb-netbios-enumeration-and-exploitation-a-practical-guide-da30a224c8a2)
* [# NetBIOS Pentesting Best Practices](https://secybr.com/posts/netbios-pentesting-best-practicies/)


