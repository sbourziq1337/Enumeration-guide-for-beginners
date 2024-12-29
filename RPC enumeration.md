# What is Remote Procedure Call (RPC)?
-------------------------------------------------------------
* ***RPC** is a technique that allows a program to execute a procedure (a block of code) in another address space (usually on a remote server) as if it were a **local procedure call**. It abstracts away the complexities of network communication and enables developers to write distributed applications without worrying too much about the underlying networking details.

## *What is a Remote Procedure?
--------------------------------------------

* A **remote procedure** is a block of code or function located in a different address space or on a remote system that can be invoked and executed by a program or process residing elsewhere, often referred to as the **client**.

## *Key Characteristics of a Remote Procedure:
------------------------------------------------------

1. **Located on a Remote System:** A remote procedure resides on a different machine, address space, or server compared to the client that initiates its execution.
2. **Callable by Remote Clients:** Clients can invoke and execute this procedure from a remote location as if it were a local function or method call.
3. **Abstracted Communication:** The communication between the client and the remote procedure is abstracted, allowing the client to interact with the remote procedure without needing to handle the low-level networking details.
4. **Transparent Invocation:** The client calling the remote procedure typically uses programming language constructs that make the call appear similar to a local procedure call, even though it’s executed remotely.

## *Key Components of RPC
--------------------------------------------

1. **Client:** The program or process that initiates the RPC by requesting a service from a remote server.
2. **Server:** The program or process that provides the requested service. It listens for incoming RPC requests and executes the corresponding procedures.
3. **Stub:** Also known as a **client proxy**, it is a local representation of the remote service. The client interacts with the stub as if it were the actual service, and the stub handles the communication details.
4. **Skeleton:** Also known as a **server proxy**, it represents the server’s interface to the client. It receives incoming RPC requests, unpacks the parameters, and invokes the appropriate procedures on the server.
5. **Communication Protocol:** Defines the rules and formats for exchanging messages between the client and server. Protocols like HTTP, TCP/IP, and gRPC are commonly used for RPC.

## *How RPC Works
-----------------------------------------------
When a client invokes a remote procedure, the following steps typically occur:

1. **Client Stub Invocation:** The client calls a procedure that appears local but is actually a stub representing the remote procedure. The stub prepares an RPC request, including information about the procedure to execute and its parameters.
2. **Marshalling:** The parameters and method information are serialized (converted into a format suitable for transmission) to be sent over the network.
3. **Communication:** The client sends the RPC request to the server using a chosen communication protocol.
4. **Server Skeleton Processing:** The server’s skeleton receives the request, unpacks the data (demarshalling), and determines the requested procedure.
5. **Local Procedure Execution:** The server executes the requested procedure using the provided parameters.
6. **Response Preparation:** The server marshals the response (if any) into a format for transmission.
7. **Response Transmission:** The server sends the response back to the client.
8. **Unmarshalling:** The client stub receives the response, demarshals it, and returns the result to the client as if it were a local procedure call.
## **Example Scenario

Imagine a scenario where a client program needs to calculate the square of a number using a remote procedure hosted on a server:

1. **Client Request:** The client sends a request to the server, asking it to calculate the square of a particular number, say 5.
2. **Remote Procedure Execution:** The server receives the request, locates the “**calculate_square**” procedure, performs the calculation (25 in this case), and prepares a response.
3. **Response Transmission:** The server sends back the response (25) to the client, which requested the square calculation.
4. **Client Retrieval:** The client receives the response and can continue its operation using the result obtained from the remote procedure execution.

## *RPCclient
--------------------------------

```shell-session
akerashadow@htb[/htb]$ rpcclient -U "" 10.129.14.128

Enter WORKGROUP\'s password:
rpcclient $> 
```

The `rpcclient` offers us many different requests with which we can execute specific functions on the SMB server to get information. A complete list of all these functions can be found on the [man page](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) of the rpcclient
## *RPCclient - Enumeration
----------------------------------------

If you successfully exploited vulnerabilities and obtained a username and password, you can create an RPC bridge as follows using `rpcclient`

```
rpcclient -U "username%password" <target-ip>
```

### Service Abuse
-------------------------------------
If a certain RPC service created a vulnerability on the target machine, you can abuse this service to influence behaviors on the target system or hinder its proper operation. For example, you can stop the following RPC service:

```
rpcclient -U "username%password" <target-ip> -c 'stop service_name'
```

| **Query**                 | **Description**                                                    |
| ------------------------- | ------------------------------------------------------------------ |
| `srvinfo`                 | Server information.                                                |
| `enumdomains`             | Enumerate all domains that are deployed in the network.            |
| `querydominfo`            | Provides domain, server, and user information of deployed domains. |
| `netshareenumall`         | Enumerates all available shares.                                   |
| `netsharegetinfo <share>` | Provides information about a specific share.                       |
| `enumdomusers`            | Enumerates all domain users.                                       |
| `queryuser <RID>`         | Provides information about a specific user.                        |
|                           |                                                                    |
#### RPCclient - Enumeration
-----------------------------------------
```shell-session
rpcclient $> srvinfo

        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
		
		
rpcclient $> enumdomains

name:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]


rpcclient $> querydominfo

Domain:         DEVOPS
Server:         DEVSMB
Comment:        DEVSM
Total Users:    2
Total Groups:   0
Total Aliases:  0
Sequence No:    1632361158
Force Logoff:   -1
Domain Server State:    0x1
Server Role:    ROLE_DOMAIN_PDC
Unknown 3:      0x1


rpcclient $> netshareenumall

netname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: home
        remark: INFREIGHT Samba
        path:   C:\home\
        password:
netname: dev
        remark: DEVenv
        path:   C:\home\sambauser\dev\
        password:
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
netname: IPC$
        remark: IPC Service (DEVSM)
        path:   C:\tmp
        password:
		
		
rpcclient $> netsharegetinfo notes

netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
        type:   0x0
        perms:  0
        max_uses:       -1
        num_uses:       1
revision: 1
type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE 
DACL
        ACL     Num ACEs:       1       revision:       2
        ---
        ACE
                type: ACCESS ALLOWED (0) flags: 0x00 
                Specific bits: 0x1ff
                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
                SID: S-1-1-0
```

These examples show us what information can be leaked to anonymous users. Once an `anonymous` user has access to a network service, it only takes one mistake to give them too many permissions or too much visibility to put the entire network at significant risk.

Most importantly, anonymous access to such services can also lead to the discovery of other users, who can be attacked with brute-forcing in the most aggressive case. Humans are more error-prone than properly configured computer processes, and the lack of security awareness and laziness often leads to weak passwords that can be easily cracked. Let us see how we can enumerate users using the `rpcclient`.

### *Rpcclient - User Enumeration
----------------------------------------------------------

```shell-session
rpcclient $> enumdomusers

user:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]


rpcclient $> queryuser 0x3e9

        User Name   :   cry0l1t3
        Full Name   :   cry0l1t3
        Home Drive  :   \\devsmb\cry0l1t3
        Dir Drive   :
        Profile Path:   \\devsmb\cry0l1t3\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:50:56 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:50:56 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e9
        group_rid:      0x201
        acb_info :      0x00000014
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...


rpcclient $> queryuser 0x3e8

        User Name   :   mrb3n
        Full Name   :
        Home Drive  :   \\devsmb\mrb3n
        Dir Drive   :
        Profile Path:   \\devsmb\mrb3n\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:47:59 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:47:59 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e8
        group_rid:      0x201
        acb_info :      0x00000010
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...
```

We can then use the results to identify the group's RID, which we can then use to retrieve information from the entire group.

### *Rpcclient - Group Information
-------------------------------

```shell-session
rpcclient $> querygroup 0x201

        Group Name:     None
        Description:    Ordinary Users
        Group Attribute:7
        Num Members:2
```

However, it can also happen that not all commands are available to us, and we have certain restrictions based on the user. However, the query `queryuser <RID>` is mostly allowed based on the RID. So we can use the rpcclient to brute force the RIDs to get information. Because we may not know who has been assigned which RID, we know that we will get information about it as soon as we query an assigned RID. There are several ways and tools we can use for this. To stay with the tool, we can create a `For-loop` using `Bash` where we send a command to the service using rpcclient and filter out the results.

#### Brute Forcing User RIDs
-------------------------

```shell-session
akerashadow@htb[/htb]$ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

        User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201
		
        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201
		
        User Name   :   cry0l1t3
        user_rid :      0x3e9
        group_rid:      0x201
```

For more;
[RPC](https://www.sans.org/blog/plundering-windows-account-info-via-authenticated-smb-sessions/)
[Rpcbind](https://hackviser.com/tactics/pentesting/services/rpcbind)


