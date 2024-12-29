
## ****What is Simple Mail Transfer Protocol?****

SMTP is an [application layer protocol](https://www.geeksforgeeks.org/protocols-application-layer/). The client who wants to send the mail opens a TCP connection to the SMTP server and then sends the mail across the connection. The SMTP server is an always-on listening mode. As soon as it listens for a TCP connection from any client, the SMTP process initiates a connection through port 25. After successfully establishing a TCP connection the client process sends the mail instantly. 

![Simple Mail Transfer Protocol](https://media.geeksforgeeks.org/wp-content/uploads/20240226095034/SMTP-Gif.gif)

## Default Configuration

Each SMTP server can be configured in many ways, as can all other services. However, there are differences because the SMTP server is only responsible for sending and forwarding emails.

#### Default Configuration

  SMTP

```shell-session
akerashadow@htb[/htb]$ cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```
## For more;
-------------------------------
[# How To Install and Configure Postfix as a Send-Only SMTP Server on linux](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-18-04)


As the term “simple” implies, SMTP server can only understand simple text commands. Sender of the mail communicates with a mail receiver by issuing these command strings and supplying necessary data. Some of the important commands are

| **Command**  | **Description**                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------ |
| `AUTH PLAIN` | AUTH is a service extension used to authenticate the client.                                     |
| `HELO`       | The client logs in with its computer name and thus starts the session.                           |
| `MAIL FROM`  | The client names the email sender.                                                               |
| `RCPT TO`    | The client names the email recipient.                                                            |
| `DATA`       | The client initiates the transmission of the email.                                              |
| `RSET`       | The client aborts the initiated transmission but keeps the connection between client and server. |
| `VRFY`       | The client checks if a mailbox is available for message transfer.                                |
| `EXPN`       | The client also checks if a mailbox is available for messaging with this command.                |
| `NOOP`       | The client requests a response from the server to prevent disconnection due to time-out.         |
| `QUIT`       | The client terminates the session.                                                               |
[send email ](https://www.alore.io/blog/smtp-send-mail)
To interact with the SMTP server, we can use the `telnet` tool to initialize a TCP connection with the SMTP server. The actual initialization of the session is done with the command mentioned above, `HELO` or `EHLO`.
#### Telnet - HELO/EHLO
-------------------------------------
```shell-session
akerashadow@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 


HELO mail1.inlanefreight.htb

250 mail1.inlanefreight.htb


EHLO mail1

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING
```

The command `VRFY` can be used to enumerate existing users on the system. However, this does not always work. Depending on how the SMTP server is configured, the SMTP server may issue `code 252` and confirm the existence of a user that does not exist on the system. A list of all SMTP response codes can be found [here](https://serversmtp.com/smtp-error/).
#### Telnet - VRFY
--------------------------------------
```shell-session
akerashadow@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root

252 2.0.0 root


VRFY cry0l1t3

252 2.0.0 cry0l1t3


VRFY testuser

252 2.0.0 testuser


VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa

252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

Therefore, one should never entirely rely on the results of automatic tools. After all, they execute pre-configured commands, but none of the functions explicitly state how the administrator configures the tested server.


The default Nmap scripts include `smtp-commands`, which uses the `EHLO` command to list all possible commands that can be executed on the target SMTP server.

. Nmap also has a script to perform SMTP enumeration. We can use the script as shown below.

![Smtpenum3](https://www.hackercoolmagazine.com/wp-content/uploads/2017/06/smtpenum3.jpg)

By default, [Nmap](https://www.hackercoolmagazine.com/complete-guide-to-nmap-port-scanner/) uses RCPT method to check if a particular user exists. Unfortunately for me, it gave unhandled status code here. This Nmap script can be modified to use different methods. Here I changed it to use VRFY method to enumerate users. I have only scanned port 25 to remove the clutter. But still it gave me the same error.

![Smtpenum4](https://www.hackercoolmagazine.com/wp-content/uploads/2017/06/smtpenum4.jpg)

There is another tool in the arsenal of Kali Linux which is built specifically for SMTP enumeration. Its called smtp-user-enum. Here let us test if a user called “root” exists on the target system as shown below.

![Smtpenum5](https://www.hackercoolmagazine.com/wp-content/uploads/2017/06/smtpenum5.jpg)


## For more;
-----------------------
[# SMTP Enumeration Technique](https://hamzamhirsi.medium.com/smtp-enumeration-technique-20c7aab1887a)
[# Understand, Enumerate, and Exploit SMTP with THM example](https://medium.com/@tahirgozuacik/understand-enumerate-and-exploit-smtp-with-thm-example-34e3eb3806b6)
[comand smtp-user-enum](https://github.com/cytopia/smtp-user-enum?tab=readme-ov-file#tada-installation)
