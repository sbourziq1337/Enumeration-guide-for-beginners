# What is IMAP?

Internet Message Access Protocol (IMAP) is then used by the recipient’s email client to fetch your message from the email server and put it in their inbox.

[![Untitled](https://secybr.com/assets/img/pitcures/imap/imap.png)](https://secybr.com/assets/img/pitcures/imap/imap.png)

- **Port 143** - this is the default IMAP non-encrypted port
- **Port 993** - this is the port you need to use if you want to connect using IMAP securely
# What is POP3?

POP3 stands for Post Office Protocol. As the name suggests, it allows you to use your email inbox like a post office – emails are downloaded onto your computer and removed from the mail server.

When accessing your emails using the POP3 protocol, a copy of the emails is created and stored locally on your computer. The originals are usually, but not always, removed from the mail server. In other words, emails are tied to the specific device. Once the email is downloaded onto one device (and removed from the mail server), it cannot be accessed by another email client or device.

![POP3 Email Protocol](https://media.geeksforgeeks.org/wp-content/uploads/20230817212918/IMG-20230817-WA0019.jpg)

## Default Configuration
-----------------------------------

Both IMAP and POP3 have a large number of configuration options, making it difficult to deep dive into each component in more detail. If you wish to examine these protocol configurations deeper, we recommend creating a VM locally and install the two packages `dovecot-imapd`, and `dovecot-pop3d` using `apt` and play around with the configurations and experiment.

In the documentation of Dovecot, we can find the individual [core settings](https://doc.dovecot.org/settings/core/) and [service configuration](https://doc.dovecot.org/configuration_manual/service_configuration/) options that can be utilized for our experiments. However, let us look at the list of commands and see how we can directly interact and communicate with IMAP and POP3 using the command line.
#### IMAP Commands
---------------------------------

| **Command**                     | **Description**                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `1 LOGIN username password`     | User's login.                                                                                                 |
| `1 LIST "" *`                   | Lists all directories.                                                                                        |
| `1 CREATE "INBOX"`              | Creates a mailbox with a specified name.                                                                      |
| `1 DELETE "INBOX"`              | Deletes a mailbox.                                                                                            |
| `1 RENAME "ToRead" "Important"` | Renames a mailbox.                                                                                            |
| `1 LSUB "" *`                   | Returns a subset of names from the set of names that the User has declared as being `active` or `subscribed`. |
| `1 SELECT INBOX`                | Selects a mailbox so that messages in the mailbox can be accessed.                                            |
| `1 UNSELECT INBOX`              | Exits the selected mailbox.                                                                                   |
| `1 FETCH <ID> all`              | Retrieves data associated with a message in the mailbox.                                                      |
| `1 CLOSE`                       | Removes all messages with the `Deleted` flag set.                                                             |
| `1 LOGOUT`                      | Closes the connection with the IMAP server.                                                                   |
#### __For more ;
-----------------------
[IMAP Commands](https://www.atmail.com/blog/imap-commands/)
[Comands imap](https://donsutherland.org/crib/imap)
#### POP3 Commands
----------------------------

| **Command**     | **Description**                                             |
| --------------- | ----------------------------------------------------------- |
| `USER username` | Identifies the user.                                        |
| `PASS password` | Authentication of the user using its password.              |
| `STAT`          | Requests the number of saved emails from the server.        |
| `LIST`          | Requests from the server the number and size of all emails. |
| `RETR id`       | Requests the server to deliver the requested email by ID.   |
| `DELE id`       | Requests the server to delete the requested email by ID.    |
| `CAPA`          | Requests the server to display the server capabilities.     |
| `RSET`          | Requests the server to reset the transmitted information.   |
| `QUIT`          | Closes the connection with the POP3 server.                 |
# __For more comand:__
--------------------------------------

[Commands POP3](https://www.atmail.com/blog/pop-101-manual-pop-sessions/)
[Command pop3](https://www.suburbancomputer.com/tips_email.htm)

## Difference Between IMAP and POP3
---------------------------------------

| IMAP                                                                                                                         | POP3                                                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| IMAP (Internet Message Access Protocol) is much more advanced and allows the user to see all the folders on the mail server. | POP is a simple protocol that only allows downloading messages from your Inbox to your local computer.                                              |
| The IMAP server listens on port 143, and the IMAP with SSL secure(IMAPDS) server listens on port 993.                        | The POP server listens on port 110, and the POP with SSL secure(POP3DS) server listens on port 995                                                  |
| Messages can be accessed across multiple devices                                                                             | In [POP3](https://www.geeksforgeeks.org/what-is-pop3-post-office-protocol-version-3/) the mail can only be accessed from a single device at a time. |
| The mail content can be read partially before downloading.                                                                   | To read the mail it has to be downloaded on the local system.                                                                                       |
| The user can create, delete or rename an email on the mail server.                                                           | The user can not create, delete or rename email on the mail server.                                                                                 |
# __*Enumeration

#### *Nmap
-------

This Nmap scan reveals information about IMAP (Internet Message Access Protocol) and POP3 (Post Office Protocol 3) email services running on a target system

```shell-session
akerashadow@htb[/htb]$ sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 22:09 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00026s latency).

PORT    STATE SERVICE  VERSION
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE SASL STLS TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
143/tcp open  imap     Dovecot imapd
|_imap-capabilities: more have post-login STARTTLS Pre-login capabilities LITERAL+ LOGIN-REFERRALS OK LOGINDISABLEDA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
993/tcp open  ssl/imap Dovecot imapd
|_imap-capabilities: more have post-login OK capabilities LITERAL+ LOGIN-REFERRALS Pre-login AUTH=PLAINA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
995/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE USER SASL(PLAIN) TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.74 seconds
```

For example, from the output, we can see that the common name is `mail1.inlanefreight.htb`, and the email server belongs to the organization `Inlanefreight`,which is located in California. The displayed capabilities show us the commands available on the server and for the service on the corresponding port.

If we successfully figure out the access credentials for one of the employees, an attacker could log in to the mail server and read or even send the individual messages.

#### *cURL

```shell-session
akerashadow@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd

* LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." INBOX
```

If we also use the `verbose` (`-v`) option, we will see how the connection is made. From this, we can see the version of TLS used for encryption, further details of the SSL certificate, and even the banner, which will often contain the version of the mail server.

```shell-session
akerashadow@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v

*   Trying 10.129.14.128:993...
* TCP_NODELAY set
* Connected to 10.129.14.128 (10.129.14.128) port 993 (#0)
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* Server certificate:
*  subject: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  start date: Sep 19 19:44:58 2021 GMT
*  expire date: Jul  4 19:44:58 2295 GMT
*  issuer: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
< * OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4
> A001 CAPABILITY
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN
< A001 OK Pre-login capabilities listed, post-login capabilities have more.
> A002 AUTHENTICATE PLAIN AGNyeTBsMXQzADEyMzQ=
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE
< A002 OK Logged in
> A003 LIST "" *
< * LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." Important
< * LIST (\HasNoChildren) "." INBOX
* LIST (\HasNoChildren) "." INBOX
< A003 OK List completed (0.001 + 0.000 secs).
* Connection #0 to host 10.129.14.128 left intact
```

#### __CURL

Basic navigation is possible with [CURL](https://ec.haxx.se/usingcurl/usingcurl-reademail#imap), but the documentation is light on details so checking the [source](https://github.com/curl/curl/blob/master/lib/imap.c) is recommended for precise details.

1. *Listing mailboxes (imap command `LIST "" "*"`)
	
```
curl -k 'imaps://1.2.3.4/' --user user:pass
```

2. *Listing messages in a mailbox (imap command `SELECT INBOX` and then `SEARCH ALL`)
    
```
curl -k 'imaps://1.2.3.4/INBOX?ALL' --user user:pass
```

* The result of this search is a list of message indicies.
* Its also possible to provide more complex search terms. e.g. searching for drafts with password in mail body:

```
curl -k 'imaps://1.2.3.4/Drafts?TEXT password' --user user:pass
```

* A nice overview of the search terms possible is located [here](https://www.atmail.com/blog/imap-commands/).

3. *Downloading a message (imap command `SELECT Drafts` and then `FETCH 1 BODY[]`)

```
curl -k 'imaps://1.2.3.4/Drafts;MAILINDEX=1' --user user:pass
```

* The mail index will be the same index returned from the search operation.

* It is also possible to use `UID` (unique id) to access messages, however it is less conveniant as the search command needs to be manually formatted. E.g.

```
curl -k 'imaps://1.2.3.4/INBOX' -X 'UID SEARCH ALL' --user user:pass
curl -k 'imaps://1.2.3.4/INBOX;UID=1' --user user:pass
```

* Also, possible to download just parts of a message, e.g. subject and sender of first 5 messages (the `-v` is required to see the subject and sender):

```
$ curl -k 'imaps://1.2.3.4/INBOX' -X 'FETCH 1:5 BODY[HEADER.FIELDS (SUBJECT FROM)]' --user user:pass -v 2>&1 | grep '^<'
```

* Although, its probably cleaner to just write a little for loop:

```
for m in {1..5}; do
  echo $m
  curl "imap://1.2.3.4/INBOX;MAILINDEX=$m;SECTION=HEADER.FIELDS%20(SUBJECT%20FROM)" --user user:pass
done
```

To interact with the IMAP or POP3 server over SSL, we can use `openssl`, as well as `ncat`. The commands for this would look like this:

### __*Using OpenSSL

OpenSSL provides a powerful tool called `s_client` that allows you to connect to an IMAP server securely over SSL/TLS. Here's how to use it:

1. __*Connect to the IMAP server:
```
openssl s_client -connect imap.example.com:993 -crlf -quiet
```

- Replace `imap.example.com` with your actual IMAP server address.
- Port 993 is typically used for IMAPS (IMAP over SSL).

2. __*Authenticate:
	
	After connecting, you'll need to authenticate. There are several methods:
	a. Login method:
	
```
	a login username password

```
#### *OpenSSL - TLS Encrypted Interaction POP3

```shell-session
 openssl s_client -connect 10.129.14.128:pop3s
```
#### *OpenSSL - TLS Encrypted Interaction IMAP

```shell-session
openssl s_client -connect 10.129.14.128:imaps
```
## __*Syntax__

IMAP Commands examples from [here](https://donsutherland.org/crib/imap):

```
Login
    A1 LOGIN username password
Values can be quoted to enclose spaces and special characters. A " must then be escape with a \
    A1 LOGIN "username" "password"

List Folders/Mailboxes
    A1 LIST "" *
    A1 LIST INBOX *
    A1 LIST "Archive" *

Create new Folder/Mailbox
    A1 CREATE INBOX.Archive.2012
    A1 CREATE "To Read"

Delete Folder/Mailbox
    A1 DELETE INBOX.Archive.2012
    A1 DELETE "To Read"

Rename Folder/Mailbox
    A1 RENAME "INBOX.One" "INBOX.Two"

List Subscribed Mailboxes
    A1 LSUB "" *

Status of Mailbox (There are more flags than the ones listed)
    A1 STATUS INBOX (MESSAGES UNSEEN RECENT)

Select a mailbox
    A1 SELECT INBOX

List messages
    A1 FETCH 1:* (FLAGS)
    A1 UID FETCH 1:* (FLAGS)

Retrieve Message Content
    A1 FETCH 2 body[text]
    A1 FETCH 2 all
    A1 UID FETCH 102 (UID RFC822.SIZE BODY.PEEK[])

Close Mailbox
    A1 CLOSE

Logout
    A1 LOGOUT
```

### IMAP/POP3

| **Command**                                            | **Description**                         |
| ------------------------------------------------------ | --------------------------------------- |
| `curl -k 'imaps://<FQDN/IP>' --user <user>:<password>` | Log in to the IMAPS service using cURL. |
| `openssl s_client -connect <FQDN/IP>:imaps`            | Connect to the IMAPS service.           |
| `openssl s_client -connect <FQDN/IP>:pop3s`            |                                         |
For more
============
[# Pentesting IMAP](https://book.hacktricks.xyz/network-services-pentesting/pentesting-imap)
[# Pentesting POP3](https://book.hacktricks.xyz/network-services-pentesting/pentesting-pop)
https://0xffsec.com/handbook/services/pop/
https://secybr.com/posts/pop3-pentesting-best-practices/
https://secybr.com/posts/imap-pentesting-best-practices/


