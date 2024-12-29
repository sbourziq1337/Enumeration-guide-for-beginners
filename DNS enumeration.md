## ***What is DNS?***
---------------------------------------------------
Every host is identified by the IP address but remembering numbers is very difficult for people also the IP addresses are not static therefore a mapping is required to change the domain name to the IP address. So DNS is used to convert the domain name of the websites to their numerical IP address.
### Types of Domain
----------------------------
There are various kinds of domains:

- ****Generic Domains:**** .com(commercial), .edu(educational), .mil(military), .org(nonprofit organization), .net(similar to commercial) all these are generic domains.
- ****Country Domain:**** .in (India) .us .uk
- ****Inverse Domain:**** if we want to know what is the domain name of the website. IP to domain name mapping. So DNS can provide both the mapping for example to find the IP addresses of geeksforgeeks.org then we have to type

> nslookup www.geeksforgeeks.org

![Types of DNS](https://media.geeksforgeeks.org/wp-content/uploads/20230319113854/Types-of-DNS.png)

However, the DNS does not only link computer names and IP addresses. It also stores and outputs additional information about the services associated with a domain. A DNS query can therefore also be used, for example, to determine which computer serves as the e-mail server for the domain in question or what the domain's name servers are called.

![](https://academy.hackthebox.com/storage/modules/27/tooldev-dns.png)
## ****Domain Name Server****

The client machine sends a request to the local name server, which, if the root does not find the address in its database, sends a request to the root name server, which in turn, will route the query to a top-level domain (TLD) or authoritative name server. The root name server can also contain some hostName to IP address mappings. The Top-level domain (TLD) server always knows who the authoritative name server is. So finally the IP address is returned to the local name server which in turn returns the IP address to the host.


![Domain Name Server](https://media.geeksforgeeks.org/wp-content/cdn-uploads/gq/2017/02/DNS_3.png)
Different `DNS records` are used for the DNS queries, which all have various tasks. Moreover, separate entries exist for different functions since we can set up mail servers and other servers for a domain.

[![DNS Footprinting 1](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_1.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_1.jpg)
The `SOA` record is located in a domain's zone file and specifies who is responsible for the operation of the domain and how DNS information for the domain is managed.

## How Does DNS Work?

The working of DNS starts with converting a hostname into an IP Address. A domain name serves as a distinctive identification for a website. It is used in place of an IP address to make it simpler for consumers to visit websites. Domain Name System works by executing the database whose work is to store the name of hosts which are available on the Internet. The top-level domain server stores address information for top-level domains such as .com and .net, .org, and so on. If the Client sends the request, then the DNS resolver sends a request to DNS Server to fetch the IP Address. In case, when it does not contain that particular IP Address with a hostname, it forwards the request to another DNS Server. When IP Address has arrived at the resolver, it completes the request over [Internet Protocol](https://www.geeksforgeeks.org/types-of-internet-protocols/) .

For more, you can refer to [Working of DNS Server](https://www.geeksforgeeks.org/working-of-domain-name-system-dns-server/) .

![How-DNS-Works](https://media.geeksforgeeks.org/wp-content/uploads/20240711144434/How-DNS-Works-gif-ezgifcom-optimize-1.gif)

![Working-of-DNS](https://media.geeksforgeeks.org/wp-content/uploads/20240619160023/Working-of-DNS.gif)


## Default Configuration
-----------------------------------
[Configuration DNS](https://www.geeksforgeeks.org/how-to-configure-dns-in-linux/)
## **Enumeration**
----------------------------

 ### __The first tool is nslookup. Here’s how to use nslookup to query about a domain.__
--------------------------------

[![DNS Footprinting 2](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_2.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_2.jpg)

We can even query for a specific type of record using nslookup. Let’s query specifically for “NS” and “MX” records.

[![DNS Footprinting 34](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_34.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_34.jpg)

[![DNS Footprinting 56](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_56.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_56.jpg)

There is another tool named DIG that can be used for DNS lookup.

[![DNS Footprinting 78](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_78.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_78.jpg)

This tool can also be used to query for a specific type of records as shown below.

[![DNS Footprinting 9](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_9.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_9.jpg)
```shell-session
dig ns inlanefreight.htb @10.129.14.128
```

[![DNS Footprinting 1011](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_1011.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_1011.jpg)

[![DNS Footprinting 1213](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_1213.jpg)](https://www.hackercoolmagazine.com/wp-content/uploads/2023/07/DNS_Footprinting_1213.jpg)
**Banner Grabbing**

There aren't banners in DNS but you can gran the magic query for `version.bind. CHAOS TXT` which will work on most BIND nameservers. You can perform this query using `dig`:

```
dig version.bind CHAOS TXT @DNS
```

Moreover, the tool [`fpdns`](https://github.com/kirei/fpdns) can also fingerprint the server.

It's also possible to grab the banner also with a **nmap** script:

```
--script dns-nsid
```
### **Any record**
------------------------------
The record **ANY** will ask the DNS server to **return** all the available **entries** that **it is willing to disclose**.

```
dig any victim.com @<DNS_IP>
```

**Zone Transfer**

This procedure is abbreviated `Asynchronous Full Transfer Zone` (`AXFR`).

```
dig axfr @<DNS_IP> #Try zone transfer without domain
dig axfr @<DNS_IP> <DOMAIN> #Try zone transfer guessing the domain
fierce --domain <DOMAIN> --dns-servers <DNS_IP> #Will try toperform a zone transfer against every authoritative name server and if this doesn'twork, will launch a dictionary attack
```
### More info
------------------
```
dig ANY @<DNS_IP> <DOMAIN>     #Any information
dig A @<DNS_IP> <DOMAIN>       #Regular DNS request
dig AAAA @<DNS_IP> <DOMAIN>    #IPv6 DNS request
dig TXT @<DNS_IP> <DOMAIN>     #Information
dig MX @<DNS_IP> <DOMAIN>      #Emails related
dig NS @<DNS_IP> <DOMAIN>      #DNS that resolves that name
dig -x 192.168.0.2 @<DNS_IP>   #Reverse lookup
dig -x 2a00:1450:400c:c06::93 @<DNS_IP> #reverse IPv6 lookup

#Use [-p PORT]  or  -6 (to use ivp6 address of dns)
```

### For exmple:
-----------------------------
#### IG - Version Query
----------------------------
```shell-session
akerashadow@htb[/htb]$ dig CH TXT version.bind 10.129.120.85

; <<>> DiG 9.10.6 <<>> CH TXT version.bind
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47786
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; ANSWER SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1"

;; ADDITIONAL SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1-Debian"

;; Query time: 2 msec
;; SERVER: 10.129.120.85#53(10.129.120.85)
;; WHEN: Wed Jan 05 20:23:14 UTC 2023
;; MSG SIZE  rcvd: 101
```

We can use the option `ANY` to view all available records. This will cause the server to show us all available entries that it is willing to disclose. It is important to note that not all entries from the zones will be shown.
#### DIG - ANY Query
--------------------------
```shell-session
akerashadow@htb[/htb]$ dig any inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7649
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 064b7e1f091b95120100000061476865a6026d01f87d10ca (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      ANY

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:42:13 CEST 2021
;; MSG SIZE  rcvd: 437
```
#### DIG - AXFR Zone Transfer - Internal
--------------------------------
```shell-session
akerashadow@htb[/htb]$ dig axfr internal.inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> axfr internal.inlanefreight.htb @10.129.14.128
;; global options: +cmd
internal.inlanefreight.htb. 604800 IN   SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
internal.inlanefreight.htb. 604800 IN   TXT     "MS=ms97310371"
internal.inlanefreight.htb. 604800 IN   TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
internal.inlanefreight.htb. 604800 IN   TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
internal.inlanefreight.htb. 604800 IN   NS      ns.inlanefreight.htb.
dc1.internal.inlanefreight.htb. 604800 IN A     10.129.34.16
dc2.internal.inlanefreight.htb. 604800 IN A     10.129.34.11
mail1.internal.inlanefreight.htb. 604800 IN A   10.129.18.200
ns.internal.inlanefreight.htb. 604800 IN A      10.129.34.136
vpn.internal.inlanefreight.htb. 604800 IN A     10.129.1.6
ws1.internal.inlanefreight.htb. 604800 IN A     10.129.1.34
ws2.internal.inlanefreight.htb. 604800 IN A     10.129.1.35
wsus.internal.inlanefreight.htb. 604800 IN A    10.129.18.2
internal.inlanefreight.htb. 604800 IN   SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:53:11 CEST 2021
;; XFR size: 15 records (messages 1, bytes 664)
```

Many different tools can be used for this, and most of them work in the same way. One of these tools is,[DNSenum](https://www.hackercoolmagazine.com/complete-guide-to-dnsenum/), [DNSrecon](https://www.hackercoolmagazine.com/complete-guide-to-dnsrecon/) and DNSwalk..

exmple :
```shell-session
 dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```

