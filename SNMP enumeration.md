
# What is SNMP?

[![Untitled](https://secybr.com/assets/img/pitcures/snmp/snmp.png)](https://secybr.com/assets/img/pitcures/snmp/snmp.png)
SNMP stands for Simple Network Management Protocol. It is a protocol that is used to monitor devices in the network. These devices include routers, switches, bridges, computers and printers etc. This protocol really helpful for organizations with thousands of devices in is network. [Learn more about how SNMP works](https://www.site24x7.com/network/what-is-snmp.html).[# What is SNMP](https://checkmk.com/guides/what-is-snmp) SNMP contains 3 main components. They are SNMP manager, SNMP agent and Management Information Base (MIB). SNMP runs on UDP ports 161,162.

## **MIB
----------------------------

MIB stands for Management Information Base and is a collection of information organized hierarchically. These are accessed using a protocol such as SNMP. There are two types of MIBs: scalar and tabular. Scalar objects define a single object instance whereas tabular objects define multiple related object instances grouped in MIB tables.

## **OIDs
---------------------------------

An OID represents a node in a hierarchical namespace. A sequence of numbers uniquely identifies each node, allowing the node's position in the tree to be determined. The longer the chain, the more specific the information. Many nodes in the OID tree contain nothing except references to those below them. The OIDs consist of integers and are usually concatenated by dot notation. We can look up many MIBs for the associated OIDs in the [Object Identifier Registry](https://www.alvestrand.no/objectid/).

[![Untitled](https://secybr.com/assets/img/pitcures/snmp/snmp1.png)](https://secybr.com/assets/img/pitcures/snmp/snmp1.png)
## *Default Configuration

The default configuration of the SNMP daemon defines the basic settings for the service, which include the IP addresses, ports, MIB, OIDs, authentication, and community strings.

#### *SNMP Daemon Config
---------------------------

```shell-session
akerashadow@htb[/htb]$ cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'

sysLocation    Sitting on the Dock of the Bay
sysContact     Me <me@example.org>
sysServices    72
master  agentx
agentaddress  127.0.0.1,[::1]
view   systemonly  included   .1.3.6.1.2.1.1
view   systemonly  included   .1.3.6.1.2.1.25.1
rocommunity  public default -V systemonly
rocommunity6 public default -V systemonly
rouser authPrivUser authpriv -V systemonly
```

The configuration of this service can also be changed in many ways. Therefore, we recommend setting up a VM to install and configure the SNMP server ourselves. All the settings that can be made for the SNMP daemon are defined and described in the [manpage](http://www.net-snmp.org/docs/man/snmpd.conf.html)
[# How To Install and Configure an SNMP](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-an-snmp-daemon-and-client-on-ubuntu-18-04)

## **Enumeration
-------------------------------------------
### *Nmap

- `sudo nmap -sU -sV -p 161 -Pn -n --disable-arp-ping --script=snmp-* 10.129.201.223`

### *snmpwalk
-------------------------------------

- `sudo perl /usr/share/doc/libnet-snmp-perl/examples/snmpwalk.pl -c public 10.129.220.234`
    
- `snmpwalk -v2c -c public 10.129.14.128`
    
- `snmpwalk -v1 -c public 10.10.1.224 .1.3.6.1.4.1.318` the `.1.3.6.1.4.1.318`, is theOID
- 
- See more about snmpwalk [here](https://www.comparitech.com/net-admin/snmpwalk-examples-windows-linux/)
    
- `snmpwalk -v 2c -c private 10.10.1.224`

* As a helper to walk a network, instead of launching `snmpgetnext` for each SNMP host, `snmpwalk` can be used to do it automatically:

![Snmpwalk command example](https://checkmk.com/application/files/2416/5813/9275/snmpwalk-example.png)

### *snmptable
****************************

**`snmptable`** returns the content of an SNMP table, displaying it one row at a time:

![snmptable command example](https://checkmk.com/application/files/7016/5813/9273/snmptable-example.png)

### *Onesixtyone
----------------------------------------
*  [Github Repo](https://github.com/trailofbits/onesixtyone)
- `sudo apt install onesixtyone`
    
- `onesixtyone -c dict.txt 10.129.42.254`
    
- A tool such as onesixtyone can be used to brute force the community string names using a dictionary file of common community strings such as the dict.txt file included in the GitHub repo for the tool.

* __Here we recognize some Python packages that have been installed on the system. If we do not know the community string, we can use `onesixtyone` and `SecLists` wordlists to identify these community strings.

```shell-session
akerashadow@htb[/htb]$ sudo apt install onesixtyone
akerashadow@htb[/htb]$ onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128

Scanning 1 hosts, 3220 communities
10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64
```


### *Braa
----------------------------------
*   [Github Repo](https://github.com/mteg/braa)
    
- `sudo apt install braa`
    
- `braa <community string>@<IP>:.1.3.6.*` example `braa public@10.129.14.128:.1.3.6.*`

* 0nce we know a community string, we can use it with [braa](https://github.com/mteg/braa) to brute-force the individual OIDs and enumerate the information behind them.

```shell-session
akerashadow@htb[/htb]$ sudo apt install braa
akerashadow@htb[/htb]$ braa <community string>@<IP>:.1.3.6.*   # Syntax
akerashadow@htb[/htb]$ braa public@10.129.14.128:.1.3.6.*

10.129.14.128:20ms:.1.3.6.1.2.1.1.1.0:Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64
10.129.14.128:20ms:.1.3.6.1.2.1.1.2.0:.1.3.6.1.4.1.8072.3.2.10
10.129.14.128:20ms:.1.3.6.1.2.1.1.3.0:548
10.129.14.128:20ms:.1.3.6.1.2.1.1.4.0:mrb3n@inlanefreight.htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.5.0:htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.6.0:US
10.129.14.128:20ms:.1.3.6.1.2.1.1.7.0:78
...SNIP...
```

[# Comprehensive Guide to SNMP: Protocol, Vulnerabilities, and Pentesting](https://medium.com/@yashpawar1199/comprehensive-guide-to-snmp-protocol-vulnerabilities-and-pentesting-1490ee0dd665)

[# SNMP Pentesting Best Practicies](https://secybr.com/posts/snmp-pentesting-best-practicies/)

[# Tools SNMP](https://checkmk.com/blog/how-configure-snmp-linux)

 [Learn more about how SNMP works](https://www.site24x7.com/network/what-is-snmp.html).