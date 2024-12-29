![](https://miro.medium.com/v2/resize:fit:700/0*IeCX-WhEAWwtiaBx.gif)

> **__What is Rsync?_**

* _Rsync_, which stands for _remote sync_, is a remote and local file synchronization tool. It uses an algorithm to minimize the amount of data copied by only moving the portions of files that have changed.

* In this tutorial, we’ll define Rsync, review the syntax when using `rsync`, explain how to use Rsync to sync with a remote system, and other options available to you.
#### **__Defining Rsync__
-----------------
* Rsync is a very flexible network-enabled syncing tool. Due to its ubiquity on Linux and Unix-like systems and its popularity as a tool for system scripts, it’s included on most Linux distributions by default.

#### __Understanding Rsync Syntax__
-------------------------------
* The syntax for `rsync` operates similar to other tools, such as `ssh`, `scp`, and `cp`.

* First, change into your home directory by running the following command:

```
cd ~
```

* Then create a test directory:

```
mkdir dir1
```

* Create another test directory:

```
mkdir dir2
```

* Now add some test files:

```
touch dir1/file{1..100}
```

* There’s now a directory called `dir1` with 100 empty files in it. Confirm by listing out the files:

* You also have an empty directory called `dir2`. To sync the contents of `dir1` to `dir2` on the same system, you will run `rsync` and use the `-r` flag, which stands for “recursive” and is necessary for directory syncing:

```
rsync -r dir1/ dir2
```

* Another option is to use the `-a` flag, which is a combination flag and stands for “archive”. This flag syncs recursively and preserves symbolic links, special and device files, modification times, groups, owners, and permissions. It’s more commonly used than `-r` and is the recommended flag to use. Run the same command as the previous example, this time using the `-a` flag:

```
rsync -a dir1/ dir2
```

* Please note that there is a trailing slash (`/`) at the end of the first argument in the syntax of the the previous two commands and highlighted here:
*   

```
rsync -a dir1/ dir2
```

* This trailing slash signifies the contents of `dir1`. Without the trailing slash, `dir1`, including the directory, would be placed within `dir2`. The outcome would create a hierarchy like the following:

```
~/dir2/dir1/[files]
```

* Another tip is to double-check your arguments before executing an `rsync` command. Rsync provides a method for doing this by passing the `-n` or `--dry-run` options. The `-v` flag, which means “verbose”, is also necessary to get the appropriate output. You’ll combine the `a`, `n`, and `v` flags in the following command:

```
rsync -anv dir1/ dir2
```

```
Outputsending incremental file list
./
file1
file10
file100
file11
file12
file13
file14
file15
file16
file17
file18
. . .
```

* Now compare that output to the one you receive when removing the trailing slash, as in the following:

```
rsync -anv dir1 dir2
```

```
Outputsending incremental file list
dir1/
dir1/file1
dir1/file10
dir1/file100
dir1/file11
dir1/file12
dir1/file13
dir1/file14
dir1/file15
dir1/file16
dir1/file17
dir1/file18
. . .
```

This output now demonstrates that the directory itself was transferred, rather than only the files within the directory.
### Using Rsync to Sync with a Remote System
------------------------------------------------

To use `rsync` to sync with a remote system, you only need SSH access configured between your local and remote machines, as well as `rsync` installed on both systems. Once you have SSH access verified between the two machines, you can sync the `dir1` folder from the previous section to a remote machine by using the following syntax. Please note in this case, that you want to transfer the actual directory, so you’ll omit the trailing slash:

```
rsync -a ~/dir1 username@remote_host:destination_directory
```

This process is called a _push_ operation because it “pushes” a directory from the local system to a remote system. The opposite operation is _pull_, and is used to sync a remote directory to the local system. If the `dir1` directory were on the remote system instead of your local system, the syntax would be the following:

```
rsync -a username@remote_host:/home/username/dir1 place_to_sync_on_local_machine
```

Like `cp` and similar tools, the source is always the first argument, and the destination is always the second.
## *__Enumerate rsync__
------------------------------
* In `rsync` configuration, the access can be controlled through settings in the `rsyncd.conf` configuration file. If the configuration allows anonymous access, users can list and retrieve files without providing any credentials.

* Confirm the availability of the rsync service on our host with port 873 open in two popular ways:

- **Rsync modules** are recognized as **directory shares** that might be **protected with passwords**. To identify available modules and check if they require passwords, the following commands are used:

```
nmap -sV --script "rsync-list-modules" -p <PORT> <IP>
msf> use auxiliary/scanner/rsync/modules_list

# Example with IPv6 and alternate port
rsync -av --list-only rsync://[dead:beef::250:56ff:feb9:e90a]:8730
```

* Be aware that some shares might not appear in the list, possibly hiding them. Additionally, accessing some shares might be restricted to specific **credentials**, indicated by an **"Access Denied"** message.
#### __*Probing for Accessible Shares__
----------------------------------
We can next probe the service a bit to see what we can gain access to.

```shell-session
[!bash!]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```

#### __*Enumerating an Open Share__
--------------------------------
Here we can see a share called `dev`, and we can enumerate it further.

```shell-session
[!bash!]$ rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

sent 25 bytes  received 221 bytes  492.00 bytes/sec
total size is 0  speedup is 0.00
```

### *__How To Copy Files With Rsync Over SSH__
-------------------------------------------------
* use this [link1](https://www.digitalocean.com/community/tutorials/how-to-copy-files-with-rsync-over-ssh)
* use this [link2](https://medium.com/r3d-buck3t/attack-insecure-rsync-service-928951af34ed)
* use this [link3](https://0xdf.gitlab.io/2020/12/05/htb-unbalanced.html)
> **_Additional Resources_**

- [HTB: Unbalanced | 0xdf hacks stuff](https://0xdf.gitlab.io/2020/12/05/htb-unbalanced.html)
- [873 — Pentesting Rsync — HackTricks](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync)
- [https://medium.com/r3d-buck3t/attack-insecure-rsync-service-928951af34ed](https://medium.com/r3d-buck3t/attack-insecure-rsync-service-928951af34ed)

> **_Thanks for Reading!_**
> * [ LinkedIn](https://www.linkedin.com/in/soufiane-bourziq-391213266/)



