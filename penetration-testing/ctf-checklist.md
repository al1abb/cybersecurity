# CTF Checklist

## Linux PrivEsc

### Important files

`/proc/version` = may give you information on the kernel version and additional data such as whether a compiler (e.g. GCC) is installed.

`/etc/issue` = This file usually contains some information about the OS but can easily be customized or changed

`/etc/passwd` = Shows users on the system



Cat users with home directory

```bash
cat /etc/passwd | grep home
```

### Important commands

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

`find`

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
