# FTP

Let us imagine that we want to upload local files to a server and download other files using the [FTP](https://datatracker.ietf.org/doc/html/rfc959) protocol. In an FTP connection, two channels are opened. First, the client and server establish a control channel through `TCP port 21`. The client sends commands to the server, and the server returns status codes. Then both communication participants can establish the data channel via `TCP port 20`. This channel is used exclusively for data transmission, and the protocol watches for errors during this process. If a connection is broken off during transmission, the transport can be resumed after re-established contact.

{% hint style="info" %}
TCP Port 21 - Control Channel

TCP Port 20 - Data Channel
{% endhint %}

## Active vs Passive FTP

In the **active** variant, the client establishes the connection as described via TCP port 21 and thus informs the server via which client-side port the server can transmit its responses. However, if a firewall protects the client, the server cannot reply because all external connections are blocked.&#x20;

For this purpose, the **passive mode** has been developed. Here, the server announces a port through which the client can establish the data channel. Since the client initiates the connection in this method, the firewall does not block the transfer.

## Commands and Authentication

The FTP knows different [commands](https://web.archive.org/web/20230326204635/https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/) and status codes. Not all of these commands are consistently implemented on the server. For example, the client-side instructs the server-side to upload or download files, organize directories or delete files. The server responds in each case with a status code that indicates whether the command was successfully implemented. A list of possible status codes can be found [here](https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes).

Usually, we need credentials to use FTP on a server. We also need to know that FTP is a `clear-text` protocol that can sometimes be sniffed if conditions on the network are right. However, there is also the possibility that a server offers `anonymous FTP`. The server operator then allows any user to upload or download files via FTP without using a password. Since there are security risks associated with such a public FTP server, the options for users are usually limited.

## TFTP

`Trivial File Transfer Protocol` (`TFTP`) is simpler than FTP and performs file transfers between client and server processes. However, it `does not` provide user authentication and other valuable features supported by FTP. In addition, while FTP uses TCP, TFTP uses `UDP`, making it an unreliable protocol and causing it to use UDP-assisted application layer recovery.

This is reflected, for example, in the fact that TFTP, unlike FTP, does not require the user's authentication. It does not support protected login via passwords and sets limits on access based solely on the read and write permissions of a file in the operating system. Practically, this leads to TFTP operating exclusively in directories and with files that have been shared with all users and can be read and written globally. Because of the lack of security, TFTP, unlike FTP, may only be used in local and protected networks.

Let us take a look at a few commands of `TFTP`:

| **Commands** | **Description**                                                                                                                        |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| `connect`    | Sets the remote host, and optionally the port, for file transfers.                                                                     |
| `get`        | Transfers a file or set of files from the remote host to the local host.                                                               |
| `put`        | Transfers a file or set of files from the local host onto the remote host.                                                             |
| `quit`       | Exits tftp.                                                                                                                            |
| `status`     | Shows the current status of tftp, including the current transfer mode (ascii or binary), connection status, time-out value, and so on. |
| `verbose`    | Turns verbose mode, which displays additional information during file transfer, on or off.                                             |

Unlike the FTP client, `TFTP` does not have directory listing functionality.

## Default Configuration

One of the most used FTP servers on Linux-based distributions is [vsFTPd](https://security.appspot.com/vsftpd.html). The default configuration of vsFTPd can be found in `/etc/vsftpd.conf`, and some settings are already predefined by default. It is highly recommended to install the vsFTPd server on a VM and have a closer look at this configuration.

## vsftpd Config file

```bash
cat /etc/vsftpd.conf
```

| **Setting**                                                   | **Description**                                                                                          |
| ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `listen=NO`                                                   | Run from inetd or as a standalone daemon?                                                                |
| `listen_ipv6=YES`                                             | Listen on IPv6 ?                                                                                         |
| `anonymous_enable=NO`                                         | Enable Anonymous access?                                                                                 |
| `local_enable=YES`                                            | Allow local users to login?                                                                              |
| `dirmessage_enable=YES`                                       | Display active directory messages when users go into certain directories?                                |
| `use_localtime=YES`                                           | Use local time?                                                                                          |
| `xferlog_enable=YES`                                          | Activate logging of uploads/downloads?                                                                   |
| `connect_from_port_20=YES`                                    | Connect from port 20?                                                                                    |
| `secure_chroot_dir=/var/run/vsftpd/empty`                     | Name of an empty directory                                                                               |
| `pam_service_name=vsftpd`                                     | This string is the name of the PAM service vsftpd will use.                                              |
| `rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem`          | The last three options specify the location of the RSA certificate to use for SSL encrypted connections. |
| `rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key` |                                                                                                          |
| `ssl_enable=NO`                                               |                                                                                                          |

## ftpusers file

In addition, there is a file called `/etc/ftpusers` that we also need to pay attention to, as this file is used to deny certain users access to the FTP service. In the following example, the users `guest`, `john`, and `kevin` are not permitted to log in to the FTP service, even if they exist on the Linux system:

```bash
al1abb@htb[/htb]$ cat /etc/ftpusers

guest
john
kevin#
```

## Dangerous Settings

There are many different security-related settings we can make on each FTP server. These can have various purposes, such as testing connections through the firewalls, testing routes, and authentication mechanisms. One of these authentication mechanisms is the `anonymous` user. This is often used to allow everyone on the internal network to share files and data without accessing each other's computers.&#x20;

With vsFTPd, the [optional settings](http://vsftpd.beasts.org/vsftpd_conf.html) that can be added to the configuration file for the anonymous login look like this:

| **Setting**                    | **Description**                                                                    |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| `anonymous_enable=YES`         | Allowing anonymous login?                                                          |
| `anon_upload_enable=YES`       | Allowing anonymous to upload files?                                                |
| `anon_mkdir_write_enable=YES`  | Allowing anonymous to create new directories?                                      |
| `no_anon_password=YES`         | Do not ask anonymous for password?                                                 |
| `anon_root=/home/username/ftp` | Directory for anonymous.                                                           |
| `write_enable=YES`             | Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE? |

With the standard FTP client (`ftp`), we can access the FTP server accordingly and log in with the anonymous user if the settings shown above have been used. The use of the anonymous account can occur in internal environments and infrastructures where the participants are all known. Access to this type of service can be set temporarily or with the setting to accelerate the exchange of files.

As soon as we connect to the vsFTPd server, the `response code 220` is displayed with the banner of the FTP server. Often this banner contains the description of the `service` and even the `version` of it. It also tells us what type of system the FTP server is. One of the most common configurations of FTP servers is to allow `anonymous` access, which does not require legitimate credentials but provides access to some files. Even if we cannot download them, sometimes just listing the contents is enough to generate further ideas and note down information that will help us in another approach.

## Anonymous Login

```bash
al1abb@htb[/htb]$ ftp 10.129.14.136

Connected to 10.129.14.136.
220 "Welcome to the HTB Academy vsFTP service."
Name (10.129.14.136:cry0l1t3): anonymous

230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.


ftp> ls

200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.
```

## vsFTPd Status

However, to get the first overview of the server's settings, we can use the following command:

```bash
ftp> status

Connected to 10.129.14.136.
No proxy connection.
Connecting using address family: any.
Mode: stream; Type: binary; Form: non-print; Structure: file
Verbose: on; Bell: off; Prompting: on; Globbing: on
Store unique: off; Receive unique: off
Case: off; CR stripping: on
Quote control characters: on
Ntrans: off
Nmap: off
Hash mark printing: off; Use of PORT cmds: on
Tick counter printing: off
```

## vsFTPd Detailed Output

Some commands should be used occasionally, as these will make the server show us more information that we can use for our purposes. These commands include `debug` and `trace`.

```bash
ftp> debug

Debugging on (debug=1).


ftp> trace

Packet tracing on.


ftp> ls

---> PORT 10,10,14,4,188,195
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.
```
