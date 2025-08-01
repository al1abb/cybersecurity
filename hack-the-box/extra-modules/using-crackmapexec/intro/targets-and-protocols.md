# Targets and Protocols

Depending on the scope, we can scan one or more targets within a specific range or predefined hostnames during an engagement. CrackMapExec can handle that perfectly. The target can be a [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), one IP, a hostname, or a file name containing the IP addresses/hostnames.

### **Targets Format**

```shell-session
al1abb@htb[/htb]$ crackmapexec [protocol] 10.10.10.1
al1abb@htb[/htb]$ crackmapexec [protocol] 10.10.10.1 10.10.10.2 10.10.10.3
al1abb@htb[/htb]$ crackmapexec [protocol] 10.10.10.1/24
al1abb@htb[/htb]$ crackmapexec [protocol] internal.local
al1abb@htb[/htb]$ crackmapexec [protocol] targets.txt
```

***

## Supported Protocols

CrackMapExec is designed to help us during an internal security assessment. Therefore, it must support multiple protocols linked to Windows. At the time of writing, CrackMapExec supports seven protocols:

| **Protocol** | **Default Port** |
| ------------ | ---------------- |
| SMB          | 445              |
| WINRM        | 5985/5986        |
| MSSQL        | 1433             |
| LDAP         | 389              |
| SSH          | 22               |
| RDP          | 3389             |
| FTP          | 21               |

To confirm available protocols, we can run `crackmapexec --help` to list available options and protocols.

### **List General Options and Protocols**

```shell-session
$ crackmapexec --help

usage: crackmapexec [-h] [-t THREADS] [--timeout TIMEOUT] [--jitter INTERVAL] [--darrell] [--verbose] {ftp,ssh,winrm,mssql,rdp,ldap,smb} ...

      ______ .______           ___        ______  __  ___ .___  ___.      ___      .______    _______ ___   ___  _______   ______
     /      ||   _  \         /   \      /      ||  |/  / |   \/   |     /   \     |   _  \  |   ____|\  \ /  / |   ____| /      |
    |  ,----'|  |_)  |       /  ^  \    |  ,----'|  '  /  |  \  /  |    /  ^  \    |  |_)  | |  |__    \  V  /  |  |__   |  ,----'
    |  |     |      /       /  /_\  \   |  |     |    <   |  |\/|  |   /  /_\  \   |   ___/  |   __|    >   <   |   __|  |  |
    |  `----.|  |\  \----. /  _____  \  |  `----.|  .  \  |  |  |  |  /  _____  \  |  |      |  |____  /  .  \  |  |____ |  `----.
     \______|| _| `._____|/__/     \__\  \______||__|\__\ |__|  |__| /__/     \__\ | _|      |_______|/__/ \__\ |_______| \______|

                                                A swiss army knife for pentesting networks
                                    Forged by @byt3bl33d3r and @mpgn_x64 using the powah of dank memes

                                           Exclusive release for Porchetta Industries users
                                                       https://porchetta.industries/

                                                   Version : 5.4.0
                                                   Codename: Indestructible G0thm0g

optional arguments:
  -h, --help            show this help message and exit
  -t THREADS            set how many concurrent threads to use (default: 100)
  --timeout TIMEOUT     max timeout in seconds of each thread (default: None)
  --jitter INTERVAL     sets a random delay between each connection (default: None)
  --darrell             give Darrell a hand
  --verbose             enable verbose output

protocols:
  available protocols

  {ftp,ssh,winrm,mssql,rdp,ldap,smb}
    ftp                 own stuff using FTP
    ssh                 own stuff using SSH
    winrm               own stuff using WINRM
    mssql               own stuff using MSSQL
    rdp                 own stuff using RDP
    ldap                own stuff using LDAP
    smb                 own stuff using SMB
```

We can run `crackmapexec <protocol> --help` to view the options a specified protocol supports. Let's see LDAP as an example:

### **View Options Available with LDAP Protocol**

```shell-session
$ crackmapexec ldap --help

usage: crackmapexec ldap [-h] [-id CRED_ID [CRED_ID ...]]
                         [-u USERNAME [USERNAME ...]]
                         [-p PASSWORD [PASSWORD ...]] [-k]
                         [--export EXPORT [EXPORT ...]]
                         [--aesKey AESKEY [AESKEY ...]] [--kdcHost KDCHOST]
                         [--gfail-limit LIMIT | --ufail-limit LIMIT | --fail-limit LIMIT]
                         [-M MODULE] [-o MODULE_OPTION [MODULE_OPTION ...]]
                         [-L] [--options] [--server {http,https}]
                         [--server-host HOST] [--server-port PORT]
                         [--connectback-host CHOST] [-H HASH [HASH ...]]
                         [--no-bruteforce] [--continue-on-success]
                         [--port {636,389}] [--no-smb]
                         [-d DOMAIN | --local-auth] [--asreproast ASREPROAST]
                         [--kerberoasting KERBEROASTING]
                         [--trusted-for-delegation] [--password-not-required]
                         [--admin-count] [--users] [--groups]
                         [target ...]

positional arguments:
  target                the target IP(s), range(s), CIDR(s), hostname(s),
                        FQDN(s), file(s) containing a list of targets, NMap
                        XML or .Nessus file(s)

optional arguments:
  -h, --help            show this help message and exit
  -id CRED_ID [CRED_ID ...]
                        database credential ID(s) to use for authentication
  -u USERNAME [USERNAME ...]
                        username(s) or file(s) containing usernames
  -p PASSWORD [PASSWORD ...]
                        password(s) or file(s) containing passwords
  -k, --kerberos        Use Kerberos authentication from ccache file
                        (KRB5CCNAME)
  --export EXPORT [EXPORT ...]
                        Export result into a file, probably buggy
  --aesKey AESKEY [AESKEY ...]
                        AES key to use for Kerberos Authentication (128 or 256
                        bits)
  --kdcHost KDCHOST     FQDN of the domain controller. If omitted it will use
                        the domain part (FQDN) specified in the target
                        parameter
  --gfail-limit LIMIT   max number of global failed login attempts
  --ufail-limit LIMIT   max number of failed login attempts per username
  --fail-limit LIMIT    max number of failed login attempts per host
  -M MODULE, --module MODULE
                        module to use
  -o MODULE_OPTION [MODULE_OPTION ...]
                        module options
  -L, --list-modules    list available modules
  --options             display module options
  --server {http,https}
                        use the selected server (default: https)
  --server-host HOST    IP to bind the server to (default: 0.0.0.0)
  --server-port PORT    start the server on the specified port
  --connectback-host CHOST
                        IP for the remote system to connect back to (default:
                        same as server-host)
  -H HASH [HASH ...], --hash HASH [HASH ...]
                        NTLM hash(es) or file(s) containing NTLM hashes
  --no-bruteforce       No spray when using file for username and password
                        (user1 => password1, user2 => password2
  --continue-on-success
                        continues authentication attempts even after successes
  --port {636,389}      LDAP port (default: 389)
  --no-smb              No smb connection
  -d DOMAIN             domain to authenticate to
  --local-auth          authenticate locally to each target

Retrevie hash on the remote DC:
  Options to get hashes from Kerberos

  --asreproast ASREPROAST
                        Get AS_REP response ready to crack with hashcat
  --kerberoasting KERBEROASTING
                        Get TGS ticket ready to crack with hashcat

Retrieve useful information on the domain:
  Options to to play with Kerberos

  --trusted-for-delegation
                        Get the list of users and computers with flag
                        TRUSTED_FOR_DELEGATION
  --password-not-required
                        Get the list of users with flag PASSWD_NOTREQD
  --admin-count         Get objets that had the value adminCount=1
  --users               Enumerate enabled domain users
  --groups              Enumerate domain groups
```

## Selecting a Target and Using a Protocol

With several protocols supported and several options for each, we may think it will be hard to master CrackMapExec. Fortunately, once we understand how it works for one protocol, the same logic applies to the other protocols. For example, password spraying is the same for all protocols:

### **Password Spray Example with WinRm**

```shell-session
$ crackmapexec winrm 10.10.10.1 -u users.txt -p password.txt --no-bruteforce --continue-on-success
```

If we want to perform a password spraying attack against any other protocol, we need to modify the protocol:

### **Target Protocols**

```shell-session
al1abb@htb[/htb]$ crackmapexec smb 10.10.10.1 [protocol options]
al1abb@htb[/htb]$ crackmapexec mssql 10.10.10.1 [protocol options]
al1abb@htb[/htb]$ crackmapexec ldap 10.10.10.1 [protocol options]
al1abb@htb[/htb]$ crackmapexec ssh 10.10.10.1 [protocol options]
al1abb@htb[/htb]$ crackmapexec rdp 10.10.10.1 [protocol options]
al1abb@htb[/htb]$ crackmapexec ftp 10.10.10.1 [protocol options]
```

Once we understand this simple rule, we will find that the power of CrackMapExec is due to the ease of usage regarding all the options offered.

***

## Export Function

CrackMapExec comes with an export function, but it is buggy, as shown in the help menu. It requires the full path of the file to export:

### **Exporting result CME**

```shell-session
$ crackmapexec smb 10.10.10.1 [protocol options] --export $(pwd)/export.txt
```

In the following section, we will discuss some export examples.

***

## Protocol Modules

CrackMapExec supports modules, which we will use and discuss later. Each protocol has different modules. We can run `crackmapexec <protocol> -L` to view available modules for the specified protocol.

### **View Available Modules for LDAP**

```shell-session
$ crackmapexec ldap -L

[*] MAQ                       Retrieves the MachineAccountQuota domain-level attribute
[*] adcs                      Find PKI Enrollment Services in Active Directory and Certificate Templates Names
[*] daclread                  Read and backup the Discretionary Access Control List of objects. Based on the work of @_nwodtuhs and @BlWasp_. Be carefull, this module cannot read the DACLS recursively, more explains in the options.
[*] get-desc-users            Get description of the users. May contained password
[*] get-network               
[*] laps                      Retrieves the LAPS passwords
[*] ldap-checker              Checks whether LDAP signing and binding are required and / or enforced
[*] ldap-signing              Check whether LDAP signing is required
[*] subnets                   Retrieves the different Sites and Subnets of an Active Directory
[*] user-desc                 Get user descriptions stored in Active Directory
[*] whoami                    Get details of provided user
```
