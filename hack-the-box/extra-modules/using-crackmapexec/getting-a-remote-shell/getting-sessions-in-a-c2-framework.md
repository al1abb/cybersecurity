# Getting Sessions in a C2 Framework

One thing that can be interesting with CrackMapExec is when we compromise multiple targets, we may want to do more recon or operate using a C2 Framework like Empire or Metasploit. We can use CrackMapExec to execute a payload in each target machine and get an agent into our C2.

This section will discuss two modules that integrate CME with PowerShell Empire and Metasploit framework. We will also explore an alternative if we use a different C2 framework.

***

## Empire

We will start by installing the [Empire framework](https://github.com/BC-SECURITY/Empire) using the [guide](https://bc-security.gitbook.io/empire-wiki/quickstart/installation) provided on their website.

### **Install and Launch Empire Server**

```shell-session
al1abb@htb[/htb]$ git clone --recursive https://github.com/BC-SECURITY/Empire.git -q
al1abb@htb[/htb]$ cd Empire
al1abb@htb[/htb]$ sudo su
al1abb@htb[/htb]# curl -sSL https://install.python-poetry.org | python3 -
al1abb@htb[/htb]# poetry install

<SNIP>
```

Next, we need to run Empire with the username and password we choose. We will use the username `empireadmin` and the password `HackTheBoxCME!`.

### **Running Empire with a Custom Username and Password**

```shell-session
# poetry run python empire.py server --username empireadmin --password HackTheBoxCME!

[*] Loading default config           
[*] Loading bypasses from: /home/plaintext/Empire/empire/server/bypasses/
[*] Loading stagers from: /home/plaintext/Empire/empire/server/stagers/
[*] Loading modules from: /home/plaintext/Empire/empire/server/modules/
[*] Loading listeners from: /home/plaintext/Empire/empire/server/listeners/

<SNIP>
```

Next, we need to edit the CrackMapExec configuration file and the Empire client configuration file to match the username and password we choose.

CrackMapExec configuration file is located by default at `~/.cme/cme.conf`. We need to change the `[Empire]` option to match the username `empireadmin` and password `HackTheBoxCME!`. By default, Empire local server runs at port 1337. It can be modified in the CrackMapExec configuration file.

### CrackMapExec Configuration File

```shell-session
$ cat ~/.cme/cme.conf

[CME]
workspace = default
last_used_db = smb
pwn3d_label = Pwn3d!
audit_mode = 

[BloodHound]
bh_enabled = False
bh_uri = 127.0.0.1
bh_port = 7687
bh_user = neo4j
bh_pass = neo4j

[Empire]
api_host = 127.0.0.1
api_port = 1337
username = empireadmin
password = HackTheBoxCME!

<SNIP>
```

We need to do the same for the Empire configuration file. The file is located at `empire/client/config.yaml`:

### **Reviewing**

```shell-session
# pwd; echo "";cat empire/client/config.yaml            
                                                                                          
/home/plaintext/Empire

suppress-self-cert-warning: true
auto-copy-stagers: true                    
servers:     
  localhost:             
    host: https://localhost
    port: 1337
    socketport: 5000                        
    username: empireadmin
    password: HackTheBoxCME!
    autoconnect: true  
  other-server:       
    host: https://localhost
    port: 1337
    socketport: 5000                     
    username: empireadmin
    password: HackTheBoxCME!
  another-one:         
    host: https://localhost
    port: 1337                             
    socketport: 5000
    username: empireadmin      
    password: HackTheBoxCME!

<SNIP>
```

Once the configuration files are changed, we must connect to the Empire server with the Empire client.

### **Empire Client Connection**

```shell-session
# poetry run python empire.py client --config empire/client/config.yaml         
                                                     
Loading config from empire/client/config.yaml                       

<SNIP>

========================================================================================
 [Empire] Post-Exploitation Framework
========================================================================================
 [Version] 4.8.1 BC Security Fork | [Web] https://github.com/BC-SECURITY/Empire
========================================================================================
 [Starkiller] Multi-User GUI | [Web] https://github.com/BC-SECURITY/Starkiller
========================================================================================
 [Documentation] | [Web] https://bc-security.gitbook.io/empire-wiki/
========================================================================================

   _______   ___  ___   ______    __   ______        _______
  |   ____| |   \/   | |   _  \  |  | |   _  \      |   ____|
  |  |__    |  \  /  | |  |_)  | |  | |  |_)  |     |  |__
  |   __|   |  |\/|  | |   ___/  |  | |      /      |   __|
  |  |____  |  |  |  | |  |      |  | |  |\  \----. |  |____
  |_______| |__|  |__| | _|      |__| | _| `._____| |_______|


       409 modules currently loaded

       0 listeners currently active

       0 agents currently active

[*] Connected to localhost
(Empire) > 
```
