# Notes

## 17/07. PowerShell Commands to remember

{% hint style="info" %}
2 main commands to remember:&#x20;

`Get-Command (gcm)` and `Get-Help` (`help`)
{% endhint %}

`Get-Command *dns*` - prints every command with DNS in middle of the name&#x20;

You can specify verb part by using `-Verb`&#x20;

Noun part can be specified using `-Noun`&#x20;

Example of above 2 lines:&#x20;

`Get-Command -Verb get -Noun *firewall*`&#x20;

### Help in PowerShell

`ipconfig /?`

`ipconfig -h`

Same outputs

## 22/07. Why free Wi-Fi and DNS is bad, Trust & Ethics

{% hint style="info" %}
Default Gateway is usually IP .1(first) or IP .254(last) in big companies (Houses have 1)
{% endhint %}

### DNS

DNS - Translates names to IP Addresses

www.domain.com

www - is standardized here. (Computer name)

DNS Zones:

1. **Forward** (Name => IP)
2. **Reverse** (IP => Name)
3. There is also **Name => Name**. (For example when companies change names). Kind of like an alias

{% hint style="warning" %}
Avoid using free WiFi. VPN makes it harder but it still is breakable
{% endhint %}

{% hint style="success" %}
Trust and Ethics is important! When a company hires you, they need to trust you
{% endhint %}

{% hint style="info" %}
8.8.8.8, 8.8.4.4, 1.1.1.1 DNS's are free.&#x20;

Whatever is free, it means you probably are the product.

When you use these, google sees what you do. Then it uses it to provide better ads, maybe sell your search data
{% endhint %}

## 26/07. Intro to PowerShell

### Get-Process

`Get-Process` - Shows processes.&#x20;

`-Name` is optional

### Kill

kill command is an alias to stop process

### Get-Alias

Get all aliases

### Services vs Processes

Services create processes

### Out-File

Out the file to a file.&#x20;

If you do not have permission (in C drive), you won't be able to out and it will give an access denied error

**Out-GridView**

### Type

Type is an alias for Get-Content

