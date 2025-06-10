# HyperText Transfer Protocol (HTTP)

Today, the majority of the applications we use constantly interact with the internet, both web and mobile applications. Most internet communications are made with web requests through the HTTP protocol. [HTTP](https://tools.ietf.org/html/rfc2616) is an application-level protocol used to access the World Wide Web resources. The term `hypertext` stands for text containing links to other resources and text that the readers can easily interpret.

HTTP communication consists of a client and a server, where the client requests the server for a resource. The server processes the requests and returns the requested resource. The default port for HTTP communication is port `80`, though this can be changed to any other port, depending on the web server configuration. The same requests are utilized when we use the internet to visit different websites. We enter a `Fully Qualified Domain Name` (`FQDN`) as a `Uniform Resource Locator` (`URL`) to reach the desired website, like [www.hackthebox.com](http://www.hackthebox.com/).

***

## URL

Resources over HTTP are accessed via a `URL`, which offers many more specifications than simply specifying a website we want to visit. Let's look at the structure of a URL:

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Here is what each component stands for:

<table data-header-hidden><thead><tr><th width="156.272705078125"></th><th width="173.18182373046875"></th><th></th></tr></thead><tbody><tr><td><strong>Component</strong></td><td><strong>Example</strong></td><td><strong>Description</strong></td></tr><tr><td><code>Scheme</code></td><td><code>http://</code> <code>https://</code></td><td>This is used to identify the protocol being accessed by the client, and ends with a colon and a double slash (<code>://</code>)</td></tr><tr><td><code>User Info</code></td><td><code>admin:password@</code></td><td>This is an optional component that contains the credentials (separated by a colon <code>:</code>) used to authenticate to the host, and is separated from the host with an at sign (<code>@</code>)</td></tr><tr><td><code>Host</code></td><td><code>inlanefreight.com</code></td><td>The host signifies the resource location. This can be a hostname or an IP address</td></tr><tr><td><code>Port</code></td><td><code>:80</code></td><td>The <code>Port</code> is separated from the <code>Host</code> by a colon (<code>:</code>). If no port is specified, <code>http</code> schemes default to port <code>80</code> and <code>https</code> default to port <code>443</code></td></tr><tr><td><code>Path</code></td><td><code>/dashboard.php</code></td><td>This points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. <code>index.html</code>).</td></tr><tr><td><code>Query String</code></td><td><code>?login=true</code></td><td>The query string starts with a question mark (<code>?</code>), and consists of a parameter (e.g. <code>login</code>) and a value (e.g. <code>true</code>). Multiple parameters can be separated by an ampersand (<code>&#x26;</code>).</td></tr><tr><td><code>Fragments</code></td><td><code>#status</code></td><td>Fragments are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page).</td></tr></tbody></table>

Not all components are required to access a resource. The main mandatory fields are the scheme and the host, without which the request would have no resource to request.

***

## HTTP Flow

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The diagram above presents the anatomy of an HTTP request at a very high level. The first time a user enters the URL (`inlanefreight.com`) into the browser, it sends a request to a DNS (Domain Name System) server to resolve the domain and get its IP. The DNS server looks up the IP address for `inlanefreight.com` and returns it. All domain names need to be resolved this way, as a server can't communicate without an IP address.

{% hint style="info" %}
**Note:** Our browsers usually first look up records in the local '`/etc/hosts`' file, and if the requested domain does not exist within it, then they would contact other DNS servers. We can use the '`/etc/hosts`' to manually add records to for DNS resolution, by adding the IP followed by the domain name.
{% endhint %}

Once the browser gets the IP address linked to the requested domain, it sends a GET request to the default HTTP port (e.g. `80`), asking for the root `/` path. Then, the web server receives the request and processes it. By default, servers are configured to return an index file when a request for `/` is received.

In this case, the contents of `index.html` are read and returned by the web server as an HTTP response. The response also contains the status code (e.g. `200 OK`), which indicates that the request was successfully processed. The web browser then renders the `index.html` contents and presents it to the user.

{% hint style="info" %}
**Note:** This module is mainly focused on HTTP web requests. For more on HTML and web applications, you may refer to the [Introduction to Web Applications](https://academy.hackthebox.com/module/details/75) module.
{% endhint %}

***

## cURL

In this module, we will be sending web requests through two of the most important tools for any web penetration tester, a Web Browser, like Chrome or Firefox, and the `cURL` command line tool.

[cURL](https://curl.haxx.se/) (client URL) is a command-line tool and library that primarily supports HTTP along with many other protocols. This makes it a good candidate for scripts as well as automation, making it essential for sending various types of web requests from the command line, which is necessary for many types of web penetration tests.

We can send a basic HTTP request to any URL by using it as an argument for cURL, as follows:

```shell-session
$ curl inlanefreight.com

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

We see that cURL does not render the HTML/JavaScript/CSS code, unlike a web browser, but prints it in its raw format. However, as penetration testers, we are mainly interested in the request and response context, which usually becomes much faster and more convenient than a web browser.

We may also use cURL to download a page or a file and output the content into a file using the `-O` flag. If we want to specify the output file name, we can use the `-o` flag and specify the name. Otherwise, we can use `-O` and cURL will use the remote file name, as follows:

```bash
al1abb@htb[/htb]$ curl -O inlanefreight.com/index.html
al1abb@htb[/htb]$ ls
index.html
```

As we can see, the output was not printed this time but rather saved into `index.html`. We noticed that cURL still printed some status while processing the request. We can silent the status with the `-s` flag, as follows:

```shell-session
$ curl -s -O inlanefreight.com/index.html
```

This time, cURL did not print anything, as the output was saved into the `index.html` file. Finally, we may use the `-h` flag to see what other options we may use with cURL:

```shell-session
$ curl -h
Usage: curl [options...] <url>
 -d, --data <data>   HTTP POST data
 -h, --help <category> Get help for commands
 -i, --include       Include protocol response headers in the output
 -o, --output <file> Write to file instead of stdout
 -O, --remote-name   Write output to a file named as the remote file
 -s, --silent        Silent mode
 -u, --user <user:password> Server user and password
 -A, --user-agent <name> Send User-Agent <name> to server
 -v, --verbose       Make the operation more talkative

This is not the full help, this menu is stripped into categories.
Use "--help category" to get an overview of all categories.
Use the user manual `man curl` or the "--help all" flag for all options.
```

As the above message mentions, we may use `--help all` to print a more detailed help menu, or `--help category` (e.g. `-h http`) to print the detailed help of a specific flag. If we ever need to read more detailed documentation, we can use `man curl` to view the full cURL manual page.

In the upcoming sections, we will cover most of the above flags and see where we should use each of them.
