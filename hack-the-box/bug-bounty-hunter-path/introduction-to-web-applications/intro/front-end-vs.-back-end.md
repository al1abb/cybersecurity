# Front End vs. Back End

We may have heard the [terms](https://en.wikipedia.org/wiki/Front_end_and_back_end) `front end` and `back end` web development, or the term [Full Stack](https://www.w3schools.com/whatis/whatis_fullstack.asp) web development, which refers to both `front` and `back end` web development. These terms are becoming synonymous with web application development, as they comprise the majority of the web development cycle. However, these terms are very different from each other, as each refers to one side of the web application, and each function and communicate in different areas.

***

## Front End

The front end of a web application contains the user's components directly through their web browser (client-side). These components make up the source code of the web page we view when visiting a web application and usually include `HTML`, `CSS`, and `JavaScript`, which is then interpreted in real-time by our browsers.

<figure><img src="../../../../.gitbook/assets/image (442).png" alt=""><figcaption></figcaption></figure>

This includes everything that the user sees and interacts with, like the page's main elements such as the title and text [HTML](https://www.w3schools.com/html/html_intro.asp), the design and animation of all elements [CSS](https://www.w3schools.com/css/css_intro.asp), and what function each part of a page performs [JavaScript](https://www.w3schools.com/js/js_intro.asp).

In modern web applications, front end components should adapt to any screen size and work within any browser on any device. This contrasts with back end components, which are usually written for a specific platform or operating system. If the front end of a web application is not well optimized, it may make the entire web application slow and unresponsive. In this case, some users may think that the hosting server, or their internet, is slow, while the issue lies entirely on the client-side at the user's browser. This is why the front end of a web application must be optimized for most platforms, devices (including mobile!), and screen sizes.

Aside from frontend code development, the following are some of the other tasks related to front end web application development:

* Visual Concept Web Design
* User Interface (UI) design
* User Experience (UX) design

There are many sites available to us to practice front end coding. One example is [this one](https://html-css-js.com/). Here we can play around with the [editor](https://htmlg.com/html-editor/), typing and formatting text and seeing the resulting `HTML`, `CSS`, and `JavaScript` being generated for us. Copy/paste this VERY simple example into the right hand side of the editor:

```html
<p><strong>Welcome to Hack The Box Academy</strong><strong></strong></p>
<p></p>
<p><em>This is some italic text.</em></p>
<p></p>
<p><span style="color: #0000ff;">This is some blue text.</span></p>
<p></p>
<p></p>
```

Watch the simple HTML code render on the left. Play around with the formatting, headers, colors, etc., and watch the code change.

***

## Back End

The back end of a web application drives all of the core web application functionalities, all of which is executed at the back end server, which processes everything required for the web application to run correctly. It is the part we may never see or directly interact with, but a website is just a collection of static web pages without a back end.

There are four main back end components for web applications:

<table data-header-hidden><thead><tr><th width="126.727294921875"></th><th></th></tr></thead><tbody><tr><td><strong>Component</strong></td><td><strong>Description</strong></td></tr><tr><td><code>Back end Servers</code></td><td>The hardware and operating system that hosts all other components and are usually run on operating systems like <code>Linux</code>, <code>Windows</code>, or using <code>Containers</code>.</td></tr><tr><td><code>Web Servers</code></td><td>Web servers handle HTTP requests and connections. Some examples are <code>Apache</code>, <code>NGINX</code>, and <code>IIS</code>.</td></tr><tr><td><code>Databases</code></td><td>Databases (<code>DBs</code>) store and retrieve the web application data. Some examples of relational databases are <code>MySQL</code>, <code>MSSQL</code>, <code>Oracle</code>, <code>PostgreSQL</code>, while examples of non-relational databases include <code>NoSQL</code> and <code>MongoDB</code>.</td></tr><tr><td><code>Development Frameworks</code></td><td>Development Frameworks are used to develop the core Web Application. Some well-known frameworks include <code>Laravel</code> (<code>PHP</code>), <code>ASP.NET</code> (<code>C#</code>), <code>Spring</code> (<code>Java</code>), <code>Django</code> (<code>Python</code>), and <code>Express</code> (<code>NodeJS JavaScript</code>).</td></tr></tbody></table>

<figure><img src="../../../../.gitbook/assets/image (443).png" alt=""><figcaption></figcaption></figure>

It is also possible to host each component of the back end on its own isolated server, or in isolated containers, by utilizing services such as [Docker](https://www.docker.com/). To maintain logical separation and mitigate the impact of vulnerabilities, different components of the web application, such as the database, can be installed in one Docker container, while the main web application is installed in another, thereby isolating each part from potential vulnerabilities that may affect the other container(s). It is also possible to separate each into its dedicated server, which can be more resource-intensive and time-consuming to maintain. Still, it depends on the business case and design/functionality of the web application in question.

Some of the main jobs performed by back end components include:

* Develop the main logic and services of the back end of the web application
* Develop the main code and functionalities of the web application
* Develop and maintain the back end database
* Develop and implement libraries to be used by the web application
* Implement technical/business needs for the web application
* Implement the main [APIs](https://en.wikipedia.org/wiki/API) for front end component communications
* Integrate remote servers and cloud services into the web application

***

## Securing Front/Back End

Even though in most cases, we will not have access to the back end code to analyze the individual functions and the structure of the code, it does not make the application invulnerable. It could still be exploited by various injection attacks, for example.

Suppose we have a search function in a web application that mistakenly does not process our search queries correctly. In that case, we could use specific techniques to manipulate the queries in such a way that we gain unauthorized access to specific database data [SQL injections](https://www.w3schools.com/sql/sql_injection.asp) or even execute operating system commands via the web application, also known as [Command Injections](https://owasp.org/www-community/attacks/Command_Injection).

We will later discuss how to secure each component used on the front and back ends. When we have full access to the source code of front end components, we can perform a code review to find vulnerabilities, which is part of what is referred to as [Whitebox Pentesting](https://en.wikipedia.org/wiki/White-box_testing).

On the other hand, back end components' source code is stored on the back end server, so we do not have access to it by default, which forces us only to perform what is known as [Blackbox Pentesting](https://en.wikipedia.org/wiki/Black-box_testing). However, as we will see, some web applications are open source, meaning we likely have access to their source code. Furthermore, some vulnerabilities such as [Local File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion) could allow us to obtain the source code from the back end server. With this source code in hand, we can then perform a code review on back end components to further understand how the application works, potentially find sensitive data in the source code (such as passwords), and even find vulnerabilities that would be difficult or impossible to find without access to the source code.

The `top 20` most common mistakes web developers make that are essential for us as penetration testers are:

<table data-header-hidden><thead><tr><th width="67.6363525390625"></th><th></th></tr></thead><tbody><tr><td><strong>No.</strong></td><td><strong>Mistake</strong></td></tr><tr><td><code>1.</code></td><td>Permitting Invalid Data to Enter the Database</td></tr><tr><td><code>2.</code></td><td>Focusing on the System as a Whole</td></tr><tr><td><code>3.</code></td><td>Establishing Personally Developed Security Methods</td></tr><tr><td><code>4.</code></td><td>Treating Security to be Your Last Step</td></tr><tr><td><code>5.</code></td><td>Developing Plain Text Password Storage</td></tr><tr><td><code>6.</code></td><td>Creating Weak Passwords</td></tr><tr><td><code>7.</code></td><td>Storing Unencrypted Data in the Database</td></tr><tr><td><code>8.</code></td><td>Depending Excessively on the Client Side</td></tr><tr><td><code>9.</code></td><td>Being Too Optimistic</td></tr><tr><td><code>10.</code></td><td>Permitting Variables via the URL Path Name</td></tr><tr><td><code>11.</code></td><td>Trusting third-party code</td></tr><tr><td><code>12.</code></td><td>Hard-coding backdoor accounts</td></tr><tr><td><code>13.</code></td><td>Unverified SQL injections</td></tr><tr><td><code>14.</code></td><td>Remote file inclusions</td></tr><tr><td><code>15.</code></td><td>Insecure data handling</td></tr><tr><td><code>16.</code></td><td>Failing to encrypt data properly</td></tr><tr><td><code>17.</code></td><td>Not using a secure cryptographic system</td></tr><tr><td><code>18.</code></td><td>Ignoring layer 8</td></tr><tr><td><code>19.</code></td><td>Review user actions</td></tr><tr><td><code>20.</code></td><td>Web Application Firewall misconfigurations</td></tr></tbody></table>

These mistakes lead to the [OWASP Top 10](https://owasp.org/www-project-top-ten/) vulnerabilities for web applications, which we will discuss in other modules:

<table data-header-hidden><thead><tr><th width="68.54547119140625"></th><th></th></tr></thead><tbody><tr><td><strong>No.</strong></td><td><strong>Vulnerability</strong></td></tr><tr><td><code>1.</code></td><td>Broken Access Control</td></tr><tr><td><code>2.</code></td><td>Cryptographic Failures</td></tr><tr><td><code>3.</code></td><td>Injection</td></tr><tr><td><code>4.</code></td><td>Insecure Design</td></tr><tr><td><code>5.</code></td><td>Security Misconfiguration</td></tr><tr><td><code>6.</code></td><td>Vulnerable and Outdated Components</td></tr><tr><td><code>7.</code></td><td>Identification and Authentication Failures</td></tr><tr><td><code>8.</code></td><td>Software and Data Integrity Failures</td></tr><tr><td><code>9.</code></td><td>Security Logging and Monitoring Failures</td></tr><tr><td><code>10.</code></td><td>Server-Side Request Forgery (SSRF)</td></tr></tbody></table>

It is important to begin to familiarize ourselves with these flaws and vulnerabilities as they form the basis for many of the issues we cover in future web and even non-web related modules. As pentesters, we must have the ability to competently identify, exploit, and explain these vulnerabilities for our clients.
