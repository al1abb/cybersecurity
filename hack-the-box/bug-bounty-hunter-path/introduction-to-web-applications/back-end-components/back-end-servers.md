# Back End Servers

A back-end server is the hardware and operating system on the back end that hosts all of the applications necessary to run the web application. It is the real system running all of the processes and carrying out all of the tasks that make up the entire web application. The back end server would fit in the [Data access layer](https://en.wikipedia.org/wiki/Data_access_layer).

***

## Software

The back end server contains the other 3 back end components:

* `Web Server`
* `Database`
* `Development Framework`

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Other software components on the back end server may include [hypervisors](https://en.wikipedia.org/wiki/Hypervisor), containers, and WAFs.

There are many popular combinations of "stacks" for back-end servers, which contain a specific set of back end components. Some common examples include:

<table><thead><tr><th width="144">Combinations</th><th>Components</th></tr></thead><tbody><tr><td><a href="https://en.wikipedia.org/wiki/LAMP_(software_bundle)">LAMP</a></td><td><code>Linux</code>, <code>Apache</code>, <code>MySQL</code>, and <code>PHP</code>.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/LAMP_(software_bundle)#WAMP">WAMP</a></td><td><code>Windows</code>, <code>Apache</code>, <code>MySQL</code>, and <code>PHP</code>.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Solution_stack">WINS</a></td><td><code>Windows</code>, <code>IIS</code>, <code>.NET</code>, and <code>SQL Server</code></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/MAMP">MAMP</a></td><td><code>macOS</code>, <code>Apache</code>, <code>MySQL</code>, and <code>PHP</code>.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/XAMPP">XAMPP</a></td><td>Cross-Platform, <code>Apache</code>, <code>MySQL</code>, and <code>PHP/PERL</code>.</td></tr></tbody></table>

We can find a comprehensive list of Web Solution Stacks in this [article](https://en.wikipedia.org/wiki/Solution_stack).

***

## Hardware

The back end server contains all of the necessary hardware. The power and performance capabilities of this hardware determine how stable and responsive the web application will be. As previously discussed in the `Architecture` section, many architectures, especially for huge web applications, are designed to distribute their load over many back end servers that collectively work together to perform the same tasks and deliver the web application to the end-user. Web applications do not have to run directly on a single back end server but may utilize data centers and cloud hosting services that provide virtual hosts for the web application.
