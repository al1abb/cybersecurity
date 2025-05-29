# Web Servers

A [web server](https://en.wikipedia.org/wiki/Web_server) is an application that runs on the back end server, which handles all of the HTTP traffic from the client-side browser, routes it to the requested pages, and finally responds to the client-side browser. Web servers usually run on TCP [ports](https://en.wikipedia.org/wiki/Port_\(computer_networking\)) `80` or `443`, and are responsible for connecting end-users to various parts of the web application, in addition to handling their various responses.

***

## Workflow

A typical web server accepts HTTP requests from the client-side, and responds with different HTTP responses and codes, like a code `200 OK` response for a successful request, a code `404 NOT FOUND` when requesting pages that do not exist, code `403 FORBIDDEN` for requesting access to restricted pages, and so on.

<figure><img src="../../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

The following are some of the most common [HTTP response codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status):

<table><thead><tr><th width="238.54547119140625">Code</th><th>Description</th></tr></thead><tbody><tr><td><strong>Successful responses</strong></td><td></td></tr><tr><td><code>200 OK</code></td><td>The request has succeeded</td></tr><tr><td><strong>Redirection messages</strong></td><td></td></tr><tr><td><code>301 Moved Permanently</code></td><td>The URL of the requested resource has been changed permanently</td></tr><tr><td><code>302 Found</code></td><td>The URL of the requested resource has been changed temporarily</td></tr><tr><td><strong>Client error responses</strong></td><td></td></tr><tr><td><code>400 Bad Request</code></td><td>The server could not understand the request due to invalid syntax</td></tr><tr><td><code>401 Unauthorized</code></td><td>Unauthenticated attempt to access page</td></tr><tr><td><code>403 Forbidden</code></td><td>The client does not have access rights to the content</td></tr><tr><td><code>404 Not Found</code></td><td>The server can not find the requested resource</td></tr><tr><td><code>405 Method Not Allowed</code></td><td>The request method is known by the server but has been disabled and cannot be used</td></tr><tr><td><code>408 Request Timeout</code></td><td>This response is sent on an idle connection by some servers, even without any previous request by the client</td></tr><tr><td><strong>Server error responses</strong></td><td></td></tr><tr><td><code>500 Internal Server Error</code></td><td>The server has encountered a situation it doesn't know how to handle</td></tr><tr><td><code>502 Bad Gateway</code></td><td>The server, while working as a gateway to get a response needed to handle the request, received an invalid response</td></tr><tr><td><code>504 Gateway Timeout</code></td><td>The server is acting as a gateway and cannot get a response in time</td></tr></tbody></table>

Web servers also accept various types of user input within HTTP requests, including text, [JSON](https://www.w3schools.com/js/js_json_intro.asp), and even binary data (i.e., for file uploads). Once a web server receives a web request, it is then responsible for routing it to its destination, run any processes needed for that request, and return the response to the user on the client-side. The pages and files that the webserver processes and routes traffic to are the web application core files.

The following shows an example of requesting a page in a Linux terminal using the [cURL](https://en.wikipedia.org/wiki/CURL) utility, and receiving the server response while using the `-I` flag, which displays the headers:

