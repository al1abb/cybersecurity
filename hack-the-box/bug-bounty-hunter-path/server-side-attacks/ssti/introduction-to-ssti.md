# Introduction to SSTI

As the name suggests, Server-side Template Injection (SSTI) occurs when an attacker can inject templating code into a template that is later rendered by the server. If an attacker injects malicious code, the server potentially executes the code during the rendering process, enabling an attacker to take over the server completely.

***

## Server-side Template Injection

As we have seen in the previous section, the rendering of templates inherently deals with dynamic values provided to the template engine during rendering. Often, these dynamic values are provided by the user. However, template engines can deal with user input securely if provided as values to the rendering function. That is because template engines insert the values into the corresponding places in the template and do not run any code within the values. On the other hand, SSTI occurs when an attacker can control the template parameter, as template engines run the code provided in the template.

If templating is implemented correctly, user input is always provided to the rendering function in values and never in the template string. However, SSTI can occur when user input is inserted into the template **before** the rendering function is called on the template. A different instance would be if a web application calls the rendering function on the same template multiple times. If user input is inserted into the output of the first rendering process, it would be considered part of the template string in the second rendering process, potentially resulting in SSTI. Lastly, web applications enabling users to modify or submit existing templates result in an obvious SSTI vulnerability.
