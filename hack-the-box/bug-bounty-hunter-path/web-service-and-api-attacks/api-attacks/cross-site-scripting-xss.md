# Cross-Site Scripting (XSS)

Cross-Site Scripting (XSS) vulnerabilities affect web applications and APIs alike. An XSS vulnerability may allow an attacker to execute arbitrary JavaScript code within the target's browser and result in complete web application compromise if chained together with other vulnerabilities. Our [Cross-Site Scripting (XSS)](https://academy.hackthebox.com/module/details/103) module covers XSS in detail.

Proceed to the end of this section and click on `Click here to spawn the target system!` or the `Reset Target` icon. Use the provided Pwnbox or a local VM with the supplied VPN key to reach the target API and follow along.

Suppose we are having a better look at the API of the previous section, `http://<TARGET IP>:3000/api/download`.

Let us first interact with it through the browser by requesting the below.

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption><p>http://TARGET_IP:3000/api/download/test_value</p></figcaption></figure>

`test_value` is reflected in the response.

Let us see what happens when we enter a payload such as the below (instead of _test\_value_).

```javascript
<script>alert(document.domain)</script>
```

<figure><img src="../../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

It looks like the application is encoding the submitted payload. We can try URL-encoding our payload once and submitting it again, as follows.

```javascript
%3Cscript%3Ealert%28document.domain%29%3C%2Fscript%3E
```

<figure><img src="../../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Now our submitted JavaScript payload is evaluated successfully. The API endpoint is vulnerable to XSS!
