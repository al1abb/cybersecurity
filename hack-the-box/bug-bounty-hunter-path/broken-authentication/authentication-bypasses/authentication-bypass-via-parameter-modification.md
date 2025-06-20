# Authentication Bypass via Parameter Modification

An authentication implementation can be flawed if it depends on the presence or value of an HTTP parameter, introducing authentication vulnerabilities. As in the previous section, such vulnerabilities might lead to authentication and authorization bypasses, allowing for privilege escalation.

This type of vulnerability is closely related to authorization issues such as `Insecure Direct Object Reference (IDOR)` vulnerabilities, which are covered in more detail in the [Web Attacks](https://academy.hackthebox.com/module/details/134) module.

***

## Parameter Modification

Let us take a look at our target web application. This time, we are provided with credentials for the user `htb-stdnt`. After logging in, we are redirected to `/admin.php?user_id=183`:

<figure><img src="../../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

In our web browser, we can see that we seem to be lacking privileges, as we can only see a part of the available data:

<figure><img src="../../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

To investigate the purpose of the `user_id` parameter, let us remove it from our request to `/admin.php`. When doing so, we are redirected back to the login screen at `/index.php`, even though our session provided in the `PHPSESSID` cookie is still valid:

<figure><img src="../../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Thus, we can assume that the parameter `user_id` is related to authentication. We can bypass authentication entirely by accessing the URL `/admin.php?user_id=183` directly:

<figure><img src="../../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Based on the parameter name `user_id`, we can infer that the parameter specifies the ID of the user accessing the page. If we can guess or brute-force the user ID of an administrator, we might be able to access the page with administrative privileges, thus revealing the admin information. We can use the techniques discussed in the `Brute-Force Attacks` sections to obtain an administrator ID. Afterward, we can obtain administrative privileges by specifying the admin's user ID in the `user_id` parameter.

***

## Final Remark

Note that many more advanced vulnerabilities can also lead to an authentication bypass, which we have not covered in this module but are covered by more advanced modules. For instance, Type Juggling leading to an authentication bypass is covered in the [Whitebox Attacks](https://academy.hackthebox.com/module/details/205) module, how different injection vulnerabilities can lead to an authentication bypass is covered in the [Injection Attacks](https://academy.hackthebox.com/module/details/204) and [SQL Injection Fundamentals](https://academy.hackthebox.com/module/details/33) modules, and logic bugs that can lead to an authentication bypass are covered in the [Parameter Logic Bugs](https://academy.hackthebox.com/module/details/239) module.
