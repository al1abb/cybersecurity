# Example 1: Reporting Stored XSS

`Title`: Stored Cross-Site Scripting (XSS) in X Admin Panel

`CWE`: [CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')](https://cwe.mitre.org/data/definitions/79.html)

`CVSS 3.1 Score`: 5.5 (Medium)

`Description`: During our testing activities, we identified that the "X for administrators" web application is vulnerable to stored cross-site scripting (XSS) attacks due to inadequate sanitization of user-supplied data. Specifically, the file uploading mechanism at "Admin Info" -> "Secure Data Transfer" -> "Load of Data" utilizes a value obtained from user input, specifically the uploaded file's filename, which is not only directly reflected back to the user’s browser but is also stored into the web application’s database. However, this value does not appear to be adequately sanitized. It, therefore, results in the application being vulnerable to reflected and stored cross-site scripting (XSS) attacks since JavaScript code can be entered in the filename field.

`Impact`: Cross-Site Scripting issues occur when an application uses untrusted data supplied by offensive users in a web browser without sufficient prior validation or escaping. A potential attacker can embed untrusted code within a client-side script to be executed by the browser while interpreting the page. Attackers utilize XSS vulnerabilities to execute scripts in a legitimate user's browser leading to user credentials theft, session hijacking, website defacement, or redirection to malicious sites. Anyone that can send data to the system, including administrators, are possible candidates for performing XSS attacks against the vulnerable application. This issue introduces a significant risk since the vulnerability resides in the "X for administrators” web application, and the uploaded files are visible and accessible by every administrator. Consequently, any administrator can be a possible target of a Cross-Site Scripting attack.

`POC`:

Step 1: A malicious administrator could leverage the fact that the filename value is reflected back to the browser and stored in the web application’s database to perform cross-site scripting attacks against other administrators by uploading a file containing malicious JavaScript code into its filename. The attack is feasible because administrators can view all uploaded files regardless of the uploader. Specifically, we named the file, as follows, using a Linux machine.

```javascript
"><svg onload = alert(document.cookie)>.docx
```

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Step 2: When another administrator clicks the view button to open the abovementioned file, the malicious JavaScript code in the file’s filename will be executed on the browser.

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

***

### CVSS Score Breakdown

<table><thead><tr><th width="194.90911865234375"></th><th></th></tr></thead><tbody><tr><td><code>Attack Vector:</code></td><td>Network - The attack can be mounted over the internet.</td></tr><tr><td><code>Attack Complexity:</code></td><td>Low - All the attacker (malicious admin) has to do is specify the XSS payload eventually stored in the database.</td></tr><tr><td><code>Privileges Required:</code></td><td>High - Only someone with admin-level privileges can access the admin panel.</td></tr><tr><td><code>User Interaction:</code></td><td>None - Other admins will be affected simply by browsing a specific (but regularly visited) page within the admin panel.</td></tr><tr><td><code>Scope:</code></td><td>Changed - Since the vulnerable component is the webserver and the impacted component is the browser</td></tr><tr><td><code>Confidentiality:</code></td><td>Low - Access to DOM was possible</td></tr><tr><td><code>Integrity:</code></td><td>Low - Through XSS, we can slightly affect the integrity of an application</td></tr><tr><td><code>Availability:</code></td><td>None - We cannot deny the service through XSS</td></tr></tbody></table>
