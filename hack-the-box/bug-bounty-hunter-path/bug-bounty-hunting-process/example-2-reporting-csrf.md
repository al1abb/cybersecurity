# Example 2: Reporting CSRF

`Title`: Cross-Site Request Forgery (CSRF) in Consumer Registration

`CWE`: [CWE-352: Cross-Site Request Forgery (CSRF)](https://cwe.mitre.org/data/definitions/352.html)

`CVSS 3.1 Score`: 5.4 (Medium)

`Description`: During our testing activities, we identified that the web page responsible for consumer registration is vulnerable to Cross-Site Request Forgery (CSRF) attacks. Cross-Site Request Forgery (CSRF) is an attack where an attacker tricks the victim into loading a page that contains a malicious request. It is malicious in the sense that it inherits the identity and privileges of the victim to perform an undesired function on the victim's behalf, like change the victim's e-mail address, home address, or password, or purchase something. CSRF attacks generally target functions that cause a state change on the server but can also be used to access sensitive data.

`Impact`: The impact of a CSRF flaw varies depending on the nature of the vulnerable functionality. An attacker could effectively perform any operations as the victim. Because the attacker has the victim's identity, the scope of CSRF is limited only by the victim's privileges. Specifically, an attacker can register a fintech application and create an API key as the victim in this case.

`POC`:

Step 1: Using an intercepting proxy, we looked into the request to create a new fintech application. We noticed no anti-CSRF protections being in place.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Step 2: We used the abovementioned request to craft a malicious HTML page that, if visited by a victim with an active session, a cross-site request will be performed, resulting in the advertent creation of an attacker-specific fintech application.

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Step 3: To complete the attack, we would have to send our malicious web page to a victim having an open session. The following image displays the actual cross-site request that would be issued if the victim visited our malicious web page.

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Step 4: The result would be the inadvertent creation of a new fintech application by the victim. It should be noted that this attack could have taken place in the background if combined with finding 6.1.1. <-- 6.1.1 was an XSS vulnerability.

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

***

## CVSS Score Breakdown

<table><thead><tr><th width="210.3636474609375"></th><th></th></tr></thead><tbody><tr><td><code>Attack Vector:</code></td><td>Network - The attack can be mounted over the internet.</td></tr><tr><td><code>Attack Complexity:</code></td><td>Low - All the attacker has to do is trick a user that has an open session into visiting a malicious website.</td></tr><tr><td><code>Privileges Required:</code></td><td>None - The attacker needs no privileges to mount the attack.</td></tr><tr><td><code>User Interaction:</code></td><td>Required - The victim must click a crafted link provided by the attacker.</td></tr><tr><td><code>Scope:</code></td><td>Unchanged - Since the vulnerable component is the webserver and the impacted component is again the webserver.</td></tr><tr><td><code>Confidentiality:</code></td><td>Low - The attacker can create a fintech application and obtain limited information.</td></tr><tr><td><code>Integrity:</code></td><td>Low - The attacker can modify data (create an application) but limitedly and without seriously affecting the vulnerable component's integrity.</td></tr><tr><td><code>Availability:</code></td><td>None - The attacker cannot perform a denial-of-service through this CSRF attack.</td></tr></tbody></table>
