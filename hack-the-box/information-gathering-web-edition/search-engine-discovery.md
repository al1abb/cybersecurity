# Search Engine Discovery

Search engines serve as our guides in the vast landscape of the internet, helping us navigate through the seemingly endless expanse of information. However, beyond their primary function of answering everyday queries, search engines also hold a treasure trove of data that can be invaluable for web reconnaissance and information gathering. This practice, known as search engine discovery or OSINT (Open Source Intelligence) gathering, involves using search engines as powerful tools to uncover information about target websites, organisations, and individuals.

At its core, search engine discovery leverages the immense power of search algorithms to extract data that may not be readily visible on websites. Security professionals and researchers can delve deep into the indexed web by employing specialised search operators, techniques, and tools, uncovering everything from employee information and sensitive documents to hidden login pages and exposed credentials.

## Why Search Engine Discovery Matters

Search engine discovery is a crucial component of web reconnaissance for several reasons:

* `Open Source`: The information gathered is publicly accessible, making it a legal and ethical way to gain insights into a target.
* `Breadth of Information`: Search engines index a vast portion of the web, offering a wide range of potential information sources.
* `Ease of Use`: Search engines are user-friendly and require no specialised technical skills.
* `Cost-Effective`: It's a free and readily available resource for information gathering.

The information you can pull together from Search Engines can be applied in several different ways as well:

* `Security Assessment`: Identifying vulnerabilities, exposed data, and potential attack vectors.
* `Competitive Intelligence`: Gathering information about competitors' products, services, and strategies.
* `Investigative Journalism`: Uncovering hidden connections, financial transactions, and unethical practices.
* `Threat Intelligence`: Identifying emerging threats, tracking malicious actors, and predicting potential attacks.

However, it's important to note that search engine discovery has limitations. Search engines do not index all information, and some data may be deliberately hidden or protected.

## Search Operators

Search operators are like search engines' secret codes. These special commands and modifiers unlock a new level of precision and control, allowing you to pinpoint specific types of information amidst the vastness of the indexed web.

While the exact syntax may vary slightly between search engines, the underlying principles remain consistent. Let's delve into some essential and advanced search operators:

<table><thead><tr><th width="147">Operator</th><th>Operator Description</th><th>Example</th><th>Example Description</th></tr></thead><tbody><tr><td><code>site:</code></td><td>Limits results to a specific website or domain.</td><td><code>site:example.com</code></td><td>Find all publicly accessible pages on example.com.</td></tr><tr><td><code>inurl:</code></td><td>Finds pages with a specific term in the URL.</td><td><code>inurl:login</code></td><td>Search for login pages on any website.</td></tr><tr><td><code>filetype:</code></td><td>Searches for files of a particular type.</td><td><code>filetype:pdf</code></td><td>Find downloadable PDF documents.</td></tr><tr><td><code>intitle:</code></td><td>Finds pages with a specific term in the title.</td><td><code>intitle:"confidential report"</code></td><td>Look for documents titled "confidential report" or similar variations.</td></tr><tr><td><code>intext:</code> or <code>inbody:</code></td><td>Searches for a term within the body text of pages.</td><td><code>intext:"password reset"</code></td><td>Identify webpages containing the term “password reset”.</td></tr><tr><td><code>cache:</code></td><td>Displays the cached version of a webpage (if available).</td><td><code>cache:example.com</code></td><td>View the cached version of example.com to see its previous content.</td></tr><tr><td><code>link:</code></td><td>Finds pages that link to a specific webpage.</td><td><code>link:example.com</code></td><td>Identify websites linking to example.com.</td></tr><tr><td><code>related:</code></td><td>Finds websites related to a specific webpage.</td><td><code>related:example.com</code></td><td>Discover websites similar to example.com.</td></tr><tr><td><code>info:</code></td><td>Provides a summary of information about a webpage.</td><td><code>info:example.com</code></td><td>Get basic details about example.com, such as its title and description.</td></tr><tr><td><code>define:</code></td><td>Provides definitions of a word or phrase.</td><td><code>define:phishing</code></td><td>Get a definition of "phishing" from various sources.</td></tr><tr><td><code>numrange:</code></td><td>Searches for numbers within a specific range.</td><td><code>site:example.com numrange:1000-2000</code></td><td>Find pages on example.com containing numbers between 1000 and 2000.</td></tr><tr><td><code>allintext:</code></td><td>Finds pages containing all specified words in the body text.</td><td><code>allintext:admin password reset</code></td><td>Search for pages containing both "admin" and "password reset" in the body text.</td></tr><tr><td><code>allinurl:</code></td><td>Finds pages containing all specified words in the URL.</td><td><code>allinurl:admin panel</code></td><td>Look for pages with "admin" and "panel" in the URL.</td></tr><tr><td><code>allintitle:</code></td><td>Finds pages containing all specified words in the title.</td><td><code>allintitle:confidential report 2023</code></td><td>Search for pages with "confidential," "report," and "2023" in the title.</td></tr><tr><td><code>AND</code></td><td>Narrows results by requiring all terms to be present.</td><td><code>site:example.com AND (inurl:admin OR inurl:login)</code></td><td>Find admin or login pages specifically on example.com.</td></tr><tr><td><code>OR</code></td><td>Broadens results by including pages with any of the terms.</td><td><code>"linux" OR "ubuntu" OR "debian"</code></td><td>Search for webpages mentioning Linux, Ubuntu, or Debian.</td></tr><tr><td><code>NOT</code></td><td>Excludes results containing the specified term.</td><td><code>site:bank.com NOT inurl:login</code></td><td>Find pages on bank.com excluding login pages.</td></tr><tr><td><code>*</code> (wildcard)</td><td>Represents any character or word.</td><td><code>site:socialnetwork.com filetype:pdf user* manual</code></td><td>Search for user manuals (user guide, user handbook) in PDF format on socialnetwork.com.</td></tr><tr><td><code>..</code> (range search)</td><td>Finds results within a specified numerical range.</td><td><code>site:ecommerce.com "price" 100..500</code></td><td>Look for products priced between 100 and 500 on an e-commerce website.</td></tr><tr><td><code>" "</code> (quotation marks)</td><td>Searches for exact phrases.</td><td><code>"information security policy"</code></td><td>Find documents mentioning the exact phrase "information security policy".</td></tr><tr><td><code>-</code> (minus sign)</td><td>Excludes terms from the search results.</td><td><code>site:news.com -inurl:sports</code></td><td>Search for news articles on news.com excluding sports-related content.</td></tr></tbody></table>

## Google Dorking

Google Dorking, also known as Google Hacking, is a technique that leverages the power of search operators to uncover sensitive information, security vulnerabilities, or hidden content on websites, using Google Search.

Here are some common examples of Google Dorks, for more examples, refer to the [Google Hacking Database](https://www.exploit-db.com/google-hacking-database):

* Finding Login Pages:
  * `site:example.com inurl:login`
  * `site:example.com (inurl:login OR inurl:admin)`
* Identifying Exposed Files:
  * `site:example.com filetype:pdf`
  * `site:example.com (filetype:xls OR filetype:docx)`
* Uncovering Configuration Files:
  * `site:example.com inurl:config.php`
  * `site:example.com (ext:conf OR ext:cnf)` (searches for extensions commonly used for configuration files)
* Locating Database Backups:
  * `site:example.com inurl:backup`
  * `site:example.com filetype:sql`
