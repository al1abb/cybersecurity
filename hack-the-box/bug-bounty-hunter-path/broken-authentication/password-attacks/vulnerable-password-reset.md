# Vulnerable Password Reset

We have already discussed how to brute-force password reset tokens to take over a victim's account. However, even if a web application utilizes rate limiting and CAPTCHAs, business logic bugs within the password reset functionality can allow taking over other users' accounts.

***

## Guessable Password Reset Questions

Often, web applications authenticate users who have lost their passwords by requesting that they answer one or multiple security questions. During registration, users provide answers to predefined and generic security questions, disallowing users from entering custom ones. Therefore, within the same web application, the security questions of all users will be the same, allowing attackers to abuse them.

Assuming we had found such functionality on a target website, we should try abusing it to bypass authentication. Often, the weak link in a question-based password reset functionality is the predictability of the answers. It is common to find questions like the following:

* "`What is your mother's maiden name?`"
* "`What city were you born in?`"

While these questions seem tied to the individual user, they can often be obtained through `OSINT` or guessed, given a sufficient number of attempts, i.e., a lack of brute-force protection.

For instance, assuming a web application uses a security question like `What city were you born in?`:

<figure><img src="../../../../.gitbook/assets/image (500).png" alt=""><figcaption></figcaption></figure>

We can attempt to brute-force the answer to this question by using a proper wordlist. There are multiple lists containing large cities in the world. For instance, [this](https://github.com/datasets/world-cities/blob/master/data/world-cities.csv) CSV file contains a list of more than 25,000 cities with more than 15,000 inhabitants from all over the world. This is a great starting point for brute-forcing the city a user was born in.

Since the CSV file contains the city name in the first field, we can create our wordlist containing only the city name on each line using the following command:

```shell-session
al1abb@htb[/htb]$ cat world-cities.csv | cut -d ',' -f1 > city_wordlist.txt

al1abb@htb[/htb]$ wc -l city_wordlist.txt 

26468 city_wordlist.txt
```

As we can see, this results in a total of 26,468 cities.

To set up our brute-force attack, we first need to specify the user we want to target:

<figure><img src="../../../../.gitbook/assets/image (501).png" alt=""><figcaption></figcaption></figure>

As an example, we will target the user `admin`. After specifying the username, we must answer the user's security question. The corresponding request looks like this:

<figure><img src="../../../../.gitbook/assets/image (502).png" alt=""><figcaption></figcaption></figure>

We can set up the corresponding `ffuf` command from this request to brute-force the answer. Keep in mind that we need to specify our session cookie to associate our request with the username `admin` we specified in the previous step:

```shell-session
$ ffuf -w ./city_wordlist.txt -u http://pwreset.htb/security_question.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=39b54j201u3rhu4tab1pvdb4pv" -d "security_response=FUZZ" -fr "Incorrect response."

<SNIP>

[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
    * FUZZ: Houston
```

After obtaining the security response, we can reset the admin user's password and entirely take over the account:

<figure><img src="../../../../.gitbook/assets/image (503).png" alt=""><figcaption></figcaption></figure>
