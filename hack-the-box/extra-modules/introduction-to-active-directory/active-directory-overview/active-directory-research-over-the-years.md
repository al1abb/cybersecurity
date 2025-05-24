# Active Directory Research Over the Years

Active Directory has been a major area of focus for security researchers for the past decade or so. Starting in 2014, we began to see many of the tools and much of the research emerge, leading to the discovery of common attacks and techniques that are still used today. Below is a timeline of events highlighting the discovery of some of the most impactful attacks and flaws by incredible researchers, as well as the release of some of the most widely used tools by penetration testers until this day. While many techniques have been discovered over the years, AD (and now Azure AD) presents a vast attack surface, and new attacks are still being discovered. New tools emerge that both penetration testers and defenders must have a firm grasp of to assist organizations with the difficult but critical task of securing AD environments.

As we can see from the timeline below, critical flaws are continuously being discovered. The noPac attack was discovered in December of 2021 and is the most recent critical AD attack that has been discovered at the time of writing (January of 2022). As we continue into 2022, we will surely see new tools and attacks released and new methods for exploiting and chaining together known vulnerabilities. We must stay on top of the latest and greatest Active Directory research to best help customers secure it or secure it ourselves.

This is by no means a comprehensive list of all the excellent research and tools that have been released over the years, but this is a snapshot of many of the most consequential ones seen over the past decade. The hard work of the researchers listed in this timeline (and many others) has led to remarkable discoveries and the creation of tools that can help both penetration testers and defenders dig deep into Active Directory environments. With them, it's easier to find both obvious and obscure, high-risk flaws before attackers do.

## AD Attacks & Tools Timeline

### 2021

The [PrintNightmare](https://en.wikipedia.org/wiki/PrintNightmare) vulnerability was released. This was a remote code execution flaw in the Windows Print Spooler that could be used to take over hosts in an AD environment. The [Shadow Credentials](https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab) attack was released which allows for low privileged users to impersonate other user and computer accounts if conditions are right, and can be used to escalate privileges in a domain. The [noPac](https://www.secureworks.com/blog/nopac-a-tale-of-two-vulnerabilities-that-could-end-in-ransomware) attack was released in mid-December of 2021 when much of the security world was focused on the Log4j vulnerabilities. This attack allows an attacker to gain full control over a domain from a standard domain user account if the right conditions exist.

### 2020

The [ZeroLogon](https://blog.malwarebytes.com/exploits-and-vulnerabilities/2021/01/the-story-of-zerologon/) attack debuted late in 2020. This was a critical flaw that allowed an attacker to impersonate any unpatched domain controller in a network.

### 2019

harmj0y delivered the talk ["Kerberoasting Revisited"](https://www.slideshare.net/harmj0y/derbycon-2019-kerberoasting-revisited) at DerbyCon which laid out new approaches to Kerberoasting. Elad Shamir released a [blog post](https://shenaniganslabs.io/2019/01/28/Wagging-the-Dog.html) outlining techniques for abusing resource-based constrained delegation (RBCD) in Active Directory. The company BC Security released [Empire 3.0](https://github.com/BC-SECURITY/Empire) (now version 4) which was a re-release of the PowerShell Empire framework written in Python3 with many additions and changes.

### 2018

The "Printer Bug" bug was discovered by Lee Christensen and the [SpoolSample](https://github.com/leechristensen/SpoolSample) PoC tool was released which leverages this bug to coerce Windows hosts to authenticate to other machines via the MS-RPRN RPC interface. harmj0y released the [Rubeus toolkit](https://blog.harmj0y.net/redteaming/from-kekeo-to-rubeus/) for attacking Kerberos. Late in 2018 harmj0y also released the blog ["Not A Security Boundary: Breaking Forest Trusts"](https://blog.harmj0y.net/redteaming/not-a-security-boundary-breaking-forest-trusts/) which presented key research on performing attacks across forest trusts. The [DCShadow](https://www.dcshadow.com/) attack technique was also released by Vincent LE TOUX and Benjamin Delpy at the Bluehat IL 2018 conference. The [Ping Castle](https://github.com/vletoux/pingcastle/commits/master?after=f128d84e86e675f1ad65c4b9b05bd529e1f9dc7c+34\&branch=master) tool was released by Vincent LE TOUX for performing security audits of Active Directory by looking for misconfigurations and other flaws that can raise the risk level of a domain and producing a report that can be used to identify ways to further harden the environment.

### 2017

The [ASREPRoast](https://blog.harmj0y.net/activedirectory/roasting-as-reps/) technique was introduced for attacking user accounts that don't require Kerberos preauthentication. \_wald0 and harmj0y delivered the pivotal talk on Active Directory ACL attacks ["ACE Up the Sleeve"](https://www.slideshare.net/harmj0y/ace-up-the-sleeve) at Black Hat and DEF CON. harmj0y released his ["A Guide to Attacking Domain Trusts"](https://blog.harmj0y.net/redteaming/a-guide-to-attacking-domain-trusts/) blog post on enumerating and attacking domain trusts.

### 2016

[BloodHound](https://wald0.com/?p=68) was released as a game changing tool for visualizing attack paths in AD at [DEF CON 24](https://www.youtube.com/watch?v=wP8ZCczC1OU).

### 2015

2015 saw the release of some of the most impactful Active Directory tools of all time. The [PowerShell Empire framework](https://github.com/EmpireProject/Empire) was released. [PowerView 2.0](https://blog.harmj0y.net/redteaming/powerview-2-0/) released as part of the (now deprecated) [PowerTools](https://github.com/PowerShellEmpire/PowerTools/) repository, which was a part of the PowerShellEmpire GitHub account. The DCSync attack was first released by Benjamin Delpy and Vincent Le Toux as part of the [mimikatz](https://github.com/gentilkiwi/mimikatz/) tool. It has since been included in other tools. The first stable release of CrackMapExec ([(v1.0.0)](https://github.com/byt3bl33d3r/CrackMapExec/releases?page=3) was introduced. Sean Metcalf gave a talk at Black Hat USA about the dangers of Kerberos Unconstrained Delegation and released an excellent [blog post](https://adsecurity.org/?p=1667) on the topic. The [Impacket](https://github.com/SecureAuthCorp/impacket/releases?page=2) toolkit was also released in 2015. This is a collection of Python tools, many of which can be used to perform Active Directory attacks. It is still actively maintained as of January 2022 and is a key part of most every penetration tester's toolkit.

### 2014

Veil-PowerView first [released](https://github.com/darkoperator/Veil-PowerView/commit/fdfd47c0a1e06e529bf31c93da7caed3479d08e1#diff-1695122ff2b5844b625f6d05c9274ce0a8b75b9b7cde84386df07e24ae98181b). This project later became part of the [PowerSploit](https://github.com/PowerShellMafia/PowerSploit) framework as the (no longer supported) [PowerView.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1) AD recon tool. The Kerberoasting attack was first presented at a conference by [Tim Medin](https://twitter.com/timmedin) at SANS Hackfest 2014.

### 2013

The [Responder](https://github.com/SpiderLabs/Responder/commits/master?after=c02c74853298ea52a2bfaa4d250c3898886a44ac+174\&branch=master) tool was released by Laurent Gaffie. Responder is a tool used for poisoning LLMNR, NBT-NS, and MDNS on an Active Directory network. It can be used to obtain password hashes and also perform SMB Relay attacks (when combined with other tools) to move laterally and vertically in an AD environment. It has evolved considerably over the years and is still actively supported (with new features added) as of January 2022.
