# Extra questions

## Network

SSL/TLS sertifikatının təhlükəsizlik üçün rolu nədir və necə işləyir?&#x20;

L2 attacklardan hansıları bilirsiniz?&#x20;

Firewall və IDS/IPS cihazları arasındakı fərq nədir? Onların təhlükəsizlikdə hansı rolu var?&#x20;

Threat İntelligence və SİEM toolunu qarışdırsaq, SOC analystə köməyi nə olar?&#x20;

Passive reconun qarşısını müəyyən qədər necə ala bilərik? Mac flood attackın digər adı nədir? (ARP flood) proses necə gedir və qarşısının alınması. DHCP starving attack və qarşısının alınması. SYN flood attack və qarşının alınması. DHCP spoofing attack və qarşısının alınması. VLAN hopping attack və qarşısının alınması. ARP attackların qarşısını necə alarsınız? Static, Port sec, Dynamic ARP SOA recordunun içərisində nələr olur? DORA prosesində Offer paketinin içərisində nələr olur? Mikroseqmentasiya nədir və nə üçün istifadə olunur? SOC mühitində Threat Hunting prosesi necə həyata keçirilir və hansı addımlar izlənir?

Hipotez Yaradılması -> Məlumatların Toplanması -> Məlumatların Analizi -> Suspicious dataların araşdırılması -> Response -> Report və sənədləşdirmə aparılması DMZ şəbəkə arxitekturasında necə istifadə olunur və hansı təhlükəsizlik risklərini azaldır? İOC və BİOC fərqləri EDR vs XDR vs Antivirus fərqləri

## MS

MS:

1. Təsəvvür edin ki, hansısa şəbəkəyə RDP ilə qoşulursunuz, bu zaman hansı halda Kerberos, hansı halda NTLM authentication protokolundan istifadə edilir?
2. Active Directory-də Global Kataloq nədir və birdən çox domain-i olan mühitlərdə domain kontrollerlərin işinin effektivliyini necə artırır?
3. Elə bir ssenari danışın ki, forest, tree və domainlərdən hamısından istifadə etmək lazım olsun.
4. SSO, MFA, OTP anlayışları nədir, niyə istifadə olunur?
5. Active Directory-də "Sysvol" niyə önəmlidir və nə işə yarayır?
6. "Domain local", "global" və "universal" qruplar arasındakı fərq nədir və hər növü nə vaxt istifadə edərsən?
7. Pass-the-Hash attackı barədə danış və necə mitigate edərsən?
8. Hansı hallarda NTLM hələ də modern Active Directory-də istifadə olunur və necə bu protokolun istifadəsini minimuma salarsan?
9. NTLMv1 və v2 arasındakı fərq nədir?
10. Köhnə legacy sistemlərdə SSO prosesində NTLM-in rolundan danış və Kerberos əsaslı SSO-dan nə ilə fərqlənir?
11. Bütün Kerberos attackları (kerberoasting, As-rep roasting, golden, silver, diamond ticket) qısaca nə baş verdiyi prosesini danışın və hər birini necə detekt və mitigate edərsiniz?
12. ntds.dir və SAM barədə nə deyə bilərsiniz? SAM necə işləyir və AD databazasından fərqi nədir?
13. SPN (Service Principle Name) Kerberosda rolu nədir və düzgün konfiqurasiya olunmasa, hansı problemlərə yol aça bilər?
14. Workgroup və domain fərqi, konkret situasiya deyin ki nə vaxt hansını istifadə edərsiniz.
15. RADIUS serverin Windows mühitində rolu və AD ilə necə inteqrasiya olunur?
