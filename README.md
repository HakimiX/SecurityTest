## Security Tests & Vulnerabilities 

I denne gennemgang gøre vi brug af Kali Linux som gør hacking meget lettere. Kali Linux indeholder mere end 300 præinstallerede værktøjer til hacking og er en af de mest populære programmer til hacking og penetration testing. Personer uden programmerings baggrund kan nemt følge Kali Linux tutorials på internettet og få adgang til følsomme oplysninger. 

Vi starter med at ”Stealth Scan” Hackernews siden som ligger på følgende adresse: `165.227.136.184`. Stealth Scanning er en velkendt måde til at opdage kommunikationskanaler og udnytte dem. Et virkeligt eksempel på Stealth Scanning kunne være, at gå op til en lejlighedsbygning og banke på hver dør for at se, hvem der er hjemme. Stealth Scanning stiller eksempelvis følgende spørgsmål til webserveren: hvilken webserver kørere du? Bruger du en mail server? Bruger du Telnet, FTP eller RCP? Webserveren afslører desværre mange af disse informationer og det tillader en uberettiget besøgende at få uautoriseret adgang til webserveren. Vi har gjort brug af værktøjet NMAP til at få adgang til følsomme oplysninger. Vi kan se hvilken webserver der er brugt og hvilke TCP og HTTP porte der er åbne og tilgængelige. Kali Linux Terminal Kommando - `nmap -v 165.227.136.184`

![Text](https://github.com/HakimiX/UFO/blob/master/Models/StealthScanPorts.jpg)

Derudover kan vi få adgang til SSH-Host Key. Hver computer har en unik Host Key, som er en kryptografisk nøgle, der bruges til at godkende computere. Deling af SSH-Host Key er ikke anbefalet da det kan resultere i sårbarhed over for en ”Man-in-the-Middle” (MITM) angreb. Man-in-the-middle er et angreb mod en kryptografisk protokol så hackeren kan forhandle forskellige kryptografiske parametre med webserveren og klienten. Kali Linux Terminal Kommando - `nmap –A 165.227.136.184`. 

![Text](https://github.com/HakimiX/UFO/blob/master/Models/StealthScanSSH.jpg)

Vi har også udført et DoS-angreb (Denial-of-service-attack) mod webserveren vha. værktøjerne Scatter og Hping3. Formålet med et DoS angreb er at gøre webserveren utilgængelig for sine tilsigtede brugere ved at overbelaste den med trafik. DoS-angrebet medførte at Hackernews siden blev en del langsommere og reagerede ikke til nogle anmodninger. Kali Linux Terminal Kommando - `hping3 – S –V –flood –rand-source 165.227.136.184`.

![Text](https://github.com/HakimiX/UFO/blob/master/Models/hping3.jpg)
![Text](https://github.com/HakimiX/UFO/blob/master/Models/hpin3result.jpg)

Truslerne kan komme i mange forskellige former og ovenstående er blot eksempler. Så snart de ondsindede hackere har adgang til webserveren, er brugernes følsomme oplysninger udsat. 

## OWASP ZAP Sikkerhedstest

En sikkerhedstest analysere din hjemmesides kode og hvordan den interagere med andre objekter for at identificere svagheder eller fejl, så vi kan forhindre at ondsindede hackere ikke får uautoriseret adgang til webserver eller andre netværk tilknyttet vores webserver. Vi har gjort brug af OWASP Zed Attack Proxy (ZAP) sikkerhedsværktøj til finde sikkerhedsfejl i vores Hackernews applikation. Nedenstående er en demonstration af OWASP ZAP Scan af vores system - `165.227.136.184`

![Text](https://github.com/HakimiX/UFO/blob/master/Models/OWASP2.jpg)

I OWASP’s brugergrænsefladen kan vi se at vores applikation er udsat for ”Clickjacking” angreb. Clickjacking angreb er når en hacker benytter gennemsigtige eller uigennemsigtige lag til at narre brugere til at klikke på et link eller en knap på en anden side, selvom brugerens hensigt var at klikke på øverste lag. 

> ”X-Frame-Options header is not included in the HTTP response to protect against Clickjacking attacks” - OWASP ZAP

![Text](https://github.com/HakimiX/UFO/blob/master/Models/OWASP1.jpg)

OWASP gør os opmærksom på at vores web browser XSS beskyttelse ikke er aktiveret. HTTP XXS beskyttelses response header er en funktion af Chrome og Safari, der har til formål at stoppe sider der indlæses med reflekterende XXS angreb (Cross-site scripting). Cross-site scripting er når en hacker påfører din hjemmeside ondsindede scripts. Via din hjemmeside kan en hacker påfører ondsindede scripts til dine kunders browser.  

> ”Web Browser XSS Protection is not enabled, or is disabled by the configuration of the X-XSS-Protection http response header on the web server” - OWASP ZAP