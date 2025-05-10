# [CHALLENGE_NAME] - [PLATTFORM_NAME] ([SCHWIERIGKEIT])

![[ICON_PFAD_HIER_EINTRAGEN].png]

## Übersicht

*   **VM:** [VM_NAME_HIER_EINTRAGEN]
*   **Plattform:** [[PLATTFORM_NAME_HIER_EINTRAGEN]]([LINK_ZUR_VM_HIER_EINTRAGEN])
*   **Schwierigkeit:** [SCHWIERIGKEIT_HIER_EINTRAGEN]
*   **Autor der VM:** [AUTOR_DER_VM_HIER_EINTRAGEN]
*   **Datum des Writeups:** [DATUM_DES_WRITEUPS_HIER_EINTRAGEN]
*   **Original-Writeup:** [LINK_ZUM_ORIGINAL_WRITEUP_HIER_EINTRAGEN]
*   **Autor:** Ben C.

## Kurzbeschreibung

[HIER_EINE_KURZBESCHREIBUNG_DER_CHALLENGE_UND_DES_LÖSUNGSWEGS_EINFÜGEN. WAS WAR DAS ZIEL? WAS WAREN DIE KERNPUNKTE DER LÖSUNG?]

## Disclaimer / Wichtiger Hinweis

Die in diesem Writeup beschriebenen Techniken und Werkzeuge dienen ausschließlich zu Bildungszwecken im Rahmen von legalen Capture-The-Flag (CTF)-Wettbewerben und Penetrationstests auf Systemen, für die eine ausdrückliche Genehmigung vorliegt. Die Anwendung dieser Methoden auf Systeme ohne Erlaubnis ist illegal. Der Autor übernimmt keine Verantwortung für missbräuchliche Verwendung der hier geteilten Informationen. Handeln Sie stets ethisch und verantwortungsbewusst.

## Verwendete Tools

*   `[TOOL_1]`
*   `[TOOL_2]`
*   `[TOOL_3]`
*   ...
*   Standard Linux-Befehle (`ls`, `cat`, `find`, etc.)

## Lösungsweg (Zusammenfassung)

Der Angriff auf die Maschine "[VM_NAME_HIER_EINTRAGEN]" gliederte sich in folgende Phasen:

1.  **Reconnaissance & Enumeration:**
    *   [HIER_DETAILS_ZUR_AUFKLÄRUNG_EINFÜGEN: IP-Findung, Portscans, erste Service-Informationen]

2.  **[PHASE_2_NAME_Z.B._WEB_ENUMERATION_ODER_SCHWACHSTELLENSUCHE]:**
    *   [HIER_DETAILS_ZUR_ZWEITEN_PHASE_EINFÜGEN: Welche Tools, welche Funde, welche ersten Erkenntnisse?]

3.  **Initial Access ([METHODE_Z.B._LFI_SQLI_EXPLOIT]):**
    *   [HIER_DETAILS_ZUM_INITIALEN_ZUGRIFF_EINFÜGEN: Wie wurde der erste Shell-Zugriff erlangt? Welcher Benutzer?]

4.  **Post-Exploitation / Privilege Escalation (von [USER_A] zu [USER_B]):**
    *   [HIER_DETAILS_ZUR_ERSTEN_RECHTEAUSWEITUNG_ODER_POST_EXPLOITATION_SCHRITTE_EINFÜGEN: Fund von Credentials, SUIDs, sudo-Regeln etc.]

5.  **Privilege Escalation (von [USER_B] zu root):**
    *   [HIER_DETAILS_ZUR_FINALEN_RECHTEAUSWEITUNG_ZU_ROOT_EINFÜGEN: Kernel Exploit, Cronjob, unsichere Konfigurationen etc.]

## Wichtige Schwachstellen und Konzepte

*   **[SCHWACHSTELLE_1_NAME]:** [KURZBESCHREIBUNG_DER_SCHWACHSTELLE_UND_WIE_SIE_AUSGENUTZT_WURDE]
*   **[SCHWACHSTELLE_2_NAME]:** [KURZBESCHREIBUNG_DER_SCHWACHSTELLE_UND_WIE_SIE_AUSGENUTZT_WURDE]
*   **[KONZEPT_1_Z.B._PASSWORD_CRACKING]:** [KURZBESCHREIBUNG_DES_KONZEPTS_IM_KONTEXT_DER_CHALLENGE]
*   ...

## Flags

*   **User Flag (`[PFAD_ZUR_USER_FLAG]`):** `[USER_FLAG_HIER_EINTRAGEN]`
*   **Root Flag (`[PFAD_ZUR_ROOT_FLAG]`):** `[ROOT_FLAG_HIER_EINTRAGEN]`

## Tags

`[PLATTFORM_TAG]`, `[VM_NAME_TAG]`, `[SCHWIERIGKEIT_TAG]`, `[TECHNIK_TAG_1]`, `[TECHNIK_TAG_2]`, `Linux`, `Web`, `Privilege Escalation`
