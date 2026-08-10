# Outlook- und Mail-Probleme

**Tags:** `#outlook` `#mail` `#exchange` `#kalender` `#spam` `#postfach` `#servicedesk`

## Kurzbeschreibung
Sammlung der häufigsten Outlook- und Mail-Probleme im Support-Alltag: Outlook startet nicht oder hängt, E-Mails werden nicht gesendet oder empfangen, volles Postfach, Kalender- und Freigabeprobleme, fehlerhafte Autovervollständigung, Probleme mit freigegebenen Postfächern, Spam-/Junk-Filterung, Einrichtung der mobilen Mail-App sowie eine nicht funktionierende Outlook-Suche. Jeder Abschnitt folgt dem Schema Symptom → Ursachenanalyse → Lösungsschritte → Eskalationskriterien.

## Voraussetzungen
- Lokaler Administratorzugriff auf dem betroffenen Client
- Zugriff auf das Microsoft 365 Admin Center bzw. Exchange Online (falls Postfach- oder Freigabeprobleme geprüft werden müssen)
- Kenntnis, ob das Konto per Exchange Online, POP3/IMAP oder Hybrid-Exchange angebunden ist
- Zugriff auf Remote-Support-Tool (z. B. Quick Assist, TeamViewer) für Ferndiagnose
- Bei Mobile-Problemen: Zugriff auf Intune bzw. das MDM-Profil des Geräts

## Inhaltsverzeichnis
1. [Outlook startet nicht / hängt beim Laden](#1-outlook-startet-nicht--hängt-beim-laden)
2. [E-Mails werden nicht gesendet oder empfangen](#2-e-mails-werden-nicht-gesendet-oder-empfangen)
3. [Postfach voll / Speicherplatz-Warnung](#3-postfach-voll--speicherplatz-warnung)
4. [Kalender- und Terminprobleme](#4-kalender--und-terminprobleme)
5. [Autovervollständigung funktioniert fehlerhaft](#5-autovervollständigung-funktioniert-fehlerhaft)
6. [Freigegebene Postfächer / Berechtigungsprobleme](#6-freigegebene-postfächer--berechtigungsprobleme)
7. [Spam- / Junk-Mail-Filterung](#7-spam--junk-mail-filterung)
8. [Outlook-Mobile-App-Einrichtung](#8-outlook-mobile-app-einrichtung)
9. [Outlook-Suche liefert keine oder falsche Ergebnisse](#9-outlook-suche-liefert-keine-oder-falsche-ergebnisse)
10. [Remote-Support-Hinweise](#remote-support-hinweise)

---

## 1. Outlook startet nicht / hängt beim Laden

**Priorität:** P2

**Symptom:** Outlook lässt sich nicht öffnen, bleibt beim Startbildschirm oder bei „Profil wird geladen" hängen, oder stürzt unmittelbar nach dem Start wieder ab.

**Erfahrungsbericht:** Bei diesem Ticket hilft es fast immer, zuerst zu fragen, ob Outlook im abgesicherten Modus (`outlook.exe /safe`) startet. Öffnet es sich dort normal, liegt das Problem praktisch immer an einem Add-in – meist ein kürzlich installiertes oder automatisch aktualisierte Add-in eines Drittanbieters (z.B. ein PDF-Add-in oder CRM-Add-in). Bevor man sich also an ein defekte Profil oder eine beschädigte PST/OST-Datei macht, lohnt sich der 2-Minuten-Test im abgesicherten Modus, weil er die Ursache meistens sofort eingrenzt.

**Ursachenanalyse:**
- Prüfen, ob Outlook im abgesicherten Modus startet (`Win + R → outlook.exe /safe`)
- Prüfen, ob kürzlich ein Add-in installiert oder aktualisiert wurde
- Grösse und Zustand der OST-Datei prüfen (sehr grosse oder beschädigte OST-Dateien verlangsamen den Start erheblich)
- Ereignisanzeige (`eventvwr.msc → Windows-Protokolle → Anwendung`) nach Fehlern mit Quelle „Outlook" durchsuchen
- Prüfen, ob das Problem nach einem Windows- oder Office-Update aufgetreten ist.

**Lösungsschritte:**
1. Outlook im abgesicherten Modus starten, um Add-ins als Ursache zu prüfen.
2. Falls  im abgesicherten Modus stabil: Add-ins über `Datei → Optionen → Add-Ins → Verwalten: COM-Add-Ins` einzeln deaktivieren, um den Übeltäter zu identifizieren.
3. Outlook-Profil neu erstellen (`Systemsteuerung → Mail → Profile anzeigen → Neu`).
4. OST-Datei umbenennen bzw. löschen (Outlook geschlossen), damit sie beim nächsten Start neu synchronisiert wird.
5. Office-Reparatur über `Systemsteuerung → Programme und Features → Microsoft 365 → Ändern → Schnellreparatur` ausführen, bei anhaltendem Problem Onlinereparatur.
6. Bei Verdacht auf beschädigtes Windows-Benutzerprofil: Test mit einem neuen, lokalen Testprofil durchführen.

**Eskalationskriterien:**
- Problem besteht auch nach Profil-Neuerstellung und Office-Reparatur weiter
- Mehrere Nutzende gleichzeitig betroffen, insbesondere nach einem zentral verteilten Add-in oder Update → Hinweis auf zentrales Problem

**Eskalation an:** Client-Management-Team bei zentral verteilten Add-ins/Updates, 2nd-Level-Support bei anhaltend beschädigtem Profil

---

## 2. E-Mails werden nicht gesendet oder empfangen

**Priorität:** P2

**Symptom:** Ausgehende E-Mails bleiben im Postausgang stehen, oder es kommen keine neuen E-Mails an, obwohl Outlook als „Verbunden" bzw. „Alle Ordner sind aktuell" angezeigt wird.

**Ursachenanalyse:**
- Verbindungsstatus unten rechts in Outlook prüfen (`Verbunden`, `Getrennt`, `Versuche, eine Verbindung herzustellen`)
- Prüfen, ob das Problem nur beim Senden, nur beim Empfangen oder bei beidem auftritt
- Postausgang auf hängende Nachrichten mit ungewöhnlich grossen Anhängen prüfen
- Prüfen, ob eine Zustellungsfehlermeldung (NDR) für die betroffene E-Mail eingegangen ist, und deren Fehlercode notieren
- Bei Exchange Online: Dienststatus im Microsoft 365 Admin Center bzw. auf status.office.com prüfen
- Prüfen, ob Regeln (`Regeln und Benachrichtigungen`) eingehende Nachrichten automatisch verschieben oder löschen

**Lösungsschritte:**
1. Outlook schliessen und neu starten, danach `Senden/Empfangen → Alle Ordner aktualisieren` (`F9`) ausführen
2. Grösse und Empfängerliste der hängenden Nachricht im Postausgang prüfen; bei Bedarf Anhang verkleinern oder Nachricht löschen und neu senden.
3. Internetverbindung ohne Outlook testen, um eine grundlegende Netzwerk störung auszuschliessen
4. Bei NDR: Fehlercode und -text notieren und mit dem Mail-Team abgleichen (z. B. Verteilerlisten-, Grössen- oder Berechtigungsproblem).
5. Posteingangsregeln auf unerwünschte automatische Verschiebung/Löschung prüfen
7. Cache-Modus testweise deaktivieren bzw. OST-Datei neu synchronisieren lassen, falls nur der lokale Client betroffen scheint.

**Eskalationskriterien:**
- Dienststatus zeigt eine bekannte Störung bei Exchange Online → zentrales Problem, keine Client-seitige Lösung möglich
- Mehrere Nutzende gleichzeitig betroffen, besonders am gleichen Standort oder in derselben Verteilerliste
- NDR verweist auf ein serverseitiges Zustellungsproblem (z. B. Quarantäne, Transportregel)

**Eskalation an:** Mail-/Exchange-Team bei Server- oder Transportproblemen, Netzwerk-Team bei grundlegender Konnektivitätsstörung

---

## 3. Postfach voll / Speicherplatz-Warnung

**Priorität:** P3

**Symptom:** Outlook zeigt eine Warnung zur Postfachgrösse an,neue E-Mails können nicht mehr empfangen werden, oder das Senden schlägt mit einem Hinweis auf Speicherplatzüberschreitung fehl.

**Ursachenanalyse:**
- Aktuelle Postfachgrösse prüfen (`Datei → Kontoeinstellungen → Kontoeinstellungen → Datendateien` bzw. im Outlook Web Access unter Einstellungen)
- Prüfen, ob grosse Anhänge, alte Newsletter oder ein überfüllter „Gesendete Objekte"-/„Gelöschte Objekte"-Ordner die Hauptursache sind
- Prüfen, ob ein Archiveirungsprozess (Autoarchivierung, Online-Archiv) eingerichtet und aktiv ist
- Zugewiesenes Postfachlimit mit der Organisationsrichtlinie abgleichen

**Lösungsschritte:**
1. Grösste Elemente im Postfach identifizieren (`Datei → Extras für Tools → Postfachbereinigung → Grösse anzeigen`) und nicht mehr benötigte grosse Anhänge löschen oder lokal speichern.
2. Ordner „Gelöschte Objekte" und „Junk-E-Mail" leeren.
3. Online-Archiv aktivieren bzw. Autoarchivierungsregeln für ältere E-Mails einrichten, sofern von der Organisation vorgesehen.
4. Grosse Anhänge durch Links auf SharePoint/OneDrive ersetzen, statt sie direkt zu versenden.
5. Bei dauerhaftem Bedarf: Erhöhung des Postfachlimits gemäss Lizenz-/Richtlinienvorgabe beantragen.

**Eskalationskriterien:**
- Zugewiesenes Limit reicht dauerhaft nicht aus undeine Lizenz-/Kontingenterhöhung ist erforderlich
- Archivierungsfunktion lässt sich nicht aktivieren oder synchronisiert nicht

**Eskalation an:** Mail-/Exchange-Team bei Kontingent- oder Archivproblemen, Lizenzverantwortliche bei Bedarf an höherer Lizenzstufe

---

## 4. Kalender- und Terminprobleme

**Priorität:** P3

**Symptom:** Freigegebene Kalender werden nicht angezeigt oder aktualisieren sich nicht, Terminanfragen kommen doppelt oder gar nicht an, oder Teilnehmende sehen unterschiedliche Zeiten für denselben Termin.

**Erfahrungsbericht:** Bei „Teilnehmende sehen unterschiedliche Zeiten"-Tickets lohnt sich als Erstes der Blick auf die Zeitzoneneinstellung des Geräts und des Outlook-Kontos, nicht auf den Termin selbst. Das kommt überraschend oft bei Nutzenden vor, die kürzlich gereist sind oder im Homeoffice ein Gerät mit falsch eingestellter Zeitzone verwenden – der Termin ist technisch korrekt gespeichert, wird aber lokal falsch umgerechnet angezeigt. Ein kurzer Abgleich von `Einstellungen → Zeit und Sprache` mit der tatsächlichen Zeitzone der Person klärt das meist schneller als jede Kalendersynchronisation.

**Ursachenanalyse:**
- Prüfen, ob es sich um einen eigenen, einen delegierten oder einen freigegebenen Kalender handelt
- Berechtigungsstufe auf dem freigegebenen Kalender prüfen (`Nur Verfügbarkeit`, `Details`, `Bearbeiten`)
- Zeitzoneneinstellung von Gerät und Outlook-Konto abgleichen
- Prüfen, ob der Cache-Modus aktiv ist und der Kalender überhaupt synchronisiert wird
- Bei doppelten Terminen: prüfen, ob mehrere Kalender-Apps (z.b. Handy-Kalender + Outlook) gegen dasselbe Konto synchronisieren

**Lösungsschritte:**
1. Zeitzoneneinstellung des Geräts mit dem tatsächlichen Standort abgleichen und bei Bedarf korrigieren.
2. Freigabeberechtigung des betroffenen Kalenders prüfen und bei Bedarf durch die besitzende Person neu erteilen lassen.
3. Kalenderfreigabe entfernen und neu hinzufügen, um eine hängende Synchronisierung zu erzwingen.
4. Cache-Modus-Synchronisierungsstatus prüfen (`Strg + Klick auf Outlook-Symbol im Infobereich → Verbindungsstatus`).
5. Bei doppelten Terminen: doppelte Kalenderverknüpfungen auf Mobilgeräten oder in Drittanbieter-Apps identifizieren und eine davon entfernen.
6. Outlook-Kalender-Add-ins vorübergehend deaktivieren, falls ein Drittanbieter-Tool (z.b. Terminplanungs-Add-in) im Verdacht steht

**Eskalationskriterien:**
- Mehrere Nutzende können denselben freigegebenen Kalender nicht sehen, obwohl die Berechtigung korrekt gesetzt ist → Hinweis auf serverseitiges Problem
- Terminanfragen kommen bei mehreren Nutzenden dupliziert oder gar nicht an

**Eskalation an:** Mail-/Exchange-Team

---

## 5. Autovervollständigung funktioniert fehlerhaft

**Priorität:** P4

**Symptom:** Beim Eintippen einer E-Mail-Adresse schlägt Outlook falsche, veraltete oder gar keine Kontakte mehr vor, oder ein bestimmter, nicht mehr gewünschter Kontakt taucht hartnäckig immer wieder auf.

**Ursachenanalyse:**
- Prüfen, ob es sich um einen einzelnen falschen Eintrag oder ein generelles Fehlen der Autovervollständigung handelt
- Grösse und Zustand der Autovervollständigungs-Cache-Datei (`.NK2`-Äquivalent, bei modernem Outlook Teil des Profils) prüfen
- Prüfen, ob kürzlich ein neues Outlook-Profil erstellt wurde, wodurhc der bisherige Cache verloren gegangen sein könnte

**Lösungsschritte:**
1. Einzelnen fehlerhaften Eintrag direkt im Vorschlag markieren und mit dem „X"-Symbol bzw. `Entf`-Taste entfernen.
2. Bei generellem Ausfall: Outlook schliessen, Autovervollständigungs-Cache über `Datei → Optionen → E-Mail → Nachrichten senden → Automatische Vervollständigung leeren` zurücksetzen.
3. Outlook neu starten und Cache durch Versand einiger test-E-Mails neu aufbauen lassen.
4. Prüfen, ob das globale Adressbuch (GAL) korrekt synchronisiert ist, falls auch dort keine Vorschläge erscheinen.

**Eskalationskriterien:**
- Globales Adressbuch (GAL) zeigt bei mehreren Nutzenden veraltete oder fehlende Einträge → Hinweis auf AD-/Exchange-Synchronisierungsproblem

**Eskalation an:** AD/Identity-Team bei GAL-Synchronisierungsproblemen

---

## 6. Freigegebene Postfächer / Berechtigungsprobleme

**Priorität:** P3

**Symptom:** Ein freigegebenes Postfach wird nicht angezeigt, lässt sich nicht öffnen, oder E-Mails können nicht im Namen des freigegebenen Postfachs gesendet werden, obwohl Berechtigungen vorhanden sein sollten.

**Ursachenanalyse:**
- Prüfen, welche Berechtigungsstufe zugewiesen wurde (`Vollzugriff`, `Senden als`, `Senden im Auftrag von`) – diese verhalten sich unterschiedlich und werden häufig verwechselt
- Prüfen, ob die Berechtigung kürzlich vergeben wurde (Replikation der Berechtigung kann einige Zeit in Anspruch nehmen)
- Prüfen, ob das freigegebene Postfach manuell hinzugefügt wurde oder automatisch über Autodiscover erscheinen sollte
- Bei „Senden als"-Problemen: prüfen, ob die Nachricht dennoch im Postausgang des freigegebenen Postfachs oder des eigenen Kontos hängen bleibt

**Lösungsschritte:**
1. Zugewiesene Berechtigungsstufe im Admin Center bzw. über PowerShell (`Get-MailboxPermission`, `Get-RecipientPermission`) verifizieren.
2. Freigegebenes Postfach manuell in Outlook hinzufügen (`Datei → Konto hinzufügen → Weiteres Postfach öffnen`), falls es nicht automatisch erscheint.
3. Outlook neu starten, um eine aktualisierte autodiscover-Antwort mit den neuen Berechtigungen abzurufen.
4. Bei „Senden als"-Fehlern: prüfen, ob korrekt „Senden als" statt „Senden im Auftrag von" vergeben wurde, da beide unterschiedliche Absenderkennzeichnungen erzeugen.
5. Cache-Modus für das freigegebene Postfach testweise deaktivieren, falls die Synchronisierung ungewöhnlich lange dauert

**Eskalationskriterien:**
- Berechtigung ist laut Admin Center korrekt gesetzt, wirkt sich aber auch nach 24 Stunden und Neustart nicht aus
- Mehrere Personen können auf dasselbe freigegebene Postfach nicht zugreifen →  Hinweis auf ein Problem am Postfach selbst

**Eskalation an:** Mail-/Exchange-Team

---

## 7. Spam- / Junk-Mail-Filterung

**Priorität:** P4

**Symptom:** Legitime E-Mails landen im Junk-Ordner,oder umgekehrt gelangt eindeutiger Spam regelmässig in den Posteingang.

**Ursachenanalyse:**
- Prüfen, ob der Absender bereits auf einer Sperr- oder Zulassungsliste steht (`Datei → Junk → Junk-E-Mail-Optionen`)
- Bei fälschlich blockierten internen Absendern: prüfen, ob eine Transportregel oder eine zu strikte Filterstufe serverseitig greift
- Bei durchrutschendem Spam: Header der betroffenen E-Mail auf Absenderdomäne und SPF/DKIM/DMARC-Ergebnis prüfen
- Prüfen, ob das Problem nur eine Person oder mehrere/die ganze Organisation betrifft

**Lösungsschritte:**
1. Betroffenen Absender in die Liste „Sichere Absender" bzw. „Blockierte Absender" aufnehmen, je nach Fall.
2. Fälschlich einsortierte E-Mail als „Kein Junk" markieren, damit Outlook den Absender künftig korrekt einstuft.
3. Junk-E-Mail-Filterstufe prüfen (`Kein automatisches Filtern`, `Niedrig`, `Hoch`, `Nur Listen`) und bei Bedarf anpassen.
4. Bei durchrutschendem Spam: E-Mail-Header exportieren und dem Mail-Team zur Analyse der SPF/DKIM/DMARC-Ergebnisse weiterleiten.
5. Prüfen, ob eine zentrale Anti-Spam-Richtlinie (z. B. Microsoft Defender for Office 365) den Absender bereits als sicher/unsicher eingestuft hat, und bei Bedarf eine Ausnahme beantragen.

**Eskalationskriterien:**
- Mehrere Nutzende sind vom gleichen Fehlklassifizierungsmuster betroffen → Hinweis auf zentrale Filterregel statt Einzelfall
- Eindeutig bösartige E-Mails (Phishing) erreichen wiederholt den Posteingang trotz aktivem Schutz

**Eskalation an:** Mail-/Security-Team bei zentralen Filterregeln, Security-Team umgehend bei Phishing-Verdacht

---

## 8. Outlook-Mobile-App-Einrichtung

**Priorität:** P3

**Symptom:** Das Firmenkonto lässt sich in der Outlook-App auf dem Smartphone nicht einrichten, die App verlangt wiederholt ein Passwort, oder E-Mails synchronisieren auf dem Mobilgerät nicht.

**Ursachenanalyse:**
- Prüfen, ob das Gerät bereits in Intune registriert ist bzw. registriert werden muss (Voraussetzung für Firmenkonten in vielen Umgebungen)
- Prüfen, ob Multi-Faktor-Authentifizierung korrekt abgeschlossen wurde und nicht an einer abgelaufenen Sitzung scheitert
- Betriebssystemversion des Mobilgerät mit den Mindestanforderungen der Organisation abgleichen
- Prüfen, ob eine Bedingte-Zugriff-Richtlinie (Conditional Access) den Zugriff vom jeweiligen Gerät oder Standort blockiert

**Lösungsschritte:**
1. Outlook-App vollständig entfernen und neu installieren, danach Konto neu hinzufügen.
2. Geräteregistrierung in Intune bzw. dem Firmenportal prüfen und bei Bedarf abschliessen.
3. Anmeldung mit korrektem Benutzernamen-Format (meist E-Mail-Adresse) und aktueler MFA-Bestätigung wiederholen.
4. Datum/Uhrzeit des Mobilgeräts auf automatische Synchronisierung prüfen, da eine falsche Systemzeit Anmeldefehler verursachen kann.
5. Bei Conditional-Access-Verdacht: Fehlermeldung genau notieren (enthält oft einen Richtliniennamen oder eine Fehler-ID) und an das Identity-Team weiterleiten.

**Eskalationskriterien:**
- Anmeldung schlägt trotz korrekter Zugangsdaten und aktueller MFA wiederholt mit einem Conditional-Access-Fehler fehl
- Geräteregistrierung in Intune schlägt wiederholt fehl..

**Eskalation an:** Identity-/Security-Team bei Conditional-Access-Problemen, Client-Management-Team (Intune) bei Registrierungsproblemen

---

## 9. Outlook-Suche liefert keine oder falsche Ergebnisse

**Priorität:** P4

**Symptom:** Die Suche in Outlook findet bekanntermassen vorhandene E-Mails nicht, liefert nur sehr alte Ergebnisse oder bricht mit einer Fehlermeldung ab.

**Ursachenanalyse:**
- Prüfen, ob der Windows Search Index vollständig aufgebaut ist (`Systemsteuerung → Indizierungsoptionen`)
- Prüfen, ob der Suchbereich korrekt eingestellt ist (`Aktueller Ordner`, `Alle Postfächer`), da Nutzende oft im falschen Bereich suchen
- Prüfen, ob Cache-Modus aktiv ist und ob ältere E-Mails eventuell noch nicht lokal heruntergeladen wurden
- Grösse der OST-Datei und freien Speicherplatz auf dem Systemlaufwerk prüfen

**Lösungsschritte:**
1. Suchbereich in der Suchleiste kontrollieren und auf „Alle Postfächer" bzw. „Alle Outlook-Elemente" umstellen.
2. Windows Search Index für Outlook zurücksetzen (`Indizierungsoptionen → Erweitert → Neu erstellen`).
3. Prüfen und sicherstellen, dass genügend freie Speicherplatz für den Index-Aufbau vorhanden ist.
4. Cache-Modus-Zeitraum erhöhen (`Kontoeinstellungen → E-Mail-Konto ändern → Cachemodus-Einstellungen`), falls ältere E-Mails nicht lokal vorliegen.
5. Add-ins testweise deaktivieren, falls eines davon bekanntermassen mit der Suchfunktion in Konflikt steht.

**Eskalationskriterien:**
- Index-Neuaufbau schlägt wiederholt fehl oder dauert unverhältnismässig lange ohne Fortschritt
- Problem tritt bei mehreren Nutzenden gleichzeitig nach demselben Update auf

**Eskalation an:** 2nd-Level-Support bei anhaltenden Indexierungsproblemen, Client-Management-Team bei zentralem Update-Verdacht

---

## Remote-Support-Hinweise

Outlook- und Mail-Probleme lassen sich in den meisten Fällen vollständig remote lösen, da sie selten physische Eingriffe erfordern:

- **Quick Assist / TeamViewer:** geeignet für nahezu alle oben genannten Abschnitte (1–7, 9), z. B. Profil-Neuerstellung, Cache-Reset, Berechtigungsprüfung, Filtereinstellungen
- **Telefonische Anleitung:** ausreichend für einfache Einzelschritte wie das Entfernen eines Autovervollständigungs-Eintrags (Abschnitt 5) oder das Markieren einer E-Mail als „Kein Junk" (Abschnitt 7)
- **Vor-Ort-Support in der Regel nicht nötig**, ausser wenn ein grundlegendes Client-Problem (z. B. beschädigtes Windows-Profil, siehe Windows-Client-Probleme.md) die eigentliche Ursache ist
- Bei der Mobile-App-Einrichtung (Abschnitt 8) ist Bildschirmfreigabe vom Mobilgerät hilfreich, sofern verfügbar, da rein telefonische Anleitung hier oft langsamer zum Ziel führt

**Faustregel:** Bevor man sich in Outlook-spezifische Einstellungen vertieft, lohnt sich immer der kurze Blick auf den zentralen Dienststatus (status.office.com) – viele scheinbare Einzelfälle sind in Wahrheit Teil einer bereits bekannten, grösseren Störung.

---

## Verwandte Themen
- [Windows-Client-Probleme.md](./Windows-Client-Probleme.md)
- [Netzwerk-und-VPN.md](./Netzwerk-und-VPN.md)
- [Drucker-und-Peripherie.md](./Drucker-und-Peripherie.md)
- [Hardware-Diagnose.md](./Hardware-Diagnose.md)

## Changelog
| Datum | Änderung |
|---|---|
| 2026-08-10 | Erste Version |
