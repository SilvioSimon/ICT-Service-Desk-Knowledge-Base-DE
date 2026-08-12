# Hardware-Diagnose

**Tags:** `#hardware` `#diagnose` `#ram` `#datenträger` `#akku` `#mainboard` `#servicedesk`

## Kurzbeschreibung
Sammlung der häufigsten Hardware-Probleme im Support-Alltag: Datenträger- und RAM-Defekte, Überhitzung, Netzteil- und Akkuprobleme, defekte Eingabegeräte, Anschlussprobleme, Mainboard-Verdacht und physische Schäden. Jeder Abschnitt folgt dem Schema Symptom → Ursachenanalyse → Lösungsschritte → Eskalationskriterien. Anders als bei Windows-Client-Problemen endet hier ein grosser Teil der Fälle direkt bei einem Hardware-Austausch – die Lösungsschritte dienen primär dazu, den Defekt zu bestätigen, bevor eskaliert wird.

## Voraussetzungen
- Lokaler Administratorzugriff auf dem betroffenen Client
- Zugriff auf Hersteller-Diagnosetools (z. B. Dell SupportAssist, HP PC Hardware Diagnostics, Lenovo Diagnostics)
- Zugriff auf die Ereignisanzeige (`eventvwr.msc`) für Hinweise auf Hardwarefehler in den Systemprotokollen
- Für Vor-Ort-Diagnosen: Schraubendreher-Set, Ersatzteile/Testkomponenten (Ersatz-RAM-Riegel, Ersatzkabel) falls verfügbar
- Wissen, ob und wie lange das Gerät noch Garantie/Wartungsvertrag hat (relevant für Eskalationsweg)

## Inhaltsverzeichnis
1. [Datenträger-Defekt (HDD/SSD)](#1-datenträger-defekt-hddssd)
2. [RAM-Defekt / Instabilität](#2-ram-defekt--instabilität)
3. [Überhitzung / Lüfterprobleme](#3-überhitzung--lüfterprobleme)
4. [Netzteil-Probleme (Desktop)](#4-netzteil-probleme-desktop)
5. [Akku-Probleme (Notebook)](#5-akku-probleme-notebook)
6. [Tastatur / Touchpad defekt](#6-tastatur--touchpad-defekt)
7. [USB-Anschlüsse funktionieren nicht](#7-usb-anschlüsse-funktionieren-nicht)
8. [Display-Hardwaredefekt](#8-display-hardwaredefekt)
9. [Mainboard-Verdacht / POST-Fehler](#9-mainboard-verdacht--post-fehler)
10. [Ungewöhnliche Geräusche](#10-ungewöhnliche-geräusche)
11. [Ladeproblem (Notebook lädt nicht)](#11-ladeproblem-notebook-lädt-nicht)
12. [Docking Station / externe Peripherie wird nicht erkannt](#12-docking-station--externe-peripherie-wird-nicht-erkannt)
13. [Physische Schäden (Sturz, Flüssigkeit, Transportschaden)](#13-physische-schäden-sturz-flüssigkeit-transportschaden)
14. [Diagnose-Tools der Hersteller (Übersicht)](#14-diagnose-tools-der-hersteller-übersicht)

---

## 1. Datenträger-Defekt (HDD/SSD)

**Priorität:** P2

**Symptom:** System wird zunehmend langsamer, friert bei Dateizugriffen ein, Windows meldet Fehler beim Lesen/Schreiben, oder das Laufwerk wird im BIOS zeitweise nicht erkannt.

**Ursachenanalyse:**
- S.M.A.R.T.-Status auslesen (über Hersteller-Tool oder `wmic diskdrive get status`, besser über Herstellersoftware wie CrystalDiskInfo)
- Ereignisanzeige → `Windows-Protokolle → System` nach Einträgen mit Quelle „disk" oder „Ntfs" durchsuchen
- Prüfen, ob das Problem mit hoher Auslastung oder sehr geringem freiem Speicherplatz zusammenhängt – manche SSDs zeigen abhängig von Modell, Controller und Workload bei sehr hohem Füllstand einen spürbaren Performance-Rückgang; das ist ein Hinweis, aber kein Beleg gegen einen Defekt und sollte nicht ungeprüft als „normal" abgetan werden

**Lösungsschritte:**
1. S.M.A.R.T.-Werte auslesen und auf kritische Attribute prüfen (Reallocated Sectors, Pending Sectors, Wear Level bei SSDs)
2. Herstellerspezifisches Diagnosetool laufen lassen (siehe Punkt 14 – liefert meist zuverlässigere und weniger belastende Ergebnisse als Bordmittel
3. **Bei jedem Hinweis auf einen beginnenden Ausfall  (kritische S.M.A.R.T.-Werte, Klickgeräusche, zeitweises Verschwinden im BIOS) zuerst Datensicherung bzw. Datenrettungsstrategie klären, bevor weitere, potenziell belastende Tests folgen.** `chkdsk /f /r` und vergleichbare Reparaturversuche greifen aktiv in die Datenstruktur ein und können bei einem bereits angeschlagenen Laufwerk den Zustand verschlechtern oder eine spätere Datenrettung erschweren
4. Erst wenn eine Datensicherung erfolgt ist oder das Laufwerk unkritisch erscheint: `chkdsk C: /f /r` zur Prüfung/Markierung von Dateisystemfehlern ausführen
5. Kabelverbindung bei Desktop-Systemen prüfen (SATA-Kabel, Stromkabel) da lockere Verbindungen ähnliche Symptome verursachen können

**Eskalationskriterien:**
- S.M.A.R.T. meldet kritische Werte oder „Pre-Fail"-Status
- Laufwerk wird im BIOS zeitweise oder gar nicht mehr erkannt
- **Eskalation an:** Hardware-Team für Austausch, sofortige Rücksprache mit Nutzenden zur Datensicherung vor jeder weiteren Massnahme

---

## 2. RAM-Defekt / Instabilität

**Priorität:** P2

**Symptom:** Zufällige Abstürze, Bluescreens mit wechselnden Fehlercodes (siehe auch Windows-Client-Probleme, Abschnitt 2), Anwendungen stürzen ohne erkennbares Muster ab, System friert sporadisch ein.

**Erfahrungsbericht:** Ein gutes Erkennungsmerkmal für RAM-Probleme ist, dass der Fehlercode beim Bluescreen jedes Mal ein anderer ist – heute `MEMORY_MANAGEMENT`, morgen `PAGE_FAULT_IN_NONPAGED_AREA`, übermorgen ein komplett anderer. Bei einem Treiberproblem sieht man dagegen fast immer denselben Code wieder und wieder. Wenn also jemand anruft und sagt „der Bluescreen war diesmal wieder anders", ist das für mich meistens schon ein starker Hinweis Richtung RAM, noch bevor ich mdsched überhaupt laufen lasse.

**Ursachenanalyse:**
- Bluescreens auf wechselnde vs. gleichbleibende Fehlercodes prüfen (siehe Erfahrungsbericht oben)
- Prüfen, ob kürzlich RAM aus- oder eingebaut wurde
- Ereignisanzeige nach „WHEA-Logger"-Einträgen durchsuchen

**Lösungsschritte:**
1. Windows-Speicherdiagnose ausführen (`mdsched.exe`), Neustart mit Test abwarten
2. Bei Verdacht auf einen einzelnen defekten Riegel: RAM-Module einzeln testen (Jeweils nur ein Modul eingesteckt, System neu starten)
3. RAM-Riegel neu einsetzen (aus- und wieder einstecken),  Kontakte können sich lösen oder oxidieren
4. Bei mehreren Modulen: Reihenfolge/ Slots tauschen, um Slot-Defekte auf dem Mainboard auszuschliessen
5. Memtest86 als ausführlicheren Test verwenden, falls die eingebaute Windows-Diagnose keinen Fehler findet, das Problem aber weiter besteht

**Eskalationskriterien:**
- Speicherdiagnose meldet einen Fehler.
- Instabilität besteht auch nach Tausch/Neueinsetzen der Modul weiter → möglicher Slot-Defekt auf dem Mainboard.
- **Eskalation an:** Hardware-Team für RAM-Austausch, bei Slot-Verdacht direkt mit Mainboard-Verdacht (Abschnitt 9) eskalieren

---

## 3. Überhitzung / Lüfterprobleme

**Priorität:** P3

**Symptom:** System wird unter Last spürbar langsamer (Throttling), schaltet sich unerwartet ab, Lüfter läuft dauerhaft auf hoher Drehzahl oder ist ungewöhnlich laut.

**Ursachenanalyse:**
- Temperaturen über Hersteller-Tool oder BIOS/UEFI auslesen
- Prüfen, ob Lüftungsschlitze verstaubt oder blockiert sind (z. B. Gerät liegt auf einer weichen Unterlage, Decke, Bett)
- Prüfen, ob das Problem nur unte Last (z. B. Videoconferencing, grosse Excel-Dateien) oder auch im Leerlauf auftritt

**Lösungsschritte:**
1. Gerät auf harter, ebener Fläche mit freien Lüftungsschlitzen testen
2. Lüftungsschlitze und Lüfter auf sichtbaren Staub prüfen, bei Bedarf mit Druckluft reinigen (nur wenn dafür geschult / freigegeben)
3. Temperaturen im Leerlauf und unter Last vergleichen, um normales von auffälligem Verhalten zu unterscheiden
4. Energieeinstellungen prüfen – ein zu aggressives Energiesparprofil kann Lüfterkurven ungünstig beeinflussen
5. BIOS/UEFI auf aktuelle Version prüfen, da Lüftersteuerung teil per Firmware-Update verbessert wird

**Eskalationskriterien:**
- Temperaturen bleiben auch nach Reinigung und auf harter Unterlage kritisch hoch
- Gerät schaltet sich wiederholt wegen Überhitzung selbst ab
- Lüfter macht mechanische Geräusche (siehe auch Abschnitt 10)
- **Eskalation an:** Hardware-Team für Lüfter-/Wärmeleitpasten-Service

---

## 4. Netzteil-Probleme (Desktop)

**Priorität:** P2

**Symptom:** Desktop-PC lässt sich nicht einschalten, schaltet sich beim Hochfahren sofort wieder ab, oder startet nur sporadisch nach mehreren Versuchen.

**Erfahrungsbericht:** 
Bei einem vollständig reaktionslosen Desktop lässt sich aus der Ferne meist nicht sicher zwischen Netzteil-, Mainboard- und Stromversorgungsproblem unterscheiden. Ich prüfe zuerst Stromversorgung und Kabel. Ist ein kompatibles Ersatznetzteil vorhanden, hilft ein Tauschtest bei der Eingrenzung. 
Bleibt der PC auch damit ohne Reaktion, wird der Fall an den Hardware-Service eskaliert.

**Ursachenanalyse:**
- Prüfen, ob überhaupt Strom ankommt: Steckdose, Kabel, Netzteilschalter (oft auf der Rückseite) kontrollieren
- Prüfen, ob das Gehäuselüfter beim Einschaltversuch kurz anläuft (Hinweis, dass grundsätzlich Strom ankommt) oder komplett tot bleibt
- Bei Mehrfachsteckdosen/USV: prüfen, ob diese selbst korrekt funktioniert

**Lösungsschritte:**
1. Stromkabel und Steckdose mit einem anderen Gerät testen, um die Stromquelle auszuschliessen
2. Netzteilschalter und Kabelverbindungen zum Mainboard/zur Grafikkarte prüfen (fester Sitz der Stecker)
3. Wenn verfügbar: ein bekanntermassen funktionierendes Ersatznetzteil einbauen und Startversuch wiederholen (siehe Erfahrungsbericht oben) – das ist der zuverlässigste Weg, Netzteil und Mainboard als Fehlerquelle zu trennen
4. Alternativ, falls vorhanden: Netzteil mit einem Power-Supply-Tester isoliert prüfen
5. Gehäuse öffnen (nur wenn dafür befugt) und auf sichtbare Schäden am Netzteil prüfen (Gerüche, Verfärbungen, aufgeblähte Kondensatoren)

**Eskalationskriterien:**
- Gerät lässt sich auch an einer anderen, geprüften Steckdose nicht einschalten
- Gerät bleibt auch mit nachweislich funktionierendem Ersatznetzteil tot → Verdacht wechselt zum Mainboard (Abschnitt 9)
- Sichtbare Schäden am Netzteil (Geruch, Verfärbung, aufgeblähte Bauteile) → sofort eskalieren, nicht weiter betreiben
- **Eskalation an:** Hardware-Team für Netzteiltausch; bei erfolglosem Netzteiltausch direkt mit Mainboard-Verdacht (Abschnitt 9) koppeln; bei sichtbaren Schäden sofort und ohne weiteren Betriebsversuch

---

## 5. Akku-Probleme (Notebook)

**Priorität:** P3

**Symptom:** Akku entlädt sich ungewöhnlich schnell, Windows zeigt eine stark reduzierte Akkukapazität an, oder das Gerät schaltet sich trotz angezeigter Restladung plötzlich ab.

**Ursachenanalyse:**
- Akkubericht erstellen: `powercfg /batteryreport` in der Eingabeaufforderung, danach `battery-report.html` öffnen
- Im Bericht „Entwurfskapazität" mit „Volle Ladekapazität" vergleichen – eine deutliche Abweichung ist normaler Verschleiss, keine Fehlfunktion an sich
- Prüfen, ob das Problem erst seit einem bestimmten Windows-Update auftritt (gelegentlich betrifft dies die Akkuanzeige, nicht den Akku selbst)

**Lösungsschritte:**
1. Akkubericht erstellen und Kapazitätsverlust auswerten (siehe oben)
2. Energiesparoptionen und Hintergrundaktivität vonApps prüfen, die den Akku unnötig belasten
3. Akkutreiber im Geräte-Manager deinstallieren und Windows neu erkennen lassen (behebt gelegentlich reine Anzeigefehler)
4. Kalibrierung durchführen, falls vom Hersteller empfohlen (vollständig entladen, dann vollständig laden)

**Eskalationskriterien:**
- Kapazitätsverlust liegt deutlich über dem, was für Alter/Nutzung des Geräts zu erwarten wäre
- Gerät schaltet sich trotz angezeigter Restladung unerwartet ab (Sicherheitsrelevanz, insbesondere bei Anzeichen von Aufblähung des Akkus)
- **Eskalation an:** Hardware-Team für Akkutausch; bei Aufblähung oder Beschädigung sofort ausser Betrieb nehmen und melden

---

## 6. Tastatur / Touchpad defekt

**Priorität:** P3

**Symptom:** Einzelne Tasten reagieren nicht oder lösen mehrfach aus, Touchpad reagiert verzögert, gar nicht oder springt beim Tippen.

**Ursachenanalyse:**
- Prüfen, ob das Problem bei einer externen Tastatur/Maus ebenfalls auftritt (Hinweis auf Software- statt Hardwareproblem)
- Prüfen, ob Flüssigkeit oder Fremdkörper ins Gerät gelangt sind
- Treiberstatus im Geräte-Manager prüfen

**Lösungsschritte:**
1. Externe Tastatur/Maus testweise anschliessen, um zwischen Software- und Hardwaredefekt zu unterscheiden
2. Treiber für Tastatur/Touchpad im Geräte-Manager deinstallieren und neu erkennen lassen
3. Tastatureinstellungen prüfen (z.b. Anschlagverzögerung, Anschlagwiederholung unter „Erleichterte Bedienung)
4. Touchpad-Einstellungen im Hersteller-Tool prüfen (Empfindlichkeit, Palm-Rejection)
5. Bei sichtbarer Verschmutzung: vorsichtige Reinigung, sofern dafür freigegeben

**Eskalationskriterien:**
- Problem besteht auch mit externer Tastatur/Maus nicht (bestätigt internes Hardwareproblem)
- Flüssigkeitsschaden wird vermutett oder ist sichtbar
- **Eskalation an:** Hardware-Team für Tastatur-/Touchpad-Austausch

---

## 7. USB-Anschlüsse funktionieren nicht

**Priorität:** P3

**Symptom:** Einzelne oder alle USB-Anschlüsse erkennen angeschlossene Geräte nicht, Geräte werden mit Fehlermeldung „USB-Gerät nicht erkannt" angezeigt, oder die Verbindung bricht sporadisch ab.

**Ursachenanalyse:**
- Prüfen, ob nur ein einzelner Port oder alle Ports betroffen sind
- Prüfen, ob das Problem bei mehreren unterschiedlichen USB-Geräten auftritt (schliesst ein defektes Einzelgerät aus)
- Geräte-Manager auf Fehlermeldungen bei USB-Controllern prüfen (gelbes Ausrufezeichen)

**Lösungsschritte:**
1. Anderes USB-Gerät am selben Port testen, um das angeschlossene Gerät als Fehlerquelle auszuschliessen
2. USB-Controller im Geräte-Manager deinstallieren, Neustart abwarten (Windows installiert automatisch neu)
3. USB-Treiber/Chipsatz-Treiber auf Aktualität prüfen
4. Energieoptionen prüfen: „USB-Selektivsuspendierung" testweise deaktivieren
5. Anschluss auf sichtbare Beschädigung oder Fremdkörper prüfen

**Eskalationskriterien:**
- Nur ein einzelner Port betroffen, andere funktionieren einwandfrei → Hinweis auf physischen Portdefekt
- Alle Ports gleichzeitig betroffen, auch nach Treiber-Neuinstallation → möglicher Mainboard-/Controller-Defekt (siehe Abschnitt 9)
- **Eskalation an:** Hardware-Team

---

## 8. Display-Hardwaredefekt

**Priorität:** P2

**Symptom:** Sichtbare Linien, Flecken oder Verfärbungen auf dem Bildschirm, tote Pixel, Display flackert unabhängig vom Treiber, oder Riss/Bruch im Panel. Abzugrenzen vom „Schwarzer Bildschirm"-Fall in Windows-Client-Probleme, bei dem meist ein Treiber- statt ein physischer Panel-Defekt vorliegt.

**Ursachenanalyse:**
- Prüfen, ob das Fehlerbild auch über einen externen Monitor auftritt (falls ja → Grafikkarte statt Display, siehe Abschnitt 9)
- Prüfen, ob das Problem bei Bewegung/ Druck auf den Bildschirmrahmen zu- oder abnimmt (Hinweis auf lockere Kabelverbindung intern)
- Bei sichtbarem Riss: Ursache erfragen (Sturz, Transport, Druckstelle im Rucksack)

**Lösungsschritte:**
1. Externen Monitor anschliessen, um zwischen internem Panel- und Grafikkartenproblem zu unterscheiden
2. Bildschirmhelligkeit und -kalibrierung zurücksetzen, um Softwareeffekte auszuschliessen
3. Bildwiederholrate probeweise ändern, falls Flackern nur bei bestimmten Einstellungen auftritt
4. Bei Verdacht auf lockeres Kabel: leichten Druck auf verschiedene Bereiche des Rahmens testen (nur wenn sicher und ohne das Gerät zu beschädigen)

**Eskalationskriterien:**
- Sichtbarer physischer Schaden (Riss, Bruch, dauerhafte Verfärbung)
- Fehlerbild besteht auch über externen Monitor unverändert weiter → eher Grafikkarte als Display
- **Eskalation an:** Hardware Team für Displaytausch; bei Grafikkartenverdacht direkt an Mainboard-Verdacht (Abschnitt 9) koppeln

---

## 9. Mainboard-Verdacht / POST-Fehler

**Priorität:** P1

**Symptom:** Gerät startet gar nicht oder bleibt vor dem Windows-Logo hängen (POST schlägt fehl), Signaltöne (Beep-Codes) beim Start, oder mehrere Komponenten fallen gleichzeitig ohne erkennbaren Zusammenhang aus.

**Ursachenanalyse:**
- Beep-Code-Muster notieren (Anzahl und Abstand der Töne), Hersteller-Dokumentation zur Deutung nutzen
- Prüfen, welche Komponente laut POST-Diagnose-LEDs (falls vorhanden) betroffen ist
- Prüfen, ob das Problem nach einem Hardware-Eingriff (RAM-, Grafikkarten-Einbau) oder ohne erkennbaren Auslöser aufgetreten ist

**Lösungsschritte:**
1. Alle nicht zwingend benötigten Komponenten (zusätzliche RAM-Riegel, externe Geräte, zweite Grafikkarte) entfernen und minimalen Systemstart versuchen
2. CMOS-Reset durchführen (Batterie kurz entfernen bzw. Jumper setzen, je nach Hersteller)
3. Komponenten einzeln wieder hinzufügen, um die fehlerhafte Komponente einzugrenzen
4. Bei Notebooks: Hersteller-Diagnose über Boot-Menü ausführen, sofern vor dem eigentlichen Systemstart erreichbar

**Eskalationskriterien:**
- Gerät startet auch im Minimalaufbau nicht
- Beep-Code deutet laut Herstellerdokumentation eindeutig auf Mainboard- oder CPU-Defekt hin
- Mehrere unabhängige Komponenten fallen gleichzeitig aus
- **Eskalation an:** Hardware-Team / Hersteller-Support, bei Garantiefall direkt an Hersteller-RMA-Prozess

---

## 10. Ungewöhnliche Geräusche

**Priorität:** P3

**Symptom:** Klickende, schleifende, klackernde oder pfeifende Geräusche aus dem Gehäuse, meist bei Datenträger- oder Lüfteraktivität.

**Erfahrungsbericht:** Ein wiederkehrendes, rhythmisches Klicken aus einer klassischen HDD (nicht SSD) ist einer der wenigen Fälle im Service Desk, bei denen ich am Telefon sofort zu sofortiger Datensicherung rate, noch bevor irgendein Diagnoseschritt läuft. Das Geräusch bedeutet in aller Regel, dass die Mechanik kurz vor dem Totalausfall steht – manchmal hält die Platte noch Tage, manchmal nur noch Minuten. Ein einmaliges kurzes Klicken beim Hochfahren ist dagegen meist normal (Parken der Leseköpfe) und kein Grund zur Sorge.

**Ursachenanalyse:**
- Geräusch möglichst genau beschreiben lassen (klickend, schleifend, pfeifend, rhythmisch vs. unregelmässig)
- Prüfen, ob es sich um eine klassische HDD oder eine SSD handelt (SSDs haben keine beweglichen Teile, Geräusche kommen dann meist vom Lüfter)
- Prüfen, wann das Geräusch auftritt: durchgehend, nur bei Zugriff, nur unter Last

**Lösungsschritte:**
1. Bei Verdacht auf HDD-Klicken: sofortige Datensicherung einleiten, unabhängig vom weiteren Diagnoseverlauf
2. S.M.A.R.T.-Status prüfen (siehe Abschnitt 1)
3. Lüfter identifizieren, der das Geräusch verursacht (CPU-Lüfter, Gehäuselüfter, Netzteillüfter), oft durch kurzzeitiges Anhalten mit einem nicht-leitenden Gegenstand (nur wenn dafür geschult)
4. Gehäuse auf lose Schrauben oder Kabel prüfen, die vibrieren könnten

**Eskalationskriterien:**
- Rhythmisches Klicken einer HDD → sofortige Eskalation, unabhängig von S.M.A.R.T.Werten
- Geräusch bleibt nach Lüfterreinigung/-austausch bestehen.
- **Eskalation an:** Hardware-Team, bei HDD-Klicken mit Prioritätsvermerk „Datenverlust-Risiko".

---

## 11. Ladeproblem (Notebook lädt nicht)

**Priorität:** P2

**Symptom:** Notebook zeigt „Wird nicht geladen" trotz angeschlossenem Netzteil, lädt nur bei bestimmter Kabelposition, oder Ladeanzeige-LED leuchtet nicht.

**Ursachenanalyse:**
- Prüfen, ob das Netzteil selbst funktioniert (an einem anderen kompatiblen Gerät testen, falls verfügbar)
- Prüfen, ob die Ladebuchse am Notebook lose ist oder nur bei bestimmtem Winkel funktioniert (Hinweis auf mechanischen Defekt)
- Bei USB-C-Ladung: prüfen, ob das verwendete Netzteil/Kabel ausreichend Leistung liefert (nicht jedes USB-C-Kabel unterstützt Power Delivery)

**Lösungsschritte:**
1. Anderes (gleichwertiges) Netzteil testen, um das Netzteil als Fehlerquelle auszuschliessen
2. Ladebuchse auf sichtbare Schäden, Verbiegungen oder Fremdkörper prüfen
3. Akkutreiber im Geräte-Manager deinstallieren und neu erkennen lassen
4. Bei USB-C: anderen Port und anderes Kabel testen, da Power Delivery pro Port unterschiedlich unterstützt sein kann

**Eskalationskriterien:**
- Problem besteht auch mit Nachweislich funktionierendem Ersatznetzteil weiter → Ladebuchse oder internes Ladekabel defekt
- Ladebuchse ist sichtbar beschädigt oder lose
- **Eskalation an:** Hardware-Team für Reparatur der Ladebuchse bzw. Netzteiltausch

---

## 12. Docking Station / externe Peripherie wird nicht erkannt

**Priorität:** P3

**Symptom:** Nach dem Andocken werden externe Monitore, Netzwerk oder angeschlossene Geräte nicht oder nur teilweise erkannt, obwohl die Dockingstation mit Strom versorgt wird.

**Ursachenanalyse:**
- Prüfen, ob das Problem bei dieser einen Dockingstation oder bei mehreren Docks am selben Standort auftritt
- Prüfen, ob das Notebook grundsätzlich mit anderen externen Geräten (direkt, ohne Dock) funktioniert
- Firmware-/Treiberversion der Dockingstation prüfen

**Lösungsschritte:**
1. Notebook vollständig von der Dockingstation trennen (inkl. Stromkabel der Dock kurz ziehen), dann neu verbinden
2. Andere Dockingstation testen, um zwischen Notebook- und Dock-Defekt zu unterscheiden
3. Dock-Treiber/-Firmware über Hersteller-Tool aktualisieren
4. Direktverbindung (ohne Dock) testen, um sicherzustellen, dass Monitor/Netzwerk grundsätzlich funktionieren

**Eskalationskriterien:**
- Problem besteht bei mehreren Docks am gleichen Standort → Hinweis auf Standort-/Stromversorgungsproblem statt Einzelgerätedefekt
- Dockingstation bleibt auch nach Firmware-Update und mit anderem Notebook fehlerhaft
- **Eskalation an:** Hardware-Team für Dock-Austausch, bei Standort-Häufung an Facility-/Netzwerk-Team

---

## 13. Physische Schäden (Sturz, Flüssigkeit, Transportschaden)

**Priorität:** P1

**Symptom:** Gerät ist heruntergefallen, mit Flüssigkeit in Kontakt gekommen oder weist sichtbare äussere Schäden nach dem Transport auf – unabhängig davon, ob es aktuell noch funktioniert.

**Ursachenanalyse:**
- Genauen Hergang erfragen: Art der Flüssigkeit, Fallhöhe, ob das Gerät beim Vorfall eingeschaltet war
- Sichtprüfung auf äussere Schäden (Gehäuse, Anschlüsse, Display)
- Bei Flüssigkeitskontakt: Gerät **nicht** einschalten oder weiter betreiben, falls es zum Zeitpunkt des Kontakts ausgeschaltet war

**Lösungsschritte:**
1. Bei Flüssigkeitskontakt: Gerät sofort ausschalten (falls noch nicht geschehen), Akku wenn möglich trennen, Gerät **nicht** aufladen, einschalten oder eigenmächtig „trockenlegen" (kein Föhn, keine Heizung, kein Reis o. ä.)
2. **Keine pauschale wartezeit als Diagnosekriterium verwenden.** Ob und wann ein Startversuch sicher ist, hängt von Gerät, Flüssigkeitsart und Eindringtiefe ab – das ist Sache des Hardware-Teams bzw. des Hersteller-Reparaturprozesses, nicht eine feste Anzahl Stunden
3. Gerät unverändert (ausgeschaltet, nicht angeschlossen) an das Hardware-Team übergeben bzw. gemäss internem Eskalationsprozess einsenden
4. Bei Sturzschäden ohne Flüssigkeitskontakt: Funktionstest aller Komponenten (Display, Tastatur, Anschlüsse, Akku) durchführen, auch wenn das Gerät auf den ersten Blick normal startet
5. Vorfall dokumentieren (Datum, Hergang, betroffene Komponenten) für Versicherungs-/Garantiezweck

**Eskalationskriterien:**
- Jeder Flüssigkeitskontakt → grundsätzlich sofortige Eskalation, auch wenn das Gerät augenscheinlich noch funktioniert; kein eigenständiger Startversuch im Service Desk
- Sichtbarer struktureller Schaden (Gehäuse, Scharniere, Display)
- **Eskalation an:** Hardware-Team in jedem Fall; weiteres Vorgehen (Trocknung, Wartezeit, Starttest) richtet sich nach dem internen bzw. Hersteller-Reparaturprozess, nicht nach pauschalen Zeitangaben; bei Garantiefall zusätzlich Hersteller-Support, Vorfall gemäss internem Prozess dokumentieren

---

## 14. Diagnose-Tools der Hersteller (Übersicht)

**Priorität:** – (Referenzabschnitt, kein eigenständiges Ticket-Thema)

Für herstellerspezifische Vor-Ort- oder Fernvalidierung von Hardwaredefekten empfiehlt sich grundsätzlich zuerst das jeweilige Herstellertool, da es genauere und meist RMA-fähige Diagnoseergebnisse liefert als Windows-Bordmittel:

| Hersteller | Tool | Zugriff |
|---|---|---|
| Dell | SupportAssist | Vorinstalliert bzw. über Dell-Support-Website |
| HP | HP PC Hardware Diagnostics | Über BIOS/UEFI (F2 beim Start) oder Windows-Version |
| Lenovo | Lenovo Diagnostics / Vantage | Vorinstalliert bzw. über Boot-Menü (Novo-Taste) |
| Microsoft Surface | Surface Diagnostic Toolkit | Über Microsoft-Support-Website |
| Generisch (RAM) | Memtest86 | Bootfähiger USB-Stick, herstellerunabhängig |

**Praxishinweis:** Diagnoseberichte aus diesen Tools (insbesondere Fehlercode von SupportAssist oder HP PC Hardware Diagnostics) sollten bei einer Eskalation immer mit übergeben werden – viele Hersteller verlangen genau diesen Code für die RMA-Abwicklung, was den Prozess spürbar beschleunigt.

---

## Verwandte Themen
- [Windows-Client-Probleme.md](./Windows-Client-Probleme.md)
- [Netzwerk-und-VPN.md](./Netzwerk-und-VPN.md)
- [Drucker-und-Peripherie.md](./Drucker-und-Peripherie.md)
- [../03-Windows-Administration/Registry-Troubleshooting.md](../03-Windows-Administration/Registry-Troubleshooting.md)

## Changelog
| Datum | Änderung |
|---|---|
| 2026-08-12 | Fachliche Korrekturen: Netzteil-Erfahrungsbericht zum Geräte-Tausch als Diagnosemethode (Abschnitt 4) ergänzt; pauschale 90 %-Aussage zu SSD-Performance relativiert (Abschnitt 1); Reihenfolge bei Datenträger-Diagnose angepasst, Datensicherung vor `chkdsk` (Abschnitt 1); pauschale Trocknungszeit bei Flüssigkeitsschaden entfernt, Verweis auf Hersteller-/internen Prozess (Abschnitt 13) |
| 2026-08-12 | Erste Version, 14 Abschnitte |
