# Schulungen und Mitwirkung

Kein eigenes LMS. **Frappe Learning** (AGPL, Frappe-Framework) mit Keycloak-OIDC. Nicht ERPNext — das bleibt als Portal/Fachkern ausgeschlossen.

Zweck: Mitglieder **nehmen teil** (Mitwirkung, Prävention). Abschlüsse sind der Nachweis. Daran hängen **Türen zu anderen Diensten** (z. B. Chat-Regeln bestanden → Matrix). Das LMS ist Inhalt und Test; es schaltet nichts selbst frei.

## Warum Frappe, nicht Moodle

Moodle ist ein Uni-Campus: PHP-FPM, riesiger Plugin-Zoo, moodledata, oft eigene VM schon bei wenigen hundert gleichzeitigen Lernern. Für Onboarding, Prävention und ein Pflichtquiz ist das unperformant und betrieblich zu teuer — derselbe Grund, aus dem GitLab und BigBlueButton draußen bleiben.

Frappe Learning: Kurse, Quiz, Abschluss, Branding, REST/Webhook. Stack ist MariaDB + Redis + App/Worker, Compose-Overlay, Image pinnen. Immer noch ein Framework, aber kein Campus-LMS.

Nicht: Forma (OIDC nur Kauf-Plugin), LearnHouse (SSO/White-Label Enterprise), ILIAS/OpenOLAT (deutsch, nicht schlanker). Fallback, falls die Frappe-UI auf Deutsch nicht tragbar ist: **Chamilo** (GPL, OIDC-Plugin). Dieselbe Rolle, gleicher Anschluss über CAV.

## Was das LMS liefert

- Kurse (Onboarding, Prävention, interne und Amts-Schulungen)
- Lektionen, Pflichtquiz
- Abschluss pro Person (Fortschritt / Zertifikat)
- Branding: Name, Logo, Farben
- REST-API bzw. Webhook bei Enrollment-Update

Nicht: Rechte für Matrix, CAV oder Nextcloud. Nicht: zweites Mitgliederverzeichnis.

Portal: Link „Schulungen“. Mein Konto zeigt grob offen / bestanden — Quelle ist der **CAV** (Spiegel der Abschlüsse), nicht die LMS-Oberfläche als SoR. Lebende Handbücher ohne Quiz: optionales Wiki, nicht das LMS ([optionale-module.md](optionale-module.md)).

## Türen (CAV + Keycloak)

Nach Abschluss schreibt CAV eine Keycloak-Gruppe `schulung:*` (nicht per Hand, nicht 20k Klicks). Apps und der Matrix-Abgleich lesen nur diese Gruppen plus Status/Rolle.

Vorschlag Katalog (Verein kann interne Regeln enger fassen):

| Kurs | Gruppe nach Abschluss | Tür |
| --- | --- | --- |
| Onboarding | `schulung:onboarding` | CAV-Alltag (nicht nur Belege) |
| Prävention / Jugendschutz (Jahr) | `schulung:praevention` | Abgabe; Mitwirkungsnachweis |
| Chat-Regeln | `schulung:chat` | Matrix / Element |
| Ausgabe (Amt/Dienst) | `schulung:ausgabe` | Abgabefunktion zusätzlich zu `rolle:ausgabe` |
| weitere Amtskurse | `schulung:…` | analog |

`mitgliedschaft:aktiv` allein reicht nicht für Chat oder Abgabe. LMS nur für aktive Mitglieder; `pending` / `beendet` nicht.

Ob ein Jahreskurs vor Satzung und KCanG als Mitwirkung gilt, ist **Vereinsbeschluss**. Software speichert Zeitstempel und Kurs, sie ersetzt keine Rechtsberatung.

## Betrieb

Host `learn.example`. Frappe: typisch MariaDB + Redis + App/Worker, Docker-Overlay in `aeneas_infra`, Image pinnen. OIDC-Client im Realm `aeneas` für `mitgliedschaft:aktiv`. Eine Instanz für alle Zweigvereine, Kurse global oder per Gruppe — nicht 180 LMS.

Backup: Datenbank plus Dateispeicher der Kurse. Kein moodle.org-Netz, kein Frappe-Cloud-Zwang. Moodle-Plugins und moodledata gehören nicht in diese Landschaft.

## Anschluss

1. Mitglied schließt Kurs im LMS ab.
2. Webhook oder Worker liest Abschluss → CAV speichert Nachweis.
3. CAV setzt/entfernt `schulung:*` in Keycloak.
4. Matrix-Gruppenabgleich (und CAV-Fachprüfungen) folgen derselben Gruppe.

LMS-Ausfall darf laufende Chats nicht an der LMS-Session hängen; die Tür ist die Keycloak-Gruppe, bis CAV sie wieder entzieht (z. B. Jahreskurs abgelaufen).
