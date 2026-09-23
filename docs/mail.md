# Mail

Betriebsschritte (mailbox.org, Zammad IMAP/SMTP, Keycloak SMTP): `aeneas_infra/MAIL.md`.

**Kein eigener Mailserver.** Kein Mailcow, kein Postfix, keine Spam- und Blacklist-Pflege auf dem Vereins-Host.

Technisch ginge Mailcow/Stalwart auf derselben Kiste. Dann gehört euch PTR, rDNS, SPF, DKIM, DMARC, TLS-RPT, Warmup, Blocklists, Greylisting, Spamassassin, Virus, Queue, Speicher, Updates, **und** die Zustellung zu Gmail/Outlook. Ein neues Hetzner-/netcup-IP startet ohne Reputation — Passwort-Mails und Behördenpost landen im Spam, bis ihr Wochen warmlauft oder auf einer Liste steht. Cannabis-Vereinsdomain macht das nicht leichter. Solo: das ist ein zweiter Fulltime-Job neben Keycloak/Zammad.

Mailbox.org (oder vergleichbar) kostet für einen Verein ein Postfach. Das ist günstiger als ein durchwachtes Wochenende plus eine verbrannte Absender-IP. Mitgliederbriefe trotzdem nicht über denselben Server — egal ob selbst oder gemietet, siehe unten.

Interne Kommunikation läuft über die vorhandenen Kanäle:

- **Matrix** — Mitglieder und Ämter untereinander
- **Zammad** — Support, Ämter, Kontakt ohne Login (Formular)
- **Nextcloud** — nur Backoffice (Kalender, Dateien, Rechte-Ablage)
- optional **Listmonk** — Rundschreiben, Versand weiter über den Mailanbieter
- **Portal / CAV** — Belege, Beitrag, Fachliches

Matrix ersetzt trotzdem nicht jede E-Mail. Es braucht **Postfächer und SMTP bei einem Anbieter** (z. B. mailbox.org oder vergleichbar), die die Apps nur als Versand- und Empfangskanal nutzen.

## Wofür der Anbieter da ist

Ausgang (SMTP), damit Systeme Leute erreichen, die gerade nicht in Element sind:

- Keycloak: Passwort vergessen, Bestätigung
- Zammad: Ticket-Antwort nach außen
- Frappe Learning: Kurserinnerung, wenn gewünscht
- Zahlungsdienst / Portal: Einzug fehlgeschlagen, SEPA-Vorabankündigung

Eingang (IMAP in Zammad), damit die Öffentlichkeit und Mitglieder ohne Login schreiben können, ohne private Vorstands-Mails zu kennen.

Kein Chat, keine Mitgliederversammlung im Mailprogramm. Sobald jemand Mitglied mit Konto ist: Portal und Zammad im Browser; Matrix erst nach Chat-Schulung.

## Wer noch kein Mitglied ist

Personen mit Amt müssen erreichbar sein, ohne ihre Privatadresse zu veröffentlichen. Der Weg ist **Zammad**, nicht Element (dafür braucht man schon ein Konto) und nicht die Handynummer auf der Website.

Zwei Eingänge für **Kontakt**, beide landen in derselben Queue — der Aufnahmeantrag ist kein Zammad-Formular:

1. **Öffentliches Kontaktformular** auf der Vereinswebsite / am Portal ohne Login („Kontakt Vorstand“, „Frage an den Verein“). Zammad legt ein Ticket an, Gruppe z. B. Vorstand Wanne-Eickel oder Gesamtverein-Info.
2. **Funktionspostfächer beim Mailanbieter**, die Zammad abholt, z. B. `info@…`, `vorstand@wanne-eickel.…`. Antwort geht über SMTP vom Verein, Absender die Funktionsadresse.

**Mitglied werden:** eigenes gebrandetes Portal-Formular (wenige Felder, klare Sprache). CAV-Antrag + Keycloak `mitgliedschaft:pending`. Optional intern ein Zammad-Ticket für den Vorstand; die Person sieht keine Ticketmaske.

Der Vorstand arbeitet Kontakt-Tickets als Agent ab. Die Außenstehende sieht E-Mail oder das Zammad-Kundenportal, nicht Matrix. Private Gmail/Telefonticker der Gewählten bleiben intern.

Nach Zusage: `mitgliedschaft:aktiv`. LMS (Onboarding, Prävention, Chat-Regeln). Matrix erst mit `schulung:chat`.

Impressum und Satzung brauchen eine Kontaktmöglichkeit — Formular plus eine Funktionsmail reichen, ein eigener Mailserver nicht.

## Eine Adresse pro Verein, alle beim Anbieter

Behörden (Gesundheitsamt, Ordnungsamt, Finanzamt, Registergericht) schreiben oft per Mail. Dafür braucht **jeder Zweigverein mindestens eine offizielle Adresse**, plus ein paar Funktionskonten beim Gesamtverein.

Die legt ihr **gesammelt bei einem Anbieter** an, nicht auf eurem Server. Ein Vertrag, ein Domain-DNS (`MX` auf den Hoster), viele Postfächer oder Aliase.

Beispiel unter einer Domain:

| Adresse | Zweck | Zammad-Gruppe |
| --- | --- | --- |
| `info@gesamt.example` | Erstkontakt, Presse | Gesamtverein-Info |
| `vorstand@wanne-eickel.example` | Behörden und offizieller Vorstand | Vorstand Wanne-Eickel |
| `vorstand@anderes-dorf.example` | dasselbe für den nächsten Verein | Vorstand anderes Dorf |

Entweder **ein Postfach pro Verein** (Zammad holt jedes per IMAP und sortiert in die richtige Gruppe) oder Aliase, die in wenige Sammelkonten laufen. Getrennte Postfächer sind für Behörden klarer (Absender stimmt, keine Vermischung).

Vorstand liest das nicht in Thunderbird als Hauptarbeit, sondern in **Zammad**. Die Mail existiert, damit Amt und Behörde einen normgerechten Kanal haben.

## Anbieter (Beispiel mailbox.org)

Stand Preise: [mailbox.org/de/preise](https://mailbox.org/de/preise/), ohne Gewähr.

**Eigene Domain: ja.** Domain bleibt beim Registrar. Ab Tarif **Standard** (Business ca. **4 € netto / Postfach / Monat**): MX, SPF, DKIM, DMARC auf mailbox. Light (1 €) ohne eigene Domain, außer Familienaccount — für Vereine ungeeignet.

Für den Gesamtverein: **Business** mit Admin-Konsole, nicht 180 Privatkonten. Service-Paket dazu, z. B. Silber **25 €/Monat** (bis 50 Postfächer) oder Gold **75 €/Monat** (bis 250). Jedes echte Postfach extra nach Light/Standard/Premium. Funktionsadressen möglichst als **ein Postfach plus Aliasse**, nicht jedes Alias als volles Postfach.

**Ein Verein:** ein Standard-Postfach, **5 Funktionsadressen als Aliasse** auf dasselbe Fach (`info@`, `vorstand@`, `praevention@`, …) — Limit Standard **50 Aliasse** je eigener Domain, 5 liegen locker drin. **ca. 4 € netto/Monat**, kein Silber. Zammad holt **ein** IMAP-Konto und sortiert nach `To:` in Gruppen. Fünf volle Postfächer (5×4 €) nur, wenn jemand die Fächer getrennt in Thunderbird braucht — tut ihr laut Planung nicht.

Verschiedene Personen brauchen **keine eigenen IMAP-Fächer**. Amt = Keycloak `rolle:*` plus Zammad-Agent in der passenden Gruppe. Neue Mail an `praevention@` wird Ticket in der Präventions-Queue; wer die Rolle hat, sieht sie (Browser/App, optional Zammad-Benachrichtigung an die Login-Mail). Untereinander: **Matrix**. Nach außen antwortet Zammad mit Absender `praevention@…`, nicht mit der Privatadresse.

**Mitgliederbriefe: nicht über mailbox.** Transactional (Passwort, Ticket, SEPA-Hinweis) ja. Newsletter an hunderte oder 20k Adressen brennt die Domain-Reputation und ist kein Produkt von mailbox (Limits existieren, sind kein Newsletter-Tool). Dafür optional **Listmonk** plus eigenen Versandweg (SMTP des Anbieters nur bei kleinen Listen nach Absprache, sonst ESP). Abmelden und Double-Opt-in gehören dazu, nicht BCC an `vorstand@…`.

## Was nicht veröffentlicht wird

- persönliche Adressen von Vorstand, Prävention, Ausgabe
- Matrix-IDs als einziger Kontakt nach außen
- ein unmoderierter öffentlicher Matrix-Raum als „Support für Nichtmitglieder“ (Spam, keine Akte)

Zammad-Gruppen spiegeln die Ämter. Eine Person mit Posten ist erreichbar, weil Tickets ihrer Gruppe zugewiesen werden, nicht weil ihre Privatmail im Footer steht.
