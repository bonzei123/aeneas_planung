# Mail

**Kein eigener Mailserver.** Kein Mailcow, kein Postfix, keine Spam- und Blacklist-Pflege auf dem Vereins-Host.

Interne Kommunikation läuft über die vorhandenen Kanäle:

- **Matrix** — Mitglieder und Ämter untereinander
- **Zammad** — Support und Amtsaufgaben (eingeloggt)
- **Nextcloud** — nur Backoffice (Kalender, Dateien)
- **Portal / CAV** — Belege, Beitrag, Fachliches

Matrix ersetzt trotzdem nicht jede E-Mail. Es braucht **Postfächer und SMTP bei einem Anbieter** (z. B. mailbox.org oder vergleichbar), die die Apps nur als Versand- und Empfangskanal nutzen.

## Wofür der Anbieter da ist

Ausgang (SMTP), damit Systeme Leute erreichen, die gerade nicht in Element sind:

- Keycloak: Passwort vergessen, Bestätigung
- Zammad: Ticket-Antwort nach außen
- Moodle: Kurserinnerung, wenn gewünscht
- Zahlungsdienst / Portal: Einzug fehlgeschlagen, SEPA-Vorabankündigung

Eingang (IMAP in Zammad), damit die Öffentlichkeit und Mitglieder ohne Login schreiben können, ohne private Vorstands-Mails zu kennen.

Kein Chat, keine Mitgliederversammlung im Mailprogramm. Sobald jemand Mitglied mit Konto ist: Matrix und Zammad im Browser.

## Wer noch kein Mitglied ist

Personen mit Amt müssen erreichbar sein, ohne ihre Privatadresse zu veröffentlichen. Der Weg ist **Zammad**, nicht Element (dafür braucht man schon ein Konto) und nicht die Handynummer auf der Website.

Zwei Eingänge, beide landen in derselben Queue:

1. **Öffentliches Formular** auf der Vereinswebsite / am Portal ohne Login („Kontakt Vorstand“, „Mitglied werden“, „Frage an den Verein“). Zammad legt ein Ticket an, Gruppe z. B. Vorstand Wanne-Eickel oder Gesamtverein-Info.
2. **Funktionspostfächer beim Mailanbieter**, die Zammad abholt, z. B. `info@…`, `vorstand@wanne-eickel.…`. Antwort geht über SMTP vom Verein, Absender die Funktionsadresse.

Der Vorstand arbeitet das Ticket als Agent ab. Die Außenstehende sieht E-Mail oder das Ticket-Portal von Zammad, nicht Matrix. Private Gmail/Telefonticker der Gewählten bleiben intern.

Nach der Aufnahme: Keycloak-Konto, dann Matrix und eingeloggter Support wie alle anderen.

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

## Was nicht veröffentlicht wird

- persönliche Adressen von Vorstand, Prävention, Ausgabe
- Matrix-IDs als einziger Kontakt nach außen
- ein unmoderierter öffentlicher Matrix-Raum als „Support für Nichtmitglieder“ (Spam, keine Akte)

Zammad-Gruppen spiegeln die Ämter. Eine Person mit Posten ist erreichbar, weil Tickets ihrer Gruppe zugewiesen werden, nicht weil ihre Privatmail im Footer steht.
