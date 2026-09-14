# Fachkern und Portal

System of Record für KCanG-Zahlen: Mitglieder, Chargen, Abgabe, Meldung. Nicht Nextcloud, nicht Matrix, nicht Zammad, nicht Moodle.

## Portal (FastAPI)

**Hauptmenü (Linktree, rollenabhängig):** Chat (Element), CAV, Support (Zammad), Schulungen (Moodle), Cloud nur für Ämter. Zuerst Verein wählen, wenn mehrere Ämter.

**Mein Konto:** Stammdaten (soweit erlaubt), Belege, Token-Stand, Beitrag, grob Schulungsstatus aus Moodle.

Beitragsmodell (änderbar nach Reboot):

- **10 €** im Monat Lastschrift → Tokens
- manuell einzahlen über den Zahlungsdienst → Tokens
- Details: [beitrag-sepa.md](beitrag-sepa.md)

Kein React-SPA am Anfang. Kein Ticketkern, kein LMS im Portal — nur Links und dünne API (Zammad-Vorlage anlegen, Moodle-Abschluss lesen).

## CAV-Kern (FastAPI, Multi-Tenant)

| Modul | Inhalt | Reihenfolge |
| --- | --- | --- |
| Mandant / Verein | `verein_id`, Erlaubnisbezug, 500er-Deckel | 1 |
| Mitglieder | Alter, Status, Rollen, Token-Gruppen | 1 |
| Beitrag / Tokens | Soll, Gutschrift, Beleg; Einzug über Zahlungsdienst | 2 |
| Chargen / Track & Trace | Samen bis Packung, Bestand in Gramm | 3 |
| Abgabe | Limits 50 g / 30 g (18–21), Empfänger, Sorte, THC | 3 |
| Vernichtung / Schwund | inkl. Verdacht Abhandenkommen | 4 |
| Labor | THC/CBD, Charge sperren, Rückruf | 4 |
| §-26-Export | fortlaufend + Jahresmeldung bis 31. Januar, 5 Jahre | 4 |
| Audit-Log | wer hat wann was gebucht, append-only | immer |

Abgabe ist Weitergabe an Mitglieder (Selbstkosten), kein Shop. Zahlungsdienst zieht **Mitgliedsbeitrag**. Tokens intern, getrennt von Gramm-Limits.

## Was nicht selbst geschrieben wird

| Thema | Produkt |
| --- | --- |
| Login, MFA, Gruppen | Keycloak |
| Chat | Synapse + Element |
| Support / Amts-Tickets | Zammad |
| Schulungen, Mitwirkungsnachweis | Moodle |
| Backoffice-Dateien und Office | Nextcloud + Collabora + Kalender |
| HTTPS | Traefik |
| Mailserver | externer Anbieter |
| SEPA-Lastschrift | Mollie / GoCardless / Stripe |
| Finanzbuchhaltung | DATEV / Lexoffice später |
| Zutritt / Kameras / SPS | später Geräte |

## Schnittstellen

- Keycloak → alle Apps: OIDC.
- Keycloak → Gruppenabgleich → Matrix: Join/Kick.
- Portal → Zammad-API: optional Onboarding-Ticket aus Vorlage.
- Portal → Moodle-API: optional Kursabschluss für Mein Konto / interne Regel.
- CAV / Portal → Zahlungsdienst: Mandat, 10-€-Abo, Einzahlung, Webhook → Tokens.
- CAV ↛ Nextcloud für Mitgliederakten.
- Zammad/Moodle ↛ Bestände, Limits, Behördenexport.

## Haftung (kurz)

Die Anbauvereinigung bleibt für KCanG verantwortlich. Software unterstützt Dokumentation, Limits, Schulungsnachweis und Support; sie ersetzt keine Erlaubnis.
