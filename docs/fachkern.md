# Fachkern und Portal

System of Record für KCanG-Zahlen: Mitglieder, Chargen, Abgabe, Meldung. Nicht Nextcloud, nicht Matrix, nicht Zammad, nicht das LMS. Nachweise der Schulung speichert der CAV (Türen); das LMS ist nur Inhalt und Test.

## Portal (FastAPI)

**Hauptmenü (Linktree, rollenabhängig):** Chat (Element, nur mit `schulung:chat`), CAV, Support (Zammad), Schulungen (Frappe Learning), Cloud nur für Ämter, optionale Overlays nur wenn eingeschaltet. Zuerst Verein wählen, wenn mehrere Ämter.

**Aufnahme:** gebrandetes Formular auf der Vereinsseite / am Portal, idiotensicher, ohne Zammad-Oberfläche. Absenden legt CAV-Antrag plus Keycloak `mitgliedschaft:pending` an. Optional ein internes Zammad-Ticket für den Vorstand — die antragstellende Person sieht das nicht.

**Mein Konto:** Stammdaten (soweit erlaubt), Belege, Token-Stand, Beitrag, grob Schulungsstatus aus dem CAV.

Beitragsmodell (änderbar nach Reboot):

- **10 €** im Monat Lastschrift → Tokens
- manuell einzahlen über den Zahlungsdienst → Tokens
- Details: [beitrag-sepa.md](beitrag-sepa.md)

Kein React-SPA am Anfang. Kein Ticketkern, kein LMS im Portal — nur Links, Aufnahmeformular und dünne API (optional Amts-Ticket in Zammad, Abschlüsse kommen vom LMS-Webhook in den CAV).

## CAV-Kern (FastAPI, Multi-Tenant)

| Modul | Inhalt | Reihenfolge |
| --- | --- | --- |
| Mandant / Verein | `verein_id`, Erlaubnisbezug, 500er-Deckel; Tenant-Paket raus/rein | 1 |
| Mitglieder | Alter, Status, Rollen, Token-Gruppen; Schlüssel `mitglied_id` | 1 |
| Schulungsnachweis | Kurs, Zeitstempel, spiegelt `schulung:*` nach Keycloak | 1 |
| Beitrag / Tokens | Soll, Gutschrift, Beleg; Einzug über Zahlungsdienst | 2 |
| Chargen / Track & Trace | Samen bis Packung, Bestand in Gramm | 3 |
| Abgabe | Limits 50 g / 30 g (18–21), Empfänger, Sorte, THC; nur mit Präventionsabschluss laut Vereinspolitik | 3 |
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
| Schulungen, Quiz, Kursinhalt | Frappe Learning (nicht Moodle) |
| Backoffice-Dateien, Rechte-Ablage, Office, Scan | Nextcloud + Collabora + Group Folders + Kalender |
| Mitglieder-Handbuch | optional BookStack; FAQs: Zammad-KB |
| Video | optional Jitsi |
| HTTPS | Traefik |
| Mailserver | externer Anbieter |
| SEPA-Lastschrift | Mollie / GoCardless / Stripe |
| Finanzbuchhaltung | DATEV / Lexoffice später (nicht FOSS) |
| Eingangsrechnungs-Freigabe | Zammad + Nextcloud, kein Extra-ERP |
| Zutritt / Kameras / SPS | später Geräte |

## Schnittstellen

- Keycloak → alle Apps: OIDC.
- Keycloak → Gruppenabgleich → Matrix: Join/Kick, nur mit `schulung:chat`.
- LMS → CAV: Abschluss (Webhook oder Worker). CAV → Keycloak: `schulung:*`.
- Portal → Zammad-API: optional internes Amts- oder Aufnahmeticket, nie die Mitglieder-UI für den Antrag.
- CAV / Portal → Zahlungsdienst: Mandat, 10-€-Abo, Einzahlung, Webhook → Tokens.
- CAV ↛ Nextcloud für Mitgliederakten.
- Zammad/LMS ↛ Bestände, Limits, Behördenexport.
- Tenant-Paket: CAV + Keycloak-User (per E-Mail) + NC-Folder + Zammad-Tickets der Organisation; Matrix-Historie nicht. [mandanten.md](mandanten.md).

## Haftung (kurz)

Die Anbauvereinigung bleibt für KCanG verantwortlich. Software unterstützt Dokumentation, Limits, Schulungsnachweis und Support; sie ersetzt keine Erlaubnis.
