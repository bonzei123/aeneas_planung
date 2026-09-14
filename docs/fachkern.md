# Fachkern und Portal

System of Record für alles, was bei Kontrolle oder Jahresmeldung stehen muss. Nicht Nextcloud, nicht Matrix.

## Portal (FastAPI)

Zwei Flächen, kein zweites ERP.

**Hauptmenü (Linktree, rollenabhängig):** nach dem Login Türen zu Chat (Element), CAV und — nur für Ämter — Nextcloud. Wer mehrere Vereine/Ämter hat, wählt zuerst den Verein.

**Mein Konto:** Stammdaten, soweit das Mitglied sie selbst pflegen darf; Beitrags- und Abgabebelege; Token-Stand; Mitgliedsbeitrag.

Beitragsmodell (aktueller Verein, kann sich beim Neustart ändern):

- Mindestbeitrag **10 €** im Monat per Lastschrift, wird als **Tokens** gutgeschrieben.
- Zusätzlich kann das Mitglied **manuell einzahlen** (Zahlungslink / Überweisung über den Dienstleister). Auch das wird als Tokens gutgeschrieben.
- Lastschriftmandat, Einzahlen und Gebühren: siehe [beitrag-sepa.md](beitrag-sepa.md).
- Höhe, Token-Logik und Zahlweg können sich später ändern; der Platz dafür bleibt „Mein Konto“ im Portal.

Kein React-SPA am Anfang.

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

Abgabe ist Dokumentation der Weitergabe an Mitglieder (Selbstkosten), kein Verkaufs-Shop. Der Zahlungsdienstleister zieht **Mitgliedsbeitrag** ein, keine Produktkäufe. Tokens sind interne Gutschrift aus dem Beitrag, getrennt vom Abgabebeleg und von KCanG-Limits (Gramm).

## Was nicht selbst geschrieben wird

| Thema | Produkt |
| --- | --- |
| Login, MFA, Gruppen | Keycloak |
| Chat | Synapse + Element |
| Backoffice-Dateien und Office | Nextcloud + Collabora + Kalender |
| HTTPS | Traefik |
| Mailserver | externer Anbieter, nicht Mailcow am Tag 1 |
| SEPA-Lastschrift | Zahlungsdienstleister (z. B. Mollie, GoCardless, Stripe) |
| Finanzbuchhaltung | DATEV / Lexoffice später |
| Zutritt / Kameras / SPS | später Geräte, nicht Kern |

## Schnittstellen

- Keycloak → CAV/Portal: OIDC-Token mit Gruppen.
- Keycloak → Gruppenabgleich → Matrix: Join/Kick.
- CAV / Portal → Zahlungsdienst: Mandat anlegen, monatlichen Betrag einziehen, Webhook „bezahlt“ → Tokens gutschreiben.
- CAV ↛ Nextcloud für Mitgliederakten.
- Matrix ↛ Bestände, Limits, Behördenexport.

## Haftung (kurz)

Die Anbauvereinigung bleibt für Dokumentation und Limits nach KCanG verantwortlich. Software unterstützt, ersetzt keine Erlaubnis und keine Kontrolle. Wer die Software betreibt oder anbietet, muss den Fachkern erklären können.
