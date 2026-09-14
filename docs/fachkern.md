# Fachkern und Portal

System of Record für alles, was bei Kontrolle oder Jahresmeldung stehen muss. Nicht Nextcloud, nicht Matrix.

## Portal (FastAPI)

- Nach Login Module zeigen: CAV, Chat (Element), Cloud nur wenn Backoffice-Gruppe.
- Verein aus Token wählen, wenn jemand mehrere Ämter hat.
- „Meine Dokumente“: Beitrags-PDF, Abgabebeleg. Quelle ist die CAV-Datenbank bzw. Dateien, die der Kern kennt.
- Kein React-SPA am Anfang.

## CAV-Kern (FastAPI, Multi-Tenant)

| Modul | Inhalt | Reihenfolge |
| --- | --- | --- |
| Mandant / Verein | `verein_id`, Erlaubnisbezug, 500er-Deckel | 1 |
| Mitglieder | Alter, Status, Rollen, Token-Gruppen | 1 |
| Beitrags-PDF | Beleg erzeugen, im Portal zeigen | 2 |
| Chargen / Track & Trace | Samen bis Packung, Bestand in Gramm | 3 |
| Abgabe | Limits 50 g / 30 g (18–21), Empfänger, Sorte, THC | 3 |
| Vernichtung / Schwund | inkl. Verdacht Abhandenkommen | 4 |
| Labor | THC/CBD, Charge sperren, Rückruf | 4 |
| §-26-Export | fortlaufend + Jahresmeldung bis 31. Januar, 5 Jahre | 4 |
| Audit-Log | wer hat wann was gebucht, append-only | immer |

Abgabe ist Dokumentation der Weitergabe an Mitglieder (Selbstkosten), kein Verkaufs-Shop. Die Beitragsrechnung ist Vereinsbeitrag, getrennt vom Abgabebeleg.

## Was nicht selbst geschrieben wird

| Thema | Produkt |
| --- | --- |
| Login, MFA, Gruppen | Keycloak |
| Chat | Synapse + Element |
| Backoffice-Dateien und Office | Nextcloud + Collabora + Kalender |
| HTTPS | Traefik |
| Mailserver | externer Anbieter, nicht Mailcow am Tag 1 |
| Finanzbuchhaltung | DATEV / Lexoffice später |
| Zutritt / Kameras / SPS | später Geräte, nicht Kern |

## Schnittstellen

- Keycloak → CAV/Portal: OIDC-Token mit Gruppen.
- Keycloak → Gruppenabgleich → Matrix: Join/Kick.
- CAV → Portal: Belege und Status.
- CAV ↛ Nextcloud für Mitgliederakten.
- Matrix ↛ Bestände, Limits, Behördenexport.

## Haftung (kurz)

Die Anbauvereinigung bleibt für Dokumentation und Limits nach KCanG verantwortlich. Software unterstützt, ersetzt keine Erlaubnis und keine Kontrolle. Wer die Software betreibt oder anbietet, muss den Fachkern erklären können.
