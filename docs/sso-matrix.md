# SSO und Matrix

## Was SSO von allein kann

Keycloak ist der Identity Provider. Portal, CAV, Nextcloud, **MAS** (Matrix-Login), **Zammad** und **Frappe Learning** sind OIDC-Clients desselben Realms. Synapse spricht nicht selbst mit Keycloak. Optionale Overlays (BookStack, Jitsi, Listmonk, Vaultwarden) ebenfalls, nur wenn das Overlay läuft. **OpenSlides** hängt am Host `wahl.`, loggt aber lokal (`superadmin` / Teilnehmer); Gruppe `admin:wahl` nur für die Portal-Kachel.

Ablauf:

1. Person meldet sich einmal an Keycloak an (optional MFA).
2. Die nächste App nutzt die bestehende Sitzung, ohne neues Passwort.
3. Ein Groups-Mapper legt Gruppen in Token bzw. Userinfo.
4. CAV und Portal lesen die Claims: Tenant aus `verein:*`, Mitgliedschaftsstatus aus `mitgliedschaft:*`, Berechtigung aus `rolle:*` bzw. `cloud`.
5. Der Nextcloud-Client ist auf Amts-/Dienstgruppen begrenzt. `mitgliedschaft:pending` und `:beendet` bekommen kein Nextcloud-Konto.
6. Zammad: eingeloggte User (auch pending/beendet) als Kunden; LMS nur `mitgliedschaft:aktiv`. Agenten nach `rolle:*`. Matrix-Join zusätzlich `schulung:chat`.

Gleiches Login heißt nicht gleiche Sicht. Nextcloud bleibt vom Mitgliederbereich getrennt.

## SSO und Berechtigung

OIDC ist nur die Anmeldung (wer ist das). **Berechtigung** (was darf die Person) liegt in denselben Keycloak-Gruppen, die der Groups-Mapper ins Access-Token bzw. Userinfo schreibt. Die Apps werten die Claims aus; es gibt kein zweites Rechtesystem „nur für SSO“.

| Schicht | Ort | Beispiel |
| --- | --- | --- |
| Authentifizierung | Keycloak Login / Token | Sub, E-Mail, Session |
| Autorisierung (RBAC) | Gruppen im Token | `verein:wanne-eickel`, `rolle:ausgabe` |
| Fachliche Objekt-Rechte | CAV-Datenbank | diese Abgabe, diese Charge, 500er-Deckel |

Keycloak Authorization Services (UMA, Policies pro Resource) nicht nutzen. Zu komplex für den Solo-Betrieb; CAV prüft Objekt-Rechte selbst, Rollen kommen aus dem Token.

Nextcloud, Zammad, Frappe Learning und optionale Overlays: jeweilige IdP-/Group-Mapper auf dieselben Gruppennamen. Matrix: nicht nativ, eigener Abgleich (unten). Kein zweites Benutzerverzeichnis im Wiki.

## Was SSO nicht kann

MAS kennt OIDC-Login nach Keycloak (Element X und Element Web). Synapse kennt **kein** „zeige nur Räume zu meinen Keycloak-Gruppen“.

Die Kanalliste in Element ist die Menge der Räume, in denen das Matrix-Konto Mitglied ist. Ohne zusätzlichen Abgleich sieht ein neues Mitglied entweder nichts oder zu viel (öffentliches Raumverzeichnis). Das ist ein bekanntes, offenes Thema in Synapse, kein Konfigurationshäkchen.

## Gruppenmodell

Gruppen in Keycloak, nicht in Nextcloud oder Matrix als führendes System.

Vier Achsen, kein Kreuzprodukt:

| Achse | Anzahl | Beispiel | Bedeutung |
| --- | --- | --- | --- |
| Organisation | 1 Gruppe pro Zweigverein (+ optional Bundesland) | `verein:wanne-eickel`, `bundesland:nrw` | wo die Person zugeordnet ist |
| Mitgliedschaft | genau eine | `mitgliedschaft:pending`, `mitgliedschaft:aktiv`, `mitgliedschaft:beendet` | ob Login-Fachzugriff Mitglied ist |
| Funktion | festes Set | `rolle:vorstand`, `rolle:ap`, `rolle:praevb`, `rolle:ausgabe`, `rolle:anbau`, `cloud` | Amt und Dienst |
| Schulung | 0..n | `schulung:onboarding`, `schulung:praevention`, `schulung:chat`, `schulung:ausgabe` | LMS-Abschluss; CAV schreibt die Gruppe |

Bei 180 Zweigvereinen: 180 `verein:*` plus Status-, Funktions- und Schulungsset, nicht 180×Rollen. Ausgabe in Wanne-Eickel = `verein:wanne-eickel` **und** `rolle:ausgabe`. PräVB analog: `verein:wanne-eickel` **und** `rolle:praevb`.

Keine Gruppen `verein:<slug>:mitglied` / `verein:<slug>:vorstand`. Tenant im CAV kommt aus genau einer `verein:*`-Gruppe. KCanG-Mitgliedschaft in mehr als einem Anbauverein ist rechtlich eingeschränkt; der Fachkern prüft das, nicht Keycloak. Gesamtverein sieht Mandanten in der CAV-UI über `cloud`, nicht über 180 Vereinsgruppen.

CAV speichert Personen als `mitglied_id`. Keycloak-`sub` nur Spiegel — beim Ausgliedern gibt es ein neues Realm, der `sub` wechselt, die E-Mail bleibt der Join-Schlüssel. [mandanten.md](mandanten.md).

`verein:*`-Gruppen später aus dem CAV-Mandantenstamm erzeugen oder importieren, nicht 180-mal per Hand in der Admin-Konsole.

### Mitgliedschaftsstatus

Genau eine der drei Gruppen. Keycloak-User bleibt `enabled`, solange ein Login gewünscht ist. Statuswechsel und Schulungsabschlüsse macht die CAV-API (Gruppen tauschen), nicht die Admin-Konsole.

| Gruppe | Bedeutung | Konto |
| --- | --- | --- |
| `mitgliedschaft:pending` | Antrag eingegangen, noch nicht aufgenommen | anlegen bei Formular-Absendung; Passwort-Mail |
| `mitgliedschaft:aktiv` | aufgenommen, zählt gegen 500er-Deckel, Beitrag | pending → aktiv bei Zusage |
| `mitgliedschaft:beendet` | ausgetreten / gekündigt, war Mitglied | aktiv → beendet; Login bleibt |

Konto existiert also schon im Antrag, nicht erst nach Handanlage. Amt gibt frei oder lehnt ab — keine Abschrift der Stammdaten.

- **pending:** Portal (Antragsstatus), Zammad als Kunde. Keine CAV-Abgabe, kein Mitglieder-Matrix, kein LMS, kein Nextcloud. Zählt nicht gegen 500.
- **aktiv:** LMS erlaubt; Chat, Abgabe und CAV-Alltag erst mit den jeweiligen `schulung:*` (siehe [schulungen.md](schulungen.md)).
- **beendet:** Portal (Belege, Beitragshistorie), Zammad-Tickets. Keine Abgabe, Matrix-Kick aus Mitglieder-Spaces, Amts-`rolle:*` entfernen. 500er frei.
- **Ablehnung** eines Antrags: nicht `beendet`. User `enabled=false` oder löschen.
- **Konto löschen** (Selbstbedienung im Portal): Keycloak-Login beenden (`enabled=false` oder User löschen). CAV-Fachdaten (Abgabe, Beitrag, § 26) bleiben die gesetzliche Aufbewahrung; das ist kein vollständiges Löschen der Vereinsakte.

CAV bleibt System of Record für Status und Schulungsnachweis; Keycloak spiegelt beides für Token und andere Apps. `schulung:*` setzt nur der CAV nach LMS-Abschluss, nicht die Admin-Konsole.

Gruppen (Präfixe `mitgliedschaft:`, `verein:`, `rolle:`, `schulung:`; keine `amt:*`-Duplikate):

| Keycloak-Gruppe | Art | Bedeutung |
| --- | --- | --- |
| `mitgliedschaft:pending` | Status | Antrag, Login ohne Abgabe |
| `mitgliedschaft:aktiv` | Status | beitragsfähiges Mitglied, 500er |
| `mitgliedschaft:beendet` | Status | ex-Mitglied, Belege/Tickets, keine Abgabe |
| `bundesland:nrw` | Organisation | Landesebene (optional, Matrix-Space) |
| `verein:wanne-eickel` | Organisation | Zweigverein (Tenant) |
| `rolle:vorstand` | Amt | Vorstand des eigenen Vereins |
| `rolle:ap` | Amt | Ansprechpartner (Behörde / außen) |
| `rolle:praevb` | Amt | Präventionsbeauftragte |
| `rolle:ausgabe` | Dienst | Ausgabe; CAV-Abgabe, mehrere Personen pro Verein |
| `rolle:anbau` | Dienst | Anbauteam |
| `cloud` | Dienst | Nextcloud, Portal-Kachel Cloud, Gesamtverein |
| `schulung:onboarding` | Nachweis | Onboarding bestanden |
| `schulung:praevention` | Nachweis | Prävention / Jahreskurs |
| `schulung:chat` | Nachweis | Chat-Regeln; Voraussetzung Matrix |
| `schulung:ausgabe` | Nachweis | Ausgabeschulung; zusätzlich zu `rolle:ausgabe` |

Vorstand, AP, PräVB sind satzungsgemäße bzw. KCanG-Ämter (oft wenige Personen). Ausgabe ist ebenfalls eine **Rolle**, aber betrieblich: Schichtpersonal, das abgibt — nicht 180 Gruppen, eine globale `rolle:ausgabe`. Wer beides ist (Vorstand, der auch ausgibt), bekommt beide Funktionsgruppen.

## Matrix-Spaces zum selben Beispiel

Ableitung: Mitglieder-Spaces nur bei `mitgliedschaft:aktiv` **und** `schulung:chat`. `pending` und `beendet` nicht in Vereins-Spaces. Ohne Chat-Schulung kein Element, auch wenn der Status schon aktiv ist.

| Bedingung | Matrix | Nextcloud |
| --- | --- | --- |
| `mitgliedschaft:pending` | kein Mitglieder-Space | kein Konto |
| `mitgliedschaft:aktiv` ohne `schulung:chat` | kein Join | kein Konto |
| `mitgliedschaft:aktiv` und `schulung:chat` | Space Gesamtverein: Ankündigungen, Hilfe, Regeln | kein Konto |
| `mitgliedschaft:beendet` | Kick aus Mitglieder-Spaces | kein Konto |
| `bundesland:nrw` (aktiv + `schulung:chat`) | Space NRW | kein Konto |
| `verein:wanne-eickel` (aktiv + `schulung:chat`) | Space Wanne-Eickel: Ort, Termine, Vereinschat | kein Konto |
| zusätzlich `rolle:vorstand` | Space Vorstand Wanne-Eickel | Konto, Group Folder, Kalender |
| zusätzlich `rolle:ap` | Dienstraum Ansprechpartner | Konto, Amt-Ordner |
| zusätzlich `rolle:praevb` | Dienstraum Prävention | Konto, Amt-Ordner |
| zusätzlich `rolle:ausgabe` | Dienstraum Ausgabe (nicht die Abgabebuchung) | Konto, Dienstordner |

Kindräume mit Join-Regel `restricted`: nur wer im Space ist, darf beitreten. Raumverzeichnis nicht öffentlich. Federation aus.

Dann erscheint in Element genau die Hierarchie, in die der Abgleich eingeladen hat — nicht die anderen Ortsvereine.

## Gruppenabgleich (eigener kleiner Dienst)

Nach Login und zusätzlich periodisch:

1. Gruppen der Person aus Keycloak lesen.
2. Mapping-Tabelle: Gruppe → Space- bzw. Room-ID.
3. Synapse-Admin-API: fehlende Memberships joinen.
4. Bei Gruppenverlust kicken (Austritt Wanne-Eickel → Space Wanne-Eickel verlassen).

CAV verweigert Abgabe, sobald `mitgliedschaft:aktiv` oder die vom Verein geforderte `schulung:praevention` fehlt. Matrix folgt erst, wenn `schulung:chat` da ist und der Abgleich gelaufen ist. Abgleich direkt nach Login, nach Statuswechsel und nach Schulungsabschluss anstoßen, nicht nur nachts.

Element Server Suite „Group Sync“ wäre die Kaufvariante. Für den Solo-Betrieb ist ein eigener, lesbarer Worker vorgesehen.

## Klickweg

Mitglied Wanne-Eickel:

1. Login Keycloak
2. Portal: Belege, CAV, Support, Schulungen; Chat-Link erst mit `schulung:chat`; optionale Links (Wiki, Meet) nur wenn Overlay an
3. LMS: Onboarding, Prävention, Chat-Regeln
4. Element Web oder Element X (nach Chat-Kurs): Gesamtverein + NRW + Wanne-Eickel
5. Zammad: eigene Tickets
6. CAV nur Tenant Wanne-Eickel; Abgabe nach Präventionskurs
7. kein `cloud.example`

Vorstand: dasselbe plus Vorstands-Space, Zammad als Agent, Nextcloud, Amtskurse im LMS.
