# SSO und Matrix

## Was SSO von allein kann

Keycloak ist der Identity Provider. Portal, CAV, Nextcloud, Synapse (bzw. MAS), **Zammad** und **Moodle** sind OIDC-Clients desselben Realms.

Ablauf:

1. Person meldet sich einmal an Keycloak an (optional MFA).
2. Die nächste App nutzt die bestehende Sitzung, ohne neues Passwort.
3. Ein Groups-Mapper legt Gruppen in Token bzw. Userinfo.
4. CAV und Portal lesen die Gruppen: Tenant aus `verein:*`, Berechtigung aus den Funktionsgruppen.
5. Der Nextcloud-Client ist auf Backoffice-Gruppen begrenzt. Mitglieder werden dort nicht provisioniert.
6. Zammad und Moodle: alle aktiven Mitglieder. In Zammad sind Mitglieder Kunden, Ämter Agenten.

Gleiches Login heißt nicht gleiche Sicht. Nextcloud bleibt vom Mitgliederbereich getrennt.

## Was SSO nicht kann

Synapse kennt OIDC-Login. Synapse kennt **kein** „zeige nur Räume zu meinen Keycloak-Gruppen“.

Die Kanalliste in Element ist die Menge der Räume, in denen das Matrix-Konto Mitglied ist. Ohne zusätzlichen Abgleich sieht ein neues Mitglied entweder nichts oder zu viel (öffentliches Raumverzeichnis). Das ist ein bekanntes, offenes Thema in Synapse, kein Konfigurationshäkchen.

## Gruppenmodell

Gruppen in Keycloak, nicht in Nextcloud oder Matrix als führendes System.

Zwei Achsen, kein Kreuzprodukt:

| Achse | Anzahl | Beispiel | Bedeutung |
| --- | --- | --- | --- |
| Organisation | 1 Gruppe pro Zweigverein (+ optional Bundesland) | `verein:wanne-eickel`, `bundesland:nrw` | wo die Person zugeordnet ist |
| Funktion | festes, kleines Set | `mitgliedschaft:aktiv`, `rolle:vorstand`, `amt:ausgabe`, `amt:praevention`, `amt:anbau`, `backoffice` | was die Person darf |

Bei 180 Zweigvereinen: 180 `verein:*`-Gruppen plus etwa 10 Funktionsgruppen, nicht 180×3. Vorstand von Wanne-Eickel = `verein:wanne-eickel` **und** `rolle:vorstand`. Prävention analog: `verein:wanne-eickel` **und** `amt:praevention`.

Keine Gruppen `verein:<slug>:mitglied` / `verein:<slug>:vorstand`. Tenant im CAV kommt aus genau einer `verein:*`-Gruppe. KCanG-Mitgliedschaft in mehr als einem Anbauverein ist rechtlich eingeschränkt; der Fachkern prüft das, nicht Keycloak. Backoffice sieht Mandanten in der CAV-UI über `backoffice`, nicht über 180 Vereinsgruppen.

`verein:*`-Gruppen später aus dem CAV-Mandantenstamm erzeugen oder importieren, nicht 180-mal per Hand in der Admin-Konsole.

| Keycloak-Gruppe | Bedeutung |
| --- | --- |
| `mitgliedschaft:aktiv` | beitragsfähiges Mitglied |
| `bundesland:nrw` | Landesebene (optional, Matrix-Space) |
| `verein:wanne-eickel` | Zweigverein (Tenant) |
| `rolle:vorstand` | Vorstand des eigenen Vereins |
| `amt:ausgabe` | Ausgabestelle (dienstlich) |
| `amt:praevention` | Präventionsbeauftragte |
| `amt:anbau` | Anbauteam |
| `backoffice` | Gesamtverein-Mitarbeiter |

## Matrix-Spaces zum selben Beispiel

Ableitung: Space des Vereins aus `verein:*`; Vorstands-Space nur wenn zusätzlich `rolle:vorstand`.

| Bedingung | Matrix | Nextcloud |
| --- | --- | --- |
| `mitgliedschaft:aktiv` | Space Gesamtverein: Ankündigungen, Hilfe, Regeln | kein Konto |
| `bundesland:nrw` | Space NRW | kein Konto |
| `verein:wanne-eickel` | Space Wanne-Eickel: Ort, Termine, Vereinschat | kein Konto |
| zusätzlich `rolle:vorstand` | Space Vorstand Wanne-Eickel | Konto, Group Folder, Kalender |
| `amt:ausgabe` | Dienstraum Ausgabe (nicht die Abgabebuchung) | Konto, Dienstordner |

Kindräume mit Join-Regel `restricted`: nur wer im Space ist, darf beitreten. Raumverzeichnis nicht öffentlich. Federation aus.

Dann erscheint in Element genau die Hierarchie, in die der Abgleich eingeladen hat — nicht die anderen Ortsvereine.

## Gruppenabgleich (eigener kleiner Dienst)

Nach Login und zusätzlich periodisch:

1. Gruppen der Person aus Keycloak lesen.
2. Mapping-Tabelle: Gruppe → Space- bzw. Room-ID.
3. Synapse-Admin-API: fehlende Memberships joinen.
4. Bei Gruppenverlust kicken (Austritt Wanne-Eickel → Space Wanne-Eickel verlassen).

CAV verweigert den Tenant sofort, sobald die Gruppe fehlt. Matrix folgt erst, wenn der Abgleich gelaufen ist. Deshalb den Abgleich direkt nach Login anstoßen, nicht nur nachts.

Element Server Suite „Group Sync“ wäre die Kaufvariante. Für den Solo-Betrieb ist ein eigener, lesbarer Worker vorgesehen.

## Klickweg

Mitglied Wanne-Eickel:

1. Login Keycloak
2. Portal: Belege, CAV, Chat, Support, Schulungen
3. Element: Gesamtverein + NRW + Wanne-Eickel
4. Zammad: eigene Tickets; Moodle: Jahresschulung
5. CAV nur Tenant Wanne-Eickel
6. kein `cloud.example`

Vorstand: dasselbe plus Vorstands-Space, Zammad als Agent, Nextcloud, Amtskurse in Moodle.
