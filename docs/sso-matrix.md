# SSO und Matrix

## Was SSO von allein kann

Keycloak ist der Identity Provider. Portal, CAV, Nextcloud und Synapse (bzw. Matrix Authentication Service) sind OIDC-Clients desselben Realms.

Ablauf:

1. Person meldet sich einmal an Keycloak an (optional MFA).
2. Die nächste App nutzt die bestehende Sitzung, ohne neues Passwort.
3. Ein Groups-Mapper legt Gruppen in Token bzw. Userinfo.
4. CAV und Portal lesen die Gruppen und beschränken den Verein und die Funktion.
5. Der Nextcloud-Client ist in Keycloak auf Backoffice-Gruppen begrenzt. Mitglieder können diesen Client nicht benutzen und werden dort nicht provisioniert.

Gleiches Login heißt nicht, dass jede App jede Person sieht. Nextcloud und Mitglieder-Matrix teilen sich nur Keycloak, nicht den Dateispeicher.

## Was SSO nicht kann

Synapse kennt OIDC-Login. Synapse kennt **kein** „zeige nur Räume zu meinen Keycloak-Gruppen“.

Die Kanalliste in Element ist die Menge der Räume, in denen das Matrix-Konto Mitglied ist. Ohne zusätzlichen Abgleich sieht ein neues Mitglied entweder nichts oder zu viel (öffentliches Raumverzeichnis). Das ist ein bekanntes, offenes Thema in Synapse, kein Konfigurationshäkchen.

## Gruppenmodell (Beispiel)

Gruppen in Keycloak, nicht in Nextcloud oder Matrix als führendes System.

| Keycloak-Gruppe | Bedeutung |
| --- | --- |
| `mitgliedschaft:aktiv` | beitragsfähiges Mitglied irgendwo |
| `bundesland:nrw` | Landesebene |
| `verein:wanne-eickel:mitglied` | Ortsverein |
| `verein:wanne-eickel:vorstand` | Vorstand dieses Vereins |
| `amt:ausgabe` | Ausgabestelle (dienstlich) |
| `amt:praevention` | Präventionsbeauftragte |
| `amt:anbau` | Anbauteam |
| `backoffice` | Gesamtverein-Mitarbeiter |

Ein Mensch kann mehrere Gruppen haben (Mitglied Wanne-Eickel und Amt Ausgabe). KCanG-Mitgliedschaft in mehr als einem Anbauverein ist rechtlich eingeschränkt; das prüft der Fachkern, nicht Matrix.

## Matrix-Spaces zum selben Beispiel

| Keycloak-Gruppe | Matrix | Nextcloud |
| --- | --- | --- |
| `mitgliedschaft:aktiv` | Space Gesamtverein: Ankündigungen, Hilfe, Regeln | kein Konto |
| `bundesland:nrw` | Space NRW | kein Konto |
| `verein:wanne-eickel:mitglied` | Space Wanne-Eickel: Ort, Termine, Vereinschat | kein Konto |
| `verein:wanne-eickel:vorstand` | zusätzlich Space Vorstand Wanne-Eickel | Konto, Group Folder, Kalender |
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
2. Portal: Belege, Link CAV, Link Chat
3. Element: Gesamtverein + NRW + Wanne-Eickel
4. CAV nur Tenant Wanne-Eickel
5. kein `cloud.example`

Vorstand Wanne-Eickel: dasselbe plus Vorstands-Space und Nextcloud (Kalender, Collabora, Vereinsordner).
