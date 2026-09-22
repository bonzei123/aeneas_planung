# Mandanten: allein, im Gesamtverein, raus, rein

Dieselbe Software, zwei Betriebsarten. Ein Verein kann **allein** laufen oder als Zweig unter einem Gesamtverein. Ausgliedern und wieder eintreten ist ein **Datenpaket**, kein Fork und kein zweites Produkt.

Rechtlich bleibt jeder Zweigverein eigene Anbauvereinigung (Erlaubnis, 500er, Bestand). Die Plattform darf Bestände nicht vermischen. Portabilität ändert das nicht — sie verhindert nur Lock-in an den Gesamtverein-Host.

## Betriebsarten

| Modus | Was anders ist | Was gleich bleibt |
| --- | --- | --- |
| Einzelverein | ein `verein_id`, ein Host 32 GB, ein mailbox-Fach, Hosts `id.` / `portal.` / `help.` … des Vereins | Compose, Images, Realm-Schema, `verein_id` auf jeder CAV-Zeile |
| Gesamtverein | viele `verein_id`, ein Keycloak, ein Zammad, ein LMS, ein Synapse | dieselben Images; Tenant nur über Gruppe `verein:<slug>` |

Kein „Single-Edition“-Code. Auch der Alleinbetrieb hat einen Mandantenstammsatz. Sonst ist der Export später eine Sonderlocke.

## Harte Grenze (damit Raus/Rein geht)

- Jede fachliche Zeile im CAV trägt **`verein_id`**. Keine Tabelle „gilt für alle Zweige“ mit gemischten Chargen.
- Mitglied hat **genau eine** `verein:*`-Gruppe. CAV-Primärschlüssel ist `mitglied_id` (UUID), **nicht** Keycloak-`sub` — der `sub` ändert sich auf einem neuen Keycloak.
- Dateien des Amts: **ein Group Folder pro Verein**, nicht ein gemischter Gesamtordner als SoR.
- Zammad: **eine Organisation = ein Verein**. Tickets nie ohne Organisation.
- LMS-Kurse dürfen global sein (Onboarding-Text). **Abschluss** liegt im CAV (`schulung:*`), nicht nur in Frappe.
- Keine URLs des Gesamtvereins in CAV-Fachdaten (Links gehören ins Portal/Theme).

Ohne diese Regeln ist „einfach portieren“ Marketing.

## Paket (`aeneas-tenant-pack`)

Ein Verzeichnis plus Manifest, Version im JSON. Inhalt:

| Teil | Muss mit | Wie |
| --- | --- | --- |
| CAV | ja | Dump `WHERE verein_id = …` (Mitglieder, Beitrag, Tokens, Chargen, Abgabe, Nachweise, Audit, § 26) |
| Keycloak | ja | User (E-Mail, Username, Name), Gruppen**namen** (`mitgliedschaft:*`, `rolle:*`, `schulung:*`), nicht interne UUIDs der Quell-Realm |
| Nextcloud | ja | Group-Folder-Tarball des Vereins |
| Schulung | ja | aus dem CAV; Kurse selbst sind Vorlagen, nicht Vereinsbesitz |
| Zammad | soweit sinnvoll | Tickets der Organisation per API (JSON); Anhänge |
| Matrix | nein, Historie | Space-Mitgliederliste reicht; **Raumverlauf bleibt auf dem alten Homeserver** (MXID ist serverseitig) |
| Zahlungsdienst | separat | Mandate liegen beim PSP; neuer Verein braucht eigene Gläubiger-ID / Mandats-Neuabschluss, kein stilles Umhängen |
| Mail | DNS/Aliasse | Postfach-Inhalte optional; Funktionsadressen zeigen nach dem Cut auf das neue Zammad |

Import matcht Personen über **E-Mail** (einzigartig). Kollision mit einem anderen Mandanten auf dem Ziel: Abbruch, keine stille Zusammenlegung.

## Ausgliedern

1. Verein beschließt, Stichtag.
2. Mandant **schreiben sperren** (CAV, Zammad-Organisation, NC-Folder).
3. Paket bauen, Prüfsumme, dem Verein aushändigen (und Archivkopie laut Aufbewahrung).
4. Neuer Host: derselbe Compose, ein Mandant, eigenes Keycloak, eigene Hosts.
5. Import, Login-Test mit einem Amt, DNS/Mail/OIDC-Redirects umlegen.
6. Quelle: User `enabled=false` oder aus Gruppen, Mandant `archiviert`. CAV-Zeilen **nicht** löschen, solange Aufbewahrung läuft — Zugriff nur noch Archiv, kein Alltag.

Mitglieder merken: neues Login-Theme, neue Chat-Matrix-ID. Fachliche Historie (Abgabe, Beitrag) bleibt.

## Eintreten / zurück

1. Paket vom Allein-System (oder vom vorigen Export).
2. Ziel prüft: Slug frei, 500er, keine E-Mail schon in einem anderen Zweig, Erlaubnisdaten vollständig.
3. Import CAV → Keycloak-User anlegen oder koppeln → Gruppen setzen aus dem Paket → NC-Folder → Zammad-Organisation.
4. Matrix: **neuer** Vereins-Space, Abgleich lädt ein. Alter Chat kommt nicht mit.
5. Quelle (das Allein-System) abschalten oder nur noch als Totarchiv.

Zurück in denselben Gesamtverein: dasselbe, `verein_id` darf die alte UUID bleiben, dann bleiben interne Referenzen stabil.

## Was nicht „easy“ ist — trotzdem sagen

| System | Portabilität |
| --- | --- |
| CAV + Portal-Belege | ja, Kern des Pakets |
| Keycloak-Konten | ja, über E-Mail; Passwörter neu setzen (IdP-Geheimnisse nicht kopieren) |
| Nextcloud-Vereinordner | ja, Tarball |
| Zammad | ja mit Aufwand (API); kein Knopf in der Zammad-UI |
| Frappe-Kurse | Vorlagen bleiben; Nachweise aus CAV neu anwenden |
| Element/Matrix | **Mitgliederchat-Historie nicht umziehen**; neuer Homeserver = neue MXID |
| SEPA-Mandate | nicht softwareseitig „mitnehmen“ |

Wer Chat-Verlauf als Vereinsakte braucht, exportiert vor dem Cut (Element/Synapse-Admin) und legt die Datei nach Nextcloud. Das ist Archiv, kein weiterlaufender Raum.

## Betrieb

Einzelverein: [betrieb.md](betrieb.md) 32-GB-Kiste. Gesamtverein: dieselbe YAML, mehr RAM später. Paket-Job im CAV (Worker), nicht per Hand 40 SQL-Fenster. Tests: Export → leere Compose → Import → Login und eine Abgabezeile sichtbar.
