# Festlegungen

Stand der Planung. Änderungen gehören als Commit in dieses Repository, nicht nur in einen Chat.

## Produktform

Aeneas ist eine **eigene Landschaft**, kein Fork von [cannaUNITY](https://github.com/saschadaemgen/cannaUNITY) als Produkt.

cannaUNITY ist höchstens Ideenquelle für Track & Trace. Der Code wird nicht übernommen.

**Regel:** Nur Portal und CAV-Kern (plus der kleine Matrix-Gruppenabgleich) werden selbst geschrieben. Alles andere ist fertige Software hinter Keycloak.

Betrieb: **Docker Compose**, ggf. mehrere VMs, kein Kubernetes. GitHub: Infra-Compose plus Portal/CAV — nicht ein Repo pro Upstream-Produkt. [repos-und-docker.md](repos-und-docker.md).

**Mandanten:** dieselbe Software allein oder unter dem Gesamtverein. Ausgliedern und Beitritt über ein Tenant-Paket, nicht über einen zweiten Codepfad. [mandanten.md](mandanten.md).

## Schichten

1. **Identität und RBAC:** Keycloak (ein Realm, viele OIDC-Clients). Gruppen im Token sind die Berechtigung; Katalog [sso-matrix.md](sso-matrix.md). Mitgliedschaftsstatus genau einer von `pending` / `aktiv` / `beendet`; Login unabhängig von der Abgabe. Dienst-Türen zusätzlich über `schulung:*` (Abschluss im LMS, geschrieben vom CAV).
2. **Fertige Apps (Kern):** Matrix/Element (Chat), Zammad (Tickets), Frappe Learning (Schulung), Nextcloud+Collabora (nur Backoffice).
3. **Eigener Fachkern:** FastAPI Portal + CAV-Mandanten, PostgreSQL. Person = `mitglied_id`, nicht Keycloak-`sub` (sonst stirbt der Reimport).
4. **Optional:** Compose-Overlays hinter demselben Keycloak, [optionale-module.md](optionale-module.md). Nicht im Kern-Compose.

## Nextcloud

Nur Backoffice (Vorstand, AP, PräVB, Ausgabe, Anbau, Gesamtverein). Mitglieder bekommen kein Nextcloud-Konto. Belege im Portal.

**Ablage mit Rechten:** nur Nextcloud Group Folders. Kein Paperless, kein Alfresco. Flow/Approval nur Datei-Freigabe, kein Rechnungs-BPM — das ist Zammad plus später DATEV/Lexoffice.

## Matrix

Mitgliederchat, Spaces je Gesamtverein / Land / Ort. Kein Talk, kein Discord-SaaS. Kanalrechte: [sso-matrix.md](sso-matrix.md). Join erst mit `schulung:chat`, nicht schon bei bloßem `mitgliedschaft:aktiv`.

## Mitgliedsbeitrag und Tokens

- **10 €** fest im Monat per Lastschrift, Tokens.
- Zusätzlich manuelle Einzahlung per Zahlungslink.
- Mein Konto im Portal. Einzug: Dienstleister, [beitrag-sepa.md](beitrag-sepa.md).

Der Zahlungsdienst sieht nur Beitrag, keine Gramm.

## Support: Zammad

Hunderte Tickets pro Monat: **Zammad**, OIDC, kein eigenes Helpdesk, kein Nextcloud-Plugin. Amts-Checklisten über Zammad-Vorlagen/Makros — nicht der Mitglieder-Aufnahmeantrag. [tickets.md](tickets.md).

## Schulung: Frappe Learning

Kein Moodle: Campus-Stack, PHP-FPM, Plugin-Zoo, eigene VM schon bei mäßiger Last — ungeeignet für Onboarding/Prävention/Quiz. **Frappe Learning** (AGPL, MariaDB+Redis+App/Worker), nicht ERPNext. Kurse für Onboarding, Prävention, interne und Amts-Schulungen; Mitwirkungsnachweis. Abschlüsse steuern Türen (Chat, Abgabe, …) über CAV → Keycloak-Gruppen `schulung:*`. [schulungen.md](schulungen.md).

## Mail

Kein eigener Mailserver. Intern: Matrix, Zammad, Nextcloud, Portal. Nach außen und für Passwortreset: Postfächer plus SMTP beim Anbieter. Nichtmitglieder erreichen Ämter über Zammad-Formular oder Funktionsmail, nicht über private Adressen. Aufnahme: gebrandetes Portal-Formular, nicht Zammad-Ticketmaske. [mail.md](mail.md).

## Optionale Module

Nicht jeder Verein braucht dasselbe. Overlay, gleicher Realm, Portal zeigt nur eingeschaltete Links.

| Wunsch | Festlegung |
| --- | --- |
| Wiki / Handbuch | optional **BookStack**; FAQs zuerst Zammad-KB, Pflichtstoff im LMS |
| Git für Mitglieder | **nein**; Code auf GitHub, Satzung im Wiki/Nextcloud |
| DMS | nur **Nextcloud**; Paperless nicht |
| Eingangsrechnungen | **Zammad + Nextcloud**; buchen DATEV/Lexoffice; kein FOSS-ERP |
| Video | optional **Jitsi**, kein BBB |
| Newsletter | optional **Listmonk** |
| Amts-Passwörter | optional **Vaultwarden** |

[optionale-module.md](optionale-module.md).

## ERPNext

Nein als Portal, Fachkern oder Rechnungsbuch. Buchhaltung später DATEV/Lexoffice. Eingangsrechnungen: Zammad plus Nextcloud, [tickets.md](tickets.md). Frappe Learning nutzt dasselbe Framework, ist aber nur das LMS.

## OpenDesk

Gleiches Gedankenmodell, Kubernetes. Einstieg bleibt Compose.

## Erscheinungsbild

Alles Web-seitig brandbar (Logo, Name, Farben), mit Abstufungen. Matrix hat kein UI — gebrandet wird **Element**. Handy-Apps aus den Stores bleiben fremd markiert, außer eigenem Build oder Kauf-White-Label. Details: [ci-cd.md](ci-cd.md).

## Code, den niemand erklärt

Selbst: Portal, CAV-Kern, Matrix-Gruppenabgleich. Konfigurieren: Keycloak, Synapse, Zammad, Frappe Learning, Nextcloud, Traefik, Postgres, MariaDB (LMS), Zahlungsdienst. Optional dazu: BookStack, Jitsi, Listmonk, Vaultwarden — nur Images und Compose, kein Fork.
