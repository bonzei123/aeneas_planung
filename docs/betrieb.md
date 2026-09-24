# Betrieb und Größe

## Einstieg

- Ein Linux-Server (Debian oder Ubuntu LTS), Docker Compose
- Backup nach außen (restic/borg): Postgres-Dumps, MariaDB (LMS), plus Medien von Nextcloud, Frappe Learning, Zammad, Synapse; Overlay-Volumes (BookStack, …) nur wenn das Overlay läuft
- Container-Updates bewusst

## Ein Verein (≤500 Mitglieder)

Derselbe Compose (Keycloak, Portal, CAV, Zammad, Frappe, Matrix, Nextcloud). Kein Produkt fällt weg — nur Last, Speicher und Postfächer. Eine `verein:*`-Gruppe, eine Zammad-Organisation, ein Nextcloud-Group-Folder.

**32 GB RAM** reicht für den ganzen Stack, wenn Elasticsearch-Heap bei **512m–1g** bleibt und Collabora nicht dauernd alle Ämter gleichzeitig editen. **16 GB** nur Test ohne Collabora und ohne fettes ES. **64 GB Dedicated** für einen Verein Overkill.

| Posten | grob / Monat |
| --- | --- |
| Host 32 GB (Hetzner CX53 oder netcup RS 4000) | **40–50 €** brutto |
| Storage Box Backup | **ca. 5 €** |
| mailbox.org: 1–2 Standard-Postfächer, Rest Aliasse (`info@`, `vorstand@`) | **5–10 €** |
| Domain | ~1 € (Jahrespreis / 12) |
| **Infra gesamt** | **ca. 50–70 €** |

Kein Business-Silber, keine 30 Postfächer. Overlays (Jitsi, Wiki) erst, wenn RAM-Kopf bleibt. Zahlungsdienst (Lastschrift ~0,35 €/Einzug) ist **kein** Serverpreis — bei 500×10 € Lastschrift eher der teurere Posten.

Ausgliedern auf einen eigenen Host: dieselbe 32-GB-Klasse plus Tenant-Paket, [mandanten.md](mandanten.md).

Skalierung auf viele Vereine: unten, nicht diese Kiste tauschen.

## 15.000–20.000 Konten, bis ~180 Vereine

Viele Zweigvereine à höchstens 500 Mitglieder. Last ist Gleichzeitigkeit und Ticket-/Kurs-/Chatbetrieb, nicht die User-Tabelle. Eine logische Plattform (ein Keycloak, ein CAV, ein Zammad, ein LMS), nicht 180 Stacks.

| Teil | Ein Compose-Host |
| --- | --- |
| Keycloak, CAV, Portal | ja, indexiert |
| Matrix geschlossen | ja, ohne Massen-Video; bei 20k eher eigene VM |
| Zammad, hunderte Tickets/Monat | ja; bei 15k+ eigene VM + Suche laut Zammad-Doku |
| Frappe Learning für alle Mitglieder | ja; kein Moodle-Campus-Stack, eigene VM erst bei Last |
| Nextcloud nur wenige hundert Ämter | ja |
| Nextcloud für alle Mitglieder | nein |
| Optionale Overlays (Wiki, Jitsi) | ja, aber nicht alle gleichzeitig ohne RAM-Plan |

Skalierung: Dienste vom App-Host trennen, nicht Kubernetes als Start. Docker Compose bleibt das Werkzeug; Details: [repos-und-docker.md](repos-und-docker.md).

## Hoster (Einstieg)

Deutschland, Root, AV-Vertrag nach DSGVO. Kein US-Hyperscaler als IdP-/Mitgliederhost. **Hetzner** (Falkenstein/Nürnberg) passt: Robot, Storage Box für restic, später vSwitch wenn ihr VMs teilt. Alternative ähnlich: **netcup** (Nürnberg, Root-Server).

RAM, nicht vCPU, ist der Preis. Ein Verein: **32 GB**. Unter 16 GB nur Kern ohne Collabora. 180 Vereine: **64 GB** als eine Kiste, danach splitten.

| Phase | Maschine (Beispiel) | grob / Monat |
| --- | --- | --- |
| Ein Verein, ganzer Compose | Cloud/Root **32 GB** | **40–50 €** brutto |
| Test, nur KC+Portal+Zammad | 16 GB, ES klein | **20–25 €** |
| Offsite-Backup | Storage Box 1 TB | **ca. 4–6 €** |
| Viele Vereine, eine Kiste | Dedicated **64 GB** | **70–100 €** |
| 15k–20k, geteilt | 2–3 Hosts | **200–400 €** nur Server |

Preise Hetzner nach Anpassung Juni 2026, zzgl. IPv4, ohne Gewähr; netto×1,19. Cloud CX53 ≈ 35 € netto, EX44-Klasse ≈ 68 € netto. Nicht denselben Kasten wie Stoat/Community-Nextcloud dauerhaft vollpacken.

Dazu getrennt: mailbox.org Funktionspostfächer ([mail.md](mail.md)), Domains, Zahlungsdienst-Gebühren (nicht Hosting).

## Sicherheit, grob

- Matrix ohne öffentliche Federation
- Zammad: Mitglieder sind Kunden, nicht Agenten
- LMS: Kurse intern, nur `mitgliedschaft:aktiv`
- Nextcloud-Client nur Backoffice-Gruppen
- MFA für Agenten, Vorstände, LMS-Admins
- Secrets nicht im Git
- Kein eigener Mailserver; SMTP/IMAP beim Anbieter, siehe [mail.md](mail.md)

## Reihenfolge zum Einschalten

Testserver zuerst den **Kern**, ohne CAV-Fachlogik und ohne Overlays. CAV-Repo ist noch ein Stub.

1. Linux, Traefik (hinter bestehendem Caddy, falls Port 80 belegt), Keycloak
2. Portal: Login, Linktree, Aufnahmeformular
3. Zammad + OIDC, Support-Queue (kein Aufnahme-Ticket als Mitglieder-UI)
4. Frappe Learning + OIDC, Katalog (Onboarding, Prävention, Chat-Regeln)
5. Matrix + Element, Gruppenabgleich inkl. `schulung:chat` (Abgleich braucht später CAV-Gruppen)
6. Nextcloud + Collabora nur Backoffice (Group Folders = Rechte-Ablage)
7. CAV: Mandant + Mitglied aus Token, dann Worker LMS-Abschluss → Keycloak `schulung:*`
8. Mein Konto: Beitrag, Tokens, Schulungsstatus
9. SEPA über Zahlungsdienst
10. Zammad-Vorlagen nur für Amts-Onboarding
11. Abgabe und Limits (an Prävention knüpfen)
12. Track & Trace, §-26-Export
13. Optionale Overlays nach Bedarf: BookStack, Jitsi, Listmonk, Vaultwarden; **OpenSlides** erst zur Mitgliederversammlung — [optionale-module.md](optionale-module.md)
