# Aeneas Planung

Planungsrepository für die Serverlandschaft eines **Gesamtvereins** mit mehreren Cannabis-Anbauvereinigungen nach dem KCanG.

Hier liegt die Architektur, nicht der Anwendungscode. Selbst geschrieben werden später nur **Portal** und **CAV-Kern** (FastAPI) plus ein kleiner Matrix-Gruppenabgleich. Der Rest ist fertige Software (Keycloak, Matrix, Zammad, Moodle, Nextcloud).

## Zielbild in einem Satz

Ein Keycloak-Konto. Das Portal ist der Linktree. Fachliches (Abgabe, Anbau, Beitrag) im CAV-Kern. Chat in Matrix, Support in Zammad, Schulung in Moodle, Office nur für Ämter in Nextcloud.

## Dokumente

| Datei | Inhalt |
| --- | --- |
| [docs/entscheide.md](docs/entscheide.md) | Festlegungen, was fertig vs. selbst |
| [docs/architektur.md](docs/architektur.md) | Hosts, Compose, Flussbild |
| [docs/sso-matrix.md](docs/sso-matrix.md) | SSO, Keycloak-Gruppen, Matrix-Spaces |
| [docs/fachkern.md](docs/fachkern.md) | Portal, CAV, Schnittstellen |
| [docs/beitrag-sepa.md](docs/beitrag-sepa.md) | Tokens, Lastschrift, Einzahlung |
| [docs/tickets.md](docs/tickets.md) | Zammad |
| [docs/moodle.md](docs/moodle.md) | Schulungen, Mitwirkung |
| [docs/betrieb.md](docs/betrieb.md) | Linux, Backup, Größe |
| [docs/ci-cd.md](docs/ci-cd.md) | Corporate Identity / Design je App |
| [docs/mail.md](docs/mail.md) | Kein Mailserver, Kontakt nach außen |

## Nicht das Ziel

- Kein Nextcloud-Plugin als Abgabe, Helpdesk oder LMS
- Kein ERPNext als Mitgliederportal
- Kein OpenDesk/Kubernetes als Einstieg
- Kein selbst gebautes Ticketsystem oder LMS
- Keine Garantie „KCanG-konform“ durch Software allein
