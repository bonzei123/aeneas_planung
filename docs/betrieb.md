# Betrieb und Größe

## Einstieg

- Ein Linux-Server (Debian oder Ubuntu LTS), Docker Compose
- Backup nach außen (restic/borg): Postgres-Dumps, MariaDB (LMS), plus Medien von Nextcloud, Frappe Learning, Zammad, Synapse
- Container-Updates bewusst

## 15.000–20.000 Konten, bis ~180 Vereine

Viele Zweigvereine à höchstens 500 Mitglieder. Last ist Gleichzeitigkeit und Ticket-/Kurs-/Chatbetrieb, nicht die User-Tabelle. Eine logische Plattform (ein Keycloak, ein CAV, ein Zammad, ein LMS), nicht 180 Stacks.

| Teil | Ein Compose-Host |
| --- | --- |
| Keycloak, CAV, Portal | ja, indexiert |
| Matrix geschlossen | ja, ohne Massen-Video; bei 20k eher eigene VM |
| Zammad, hunderte Tickets/Monat | ja; bei 15k+ eigene VM + Suche laut Zammad-Doku |
| Frappe Learning für alle Mitglieder | ja; schlanker als Moodle, eigene VM erst bei Last |
| Nextcloud nur wenige hundert Ämter | ja |
| Nextcloud für alle Mitglieder | nein |

Skalierung: Dienste vom App-Host trennen, nicht Kubernetes als Start. Docker Compose bleibt das Werkzeug; Details: [repos-und-docker.md](repos-und-docker.md).

## Sicherheit, grob

- Matrix ohne öffentliche Federation
- Zammad: Mitglieder sind Kunden, nicht Agenten
- LMS: Kurse intern, nur `mitgliedschaft:aktiv`
- Nextcloud-Client nur Backoffice-Gruppen
- MFA für Agenten, Vorstände, LMS-Admins
- Secrets nicht im Git
- Kein eigener Mailserver; SMTP/IMAP beim Anbieter, siehe [mail.md](mail.md)

## Reihenfolge zum Einschalten

1. Linux, Traefik, Keycloak
2. Portal: Login, Linktree, Aufnahmeformular
3. CAV: Mandant + Mitglied aus Token, `schulung:*` noch leer
4. Zammad + OIDC, Support-Queue (kein Aufnahme-Ticket als Mitglieder-UI)
5. Frappe Learning + OIDC, Katalog (Onboarding, Prävention, Chat-Regeln)
6. CAV-Worker: LMS-Abschluss → Keycloak `schulung:*`
7. Matrix + Element, Gruppenabgleich inkl. `schulung:chat`
8. Nextcloud + Collabora nur Backoffice
9. Mein Konto: Beitrag, Tokens, Schulungsstatus
10. SEPA über Zahlungsdienst
11. Zammad-Vorlagen nur für Amts-Onboarding
12. Abgabe und Limits (an Prävention knüpfen)
13. Track & Trace, §-26-Export
