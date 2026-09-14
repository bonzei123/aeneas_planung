# Betrieb und Größe

## Einstieg

- Ein Linux-Server (Debian oder Ubuntu LTS), Docker Compose
- Backup nach außen (restic/borg): Postgres-Dumps plus Medien von Nextcloud, Moodle, Zammad, Synapse
- Container-Updates bewusst

## 15.000 Konten

Viele Zweigvereine à höchstens 500 Mitglieder. Last ist Gleichzeitigkeit und Ticket-/Kursbetrieb, nicht die User-Tabelle.

| Teil | Ein Compose-Host |
| --- | --- |
| Keycloak, CAV, Portal | ja, indexiert |
| Matrix geschlossen | ja, ohne Massen-Video |
| Zammad, hunderte Tickets/Monat | ja; bei 15k eher eigene VM + Suche laut Zammad-Doku |
| Moodle für alle Mitglieder | ja; bei Last eigene VM |
| Nextcloud nur wenige hundert Ämter | ja |
| Nextcloud für alle Mitglieder | nein |

Skalierung: Dienste vom App-Host trennen, nicht Kubernetes als Start.

## Sicherheit, grob

- Matrix ohne öffentliche Federation
- Zammad: Mitglieder sind Kunden, nicht Agenten
- Moodle: Kurse intern
- Nextcloud-Client nur Backoffice-Gruppen
- MFA für Agenten, Vorstände, Moodle-Admins
- Secrets nicht im Git
- Kein eigener Mailserver; SMTP/IMAP beim Anbieter, siehe [mail.md](mail.md)

## Reihenfolge zum Einschalten

1. Linux, Traefik, Keycloak
2. Portal: Login, Linktree
3. CAV: Mandant + Mitglied aus Token
4. Zammad + OIDC, Support-Queue
5. Moodle + OIDC, ein Pflichtkurs
6. Matrix + Element, Gruppenabgleich
7. Nextcloud + Collabora nur Backoffice
8. Mein Konto: Beitrag, Tokens, Schulungsstatus
9. SEPA über Zahlungsdienst
10. Zammad-Vorlagen für Amts-Onboarding
11. Abgabe und Limits
12. Track & Trace, §-26-Export
