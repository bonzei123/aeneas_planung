# Betrieb und Größe

## Einstieg

- Ein Linux-Server (Debian oder Ubuntu LTS)
- Docker Compose
- Tägliches Backup nach außen (restic oder borg), inkl. Postgres-Dump und Nextcloud-/Synapse-Medien
- Updates der Container bewusst, nicht ungesehen automatisch auf Produktion

## 15.000 Konten

15.000 Konten sind ein Dachverband (viele Zweigvereine à höchstens 500 Mitglieder), nicht ein Verein. Entscheidend ist Gleichzeitigkeit, nicht die Zahl in der User-Tabelle.

| Teil | 15.000 Konten auf einem Compose-Host |
| --- | --- |
| Keycloak | unkritisch |
| CAV-Kern + Portal | unkritisch, wenn `verein_id` indexiert ist |
| Matrix | machbar, wenn geschlossen und ohne Video für alle gleichzeitig |
| Nextcloud für alle Mitglieder | nein, deshalb nur Backoffice |
| Nextcloud für wenige hundert Ämter | ja, Compose reicht lange |

Skalierungspfad, falls nötig: Postgres vom App-Host trennen, Synapse-Worker, Nextcloud unverändert klein lassen. Kubernetes ist kein Startziel.

## Sicherheit, grob

- Homeserver nicht an das öffentliche Matrix-Netz anbinden
- Raumverzeichnis nicht öffentlich
- MFA für Backoffice und Vorstände
- Getrennte Secrets je Dienst, keine Klartext-Passwörter im Git
- Zugriff auf CAV-Admin und Nextcloud nur aus dem Amt, nicht „alle Mitglieder sind Admin irgendwo“

## Reihenfolge zum Einschalten

1. Linux, Traefik, Keycloak, ein Testuser
2. Portal mit „eingeloggt, Gruppen sichtbar“
3. CAV: Mandant + Mitglied aus Token
4. Matrix + Element, SSO-Login
5. Gruppenabgleich für ein Vereinsbeispiel (Wanne-Eickel)
6. Nextcloud nur für eine Backoffice-Gruppe, Collabora
7. Mein Konto: Beitrag, Tokens, Belege
8. SEPA-Mandat über Zahlungsdienst, monatlicher Worker
9. Abgabe und Limits
10. Track & Trace, dann §-26-Export
