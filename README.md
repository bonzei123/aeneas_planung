# Aeneas Planung

Planungsrepository für die Server- und Softwarelandschaft eines **Gesamtvereins**, der mehrere Cannabis-Anbauvereinigungen (Zweigvereine) nach dem Konsumcannabisgesetz (KCanG) organisiert.

Hier liegt die Architektur, nicht der Anwendungscode. Der KCanG-Fachkern und das Portal entstehen später in eigenen Repositories, in Python (FastAPI), von Hand gepflegt.

## Zielbild in einem Satz

Ein Keycloak-Konto für jede Person. Mitglieder arbeiten im Portal, im CAV-Kern und in Matrix. Nextcloud mit Collabora bleibt dem Backoffice vorbehalten (Gesamtverein-Mitarbeiter, Vorstände, Prävention, Ausgabe und vergleichbare Ämter).

## Dokumente

| Datei | Inhalt |
| --- | --- |
| [docs/entscheide.md](docs/entscheide.md) | Festlegungen (cannaUNITY, Nextcloud, ERPNext, Matrix) |
| [docs/architektur.md](docs/architektur.md) | Hosts, Docker-Compose, Datenflüsse |
| [docs/sso-matrix.md](docs/sso-matrix.md) | SSO, Keycloak-Gruppen, Matrix-Spaces |
| [docs/fachkern.md](docs/fachkern.md) | CAV-Kern, Portal, Mein Konto, was selbst gebaut wird |
| [docs/beitrag-sepa.md](docs/beitrag-sepa.md) | Tokens, Lastschrift, monatlicher Einzug |
| [docs/betrieb.md](docs/betrieb.md) | Linux, Backup, Skalierung auf viele Konten |

## Nicht das Ziel

- Kein Nextcloud-Plugin als Abgabe- oder Anbausystem
- Kein ERPNext als Mitgliederportal
- Kein OpenDesk/Kubernetes als Einstieg
- Keine Garantie „KCanG-konform“ durch Software allein; der Verein bleibt verantwortlich
