# Festlegungen

Stand der Planung. Änderungen gehören als Commit in dieses Repository, nicht nur in einen Chat.

## Produktform

Aeneas ist eine **eigene Landschaft**, kein Fork von [cannaUNITY](https://github.com/saschadaemgen/cannaUNITY) als Produkt.

cannaUNITY ist höchstens Ideenquelle für Track & Trace. Der Code wird nicht übernommen.

**Regel:** Nur Portal und CAV-Kern (plus der kleine Matrix-Gruppenabgleich) werden selbst geschrieben. Alles andere ist fertige Software hinter Keycloak.

## Schichten

1. **Identität:** Keycloak (ein Realm, viele OIDC-Clients).
2. **Fertige Apps:** Matrix/Element (Chat), Zammad (Tickets), Moodle (Schulung), Nextcloud+Collabora (nur Backoffice).
3. **Eigener Fachkern:** FastAPI Portal + CAV-Mandanten, PostgreSQL.

## Nextcloud

Nur Backoffice (Ämter, Vorstand, Gesamtverein). Mitglieder bekommen kein Nextcloud-Konto. Belege im Portal.

## Matrix

Mitgliederchat, Spaces je Gesamtverein / Land / Ort. Kein Talk, kein Discord-SaaS. Kanalrechte: [sso-matrix.md](sso-matrix.md).

## Mitgliedsbeitrag und Tokens

- **10 €** fest im Monat per Lastschrift, Tokens.
- Zusätzlich manuelle Einzahlung per Zahlungslink.
- Mein Konto im Portal. Einzug: Dienstleister, [beitrag-sepa.md](beitrag-sepa.md).

Der Zahlungsdienst sieht nur Beitrag, keine Gramm.

## Support: Zammad

Hunderte Tickets pro Monat: **Zammad**, OIDC, kein eigenes Helpdesk, kein Nextcloud-Plugin. Amts-Onboarding über Zammad-Vorlagen/Makros. [tickets.md](tickets.md).

## Schulung: Moodle

Tutorials und jährliche Pflichtschulungen (Mitwirkung / Prävention analog Compliance). Nachweis in Moodle, Link und Status im Portal. [moodle.md](moodle.md).

## ERPNext

Nein als Portal oder Fachkern. Buchhaltung später DATEV/Lexoffice.

## OpenDesk

Gleiches Gedankenmodell, Kubernetes. Einstieg bleibt Compose.

## Code, den niemand erklärt

Selbst: Portal, CAV-Kern, Matrix-Gruppenabgleich. Konfigurieren: Keycloak, Synapse, Zammad, Moodle, Nextcloud, Traefik, Postgres, Zahlungsdienst.
