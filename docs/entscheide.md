# Festlegungen

Stand der Planung. Änderungen gehören als Commit in dieses Repository, nicht nur in einen Chat.

## Produktform

Aeneas ist eine **eigene Landschaft**, kein Fork von [cannaUNITY](https://github.com/saschadaemgen/cannaUNITY) als Produkt.

cannaUNITY (archiviert, Pre-Alpha, Django/React, UniFi/SIMATIC, eigene Buchhaltung) ist höchstens Ideenquelle für Track & Trace (Samen bis Ausgabe). Der Code wird nicht als Basis übernommen: Alpha, Hardware-Kopplung, zu breite Featureliste, fremder Kern.

## Drei Schichten

1. **Identität:** Keycloak (ein Realm, mehrere OIDC-Clients).
2. **Kollaboration nach Zielgruppe:**
   - Mitglieder: Matrix (Element), geschlossen, ohne Federation.
   - Backoffice / Ämter: Nextcloud + Collabora + Kalender.
3. **Fachkern:** eigenes FastAPI (Portal + CAV-Mandanten), PostgreSQL.

## Nextcloud

Nextcloud **lohnt sich**, aber nur als Backoffice. Gemeinsames Bearbeiten (Collabora), Kalender, Group Folders für Satzung, Behördenpost, Dienstpläne.

Nextcloud **lohnt sich nicht** als Heimat von 15.000 Mitgliedern (Desktop-Sync, Talk, persönliche Rechnungsordner für alle). Mitgliedsbelege liegen im CAV-Portal.

Der Nextcloud-OIDC-Client in Keycloak ist auf Backoffice-Gruppen beschränkt. Mitglieder bekommen kein Nextcloud-Konto.

## Matrix statt Talk / Discord

Mitgliederchat ist Matrix + Element, nicht Nextcloud Talk. Spaces bilden Gesamtverein, Bundesland und Ortsverein ab. Abgabe, Limits und Chargen bleiben im CAV-Kern, nicht im Chat.

## ERPNext

Kein ERPNext als Portal oder als Ersatz für den Fachkern. Buchhaltung später über DATEV oder Lexoffice, nicht nachgebaut.

## OpenDesk

OpenDesk ist dasselbe Gedankenmodell (SSO, Nextcloud, Element), aber Kubernetes-Betrieb. Einstieg ist Docker Compose auf Linux. OpenDesk nur, wenn später bewusst Groupware in dem Umfang gebraucht wird.

## Code, den niemand erklärt

Der CAV-Kern, das Portal und der Matrix-Gruppenabgleich werden selbst geschrieben und sollen Zeile für Zeile erklärbar sein. Fertigsoftware (Keycloak, Synapse, Nextcloud, Traefik, Postgres) wird konfiguriert, nicht neu implementiert.
