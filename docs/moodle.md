# Moodle: Schulungen und Mitwirkung

Kein eigenes LMS. **Moodle** mit Keycloak-OIDC. Kurse, Tutorials, jährliche Pflichtschulungen (analog Compliance), Nachweis der Teilnahme.

KCanG verlangt Mitwirkung der Mitglieder und Prävention/Jugendschutz im Verein. Moodle ist der Ort für Schulungsinhalte und **Teilnahmenachweis**, nicht der CAV-Kern und nicht Nextcloud.

## Was Moodle liefert

- Kurse, Lektionen, Tests, Bescheinigungen
- Abschlussverfolgung (wer hat die Jahresschulung bestanden)
- Einschreibung über Cohorts, die zu Keycloak-Gruppen passen (Verein, Bundesland, Amt)
- Alle Mitglieder, nicht nur Backoffice

Portal: Link „Schulungen“. CAV kann später über die Moodle-API nur den Status „Kurs X im Jahr Y abgeschlossen“ lesen — kein zweites Kursprodukt.

## Jährliche Schulung

Vorlage: Pflichtkurs pro Kalenderjahr (Prävention, Jugendschutz, Vereinspraxis, Ausgabe-Regeln für Ämter). Mitglied sieht im Portal unter Mein Konto grob „Schulung 2026: offen / bestanden“, Quelle Moodle.

Ohne bestandene Pflichtschulung kann der Verein interne Regeln knüpfen (z. B. keine Abgabe, kein Amt). Das ist Vereinspolitik; Moodle liefert nur den Nachweis.

## Betrieb

Host `learn.example`, eigene Datenbank, Dateispeicher für Kursmaterial. OIDC-Client in Keycloak für alle aktiven Mitglieder. Keine Federation nach moodle.org-Netzen nötig.

Backup von DB und `moodledata`.
