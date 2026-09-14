# Mitgliedsbeitrag, Tokens, SEPA-Lastschrift

## Vereinsmodell (aktuell, änderbar)

- Fester Mitgliedsbeitrag **10 €** pro Monat, Lastschrift.
- Zusätzlich kann das Mitglied **manuell Geld einzahlen** (beliebiger Betrag über den Zahlungsdienstleister, z. B. Zahlungslink / Überweisung). Beides wird als **Tokens** gutgeschrieben.
- Satzung und Token-Logik können sich ändern (z. B. nach Reboot). Die Oberfläche bleibt: Portal → Mein Konto.

Der Zahlungsdienstleister zieht bzw. nimmt nur **Mitgliedsbeitrag / Einzahlung** entgegen. Tokens sind interne Vereinsgutschrift. Abgabe in Gramm bleibt im CAV-Kern.

## Keine eigene Bankverbindung bauen

SEPA-Lastschrift selbst gegen die Hausbank (EBICS, `pain.008`) ist möglich, für den Start zu schwer: Gläubiger-ID, Mandatsverwaltung, Vorabankündigung, Rücklastschriften, XML, Bankfreigaben.

Stattdessen ein **Zahlungsdienstleister mit SEPA-Lastschrift und wiederkehrenden Zahlungen**, angebunden per API und Webhook.

Geeignete Richtung (Auswahl, kein Vendor-Lock in der Planung):

| Anbieter | Typisch dafür |
| --- | --- |
| Mollie | SEPA-Lastschrift, wiederkehrend, DE/NL, übersichtliche API |
| GoCardless | Lastschrift als Kernprodukt, Mandate, Rückläufer |
| Stripe | SEPA Debit plus Billing, eher Karten-Ökosystem |

Der Verein braucht trotzdem: **Gläubiger-Identifikationsnummer** (Bundesbank), ein **SEPA-Mandat** je Mitglied (Text, Zeitpunkt, IP bzw. schriftlich), Vorabankündigung (wann und welcher Betrag), Aufbewahrung des Mandats, Vertrag zur Auftragsverarbeitung mit dem Dienstleister.

## Was das Portal tut, was der Dienstleister tut

Mitglied im Portal:

1. Mandat für den **festen 10-€-Beitrag** erteilen.
2. Optional: Button „einzahlen“ → Zahlungslink / Überweisung über denselben Dienstleister, Betrag frei.
3. Token-Stand und Belege sehen (10 € plus Einzahlungen).
4. Mandat kündigen / Lastschrift stoppen.

Dienstleister:

- speichert das Mandat (Referenz, IBAN-Token, Status)
- führt die Lastschrift aus
- meldet Erfolg, Ablehnung, Rückbuchung per Webhook

Euer Worker bzw. das Abo (monatlich 10 €):

- nicht „die Bank anrufen“, sondern die **API des Dienstleisters**
- fester Betrag 10 € je aktivem Mandat
- bei Webhook „bezahlt“ (Beitrag oder Einzahlung): Tokens gutschreiben, Beleg erzeugen
- bei Rücklastschrift: Tokens nicht gutschreiben bzw. Storno, Mitglied sperren oder mahnen nach Vereinsregel

## Monatlich anstoßen

Der **10-€-Beitrag** ist ein festes Abo beim Dienstleister (Intervall monatlich) oder ein Worker, der immer denselben Betrag einzieht. Betrag ändert sich nicht pro Mitglied — das ist der einfache Pfad.

**Zusätzliche Einzahlungen** stößt das Mitglied selbst an (Zahlungslink). Kein Cron nötig. Webhook „bezahlt“ → Tokens.

Zeitlich: Lastschrift braucht Vorlauf. Stichtag lieber „Einzug zum 1., Ankündigung ein paar Tage vorher“.

## grobe Transaktionskosten (Listenpreise, Stand 2026, ohne Gewähr)

Nur erfolgreiche Zahlungen; Rückläufer extra. Kontenprüfung und Cannabis-Branche können zur Ablehnung führen — vor Integration den Dienstleister fragen.

| Weg | Mollie (DE) | Stripe (DE) | GoCardless Standard (inland) |
| --- | --- | --- | --- |
| SEPA-Lastschrift | 0,35 € | 0,35 € | 1 % + 0,20 €, max. 1 € (bei 10 € ≈ 0,30 €) |
| SEPA-Überweisung / Bank Transfer | 0,25 € | Prozentmodell (typisch 0,5 %, prüfen) | eher Lastschrift, plus Pay by Bank |
| Karte (EWR-Verbraucher) | 1,80 % + 0,25 € | 1,5 % + 0,25 € | — |

Beispiel: 500 Mitglieder × 10 € Lastschrift bei Mollie ≈ **175 € Gebühren im Monat**. Bei 15.000 Konten dieselbe Logik ≈ **5.250 € im Monat** nur für den Mindestbeitrag. Deshalb Lastschrift/Überweisung, keine Karte für 10 € (Karte wäre gut 0,43 € plus Prozent).

Klassische Sammellastschrift über die Vereinsbank (`pain.008`) ist oft günstiger pro Stück, aber mehr eigene Arbeit. Für den Start: Dienstleister; bei vielen Mitgliedern die Bankgebühren danebenlegen.

Karten und Klarna fürs Aufladen vermeiden (teuer, unnötiges Konsumentenrisiko). Einzahlen: SEPA-Überweisung oder Lastschrift.

## Technisch im Stack

- Secrets des Dienstleisters nur in der Server-Umgebung, nicht im Git.
- Webhook-URL hinter Traefik, Signatur des Dienstleisters prüfen.
- Idempotenz: dieselbe Zahlungs-ID nicht zweimal Tokens gutschreiben.
- Mandatsreferenz und Zahlungs-ID in der CAV-Datenbank speichern, IBAN nicht im Klartext, wenn der Dienstleister ein Token liefert.

Buchhaltung (DATEV/Lexoffice) bekommt später den Zahlungseingang, nicht den Token-Stand. Token-Konto ist Vereinslogik, kein DATEV-Kontoersatz.
