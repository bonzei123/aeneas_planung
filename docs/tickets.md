# Tickets: Zammad

Kein eigenes Helpdesk. **Zammad** (Open Source) mit Keycloak-OIDC. Last bereits mehrere hundert Tickets pro Monat — genau das, wofür Zammad gebaut ist.

Portal: Link „Support“ für Eingeloggte. Öffentlichkeit und Nichtmitglieder brauchen **kein Konto**.

Mitglieder sehen Zammad nicht als Vereins-Hauptweg. Keine Ticket-Vorlage als „Mitglied werden“ — das wirkt nach Helpdesk, nicht nach Verein. Aufnahme: [fachkern.md](fachkern.md), [mail.md](mail.md).

## Formulare ohne Login

Kontakt ohne Keycloak: Zammad-Webformulare (oder eingebettet auf der Vereinsseite) mit den Feldern, die ihr braucht. **Nicht** der Aufnahmeantrag.

Mehrere Formulare, jedes fest an eine **Gruppe**:

- Support
- Vorstand (Kontakt, keine Aufnahme-UI)
- Präventionsbeauftragte
- je Zweigverein, wenn nötig

Die anfragende Person klickt zusammen, was ihr anbietet (Verein, Thema, Text, Anhang). Es wird ein Ticket in der richtigen Queue. Antwort per Mail an die angegebene Adresse, ohne Element.

Eingeloggte Mitglieder können zusätzlich über OIDC in Zammad ihre bestehenden Tickets sehen. Agenten (Amt) sind immer mit Konto und MFA drin.

Nicht Nextcloud (Mitglieder haben kein Konto). Nicht FastAPI-Helpdesk.

Zammad-**Knowledge Base** für Support-Artikel und kurze FAQs. Lebendes Vereinshandbuch (Satzung kommentiert, Prozesse): optionales Wiki, [optionale-module.md](optionale-module.md). Pflichtstoff mit Test: Frappe Learning.

Queue, Nummer, Filter, Suche, Zuweisung, Anhänge: fertig in Zammad. OIDC für Agenten und eingeloggte Mitglieder. Organisationen ≈ Zweigvereine.

## Amtsaufgaben

Zammad-**Vorlagen / Makros / Checklisten** nur intern (Amt), nicht für die Straße.

Beispiel: neuer Vorstand → Makro oder Portal-Knopf (dünner API-Aufruf) legt ein Ticket mit Checkliste an:

1. Notartermin Vereinsregister
2. Gewerberegisterauszug
3. Bankvollmacht
4. Präventionsunterlagen
5. Zugänge prüfen

Zuweisung an die Person oder an die Zammad-Gruppe „Vorstand Wanne-Eickel“. Ergebnis (PDF, Termin, Scan) darf in Nextcloud landen; der Auftrag bleibt das Zammad-Ticket.

## Eingangsrechnungen (FOSS, ohne Extra-ERP)

Kein Akaunting, Invoice Ninja, Dolibarr, ERPNext. Das wären ein zweites Geldsystem neben Beitragstokens und später DATEV, meist ohne brauchbares Keycloak, oft ein Mini-ERP.

Der Workflow sitzt in **Zammad** (habt ihr schon, OIDC, Queues, 4-Augen über zwei Gruppen). Die Datei sitzt in **Nextcloud**. Buchen bleibt **DATEV/Lexoffice** (nicht FOSS, Steuerberater) — [optionale-module.md](optionale-module.md).

1. Rechnung kommt per Mail ins Funktionspostfach → IMAP in Zammad, Gruppe **Finanzen** des Vereins (Organisation = Zweigverein).
2. Scan/PDF zusätzlich nach Nextcloud `Posteingang/<verein>` (Group Folder). Ticket verweist auf den Pfad oder hängt dieselbe Datei an.
3. Objekt / Pflichtfelder: Kreditor, Betrag, Währung, Leistungsdatum, Fällig, Kostenart, `verein_id`.
4. Makro-Checkliste: Betrag ok → Verein richtig → 4-Augen (z. B. Ausgabe legt an, `rolle:vorstand` gibt frei) → gezahlt → in DATEV übergeben → Ticket zu.
5. Zahlen: Vereinsbank / Steuerberater, nicht CAV-Tokens, nicht Mollie-Mitgliedsbeitrag.
6. Archiv: PDF bleibt in Nextcloud (Aufbewahrung); Zammad hält die Akte der Freigabe.

Ausgangsrechnungen an Mitglieder gibt es in diesem Modell kaum (Beitrag läuft über den Zahlungsdienst). Interne Weiterbelastung zwischen Vereinen: dasselbe Ticketmuster, kein Shop.

XRechnung/ZUGFeRD: als Anhang behandeln. Parser-Worker erst, wenn wirklich Behörden-E-Rechnungen in Menge ankommen — kein Extra-DMS.

Optional: nach Portal-Aufnahmeantrag ein **internes** Ticket „Antrag prüfen“ in der Vorstands-Queue. Die antragstellende Person arbeitet nicht in Zammad.

Der einzige eigene Code: optional Portal ruft Zammad-API auf (neues Amt, neuer Antrag). Die Queue selbst ist Zammad.

## Betrieb

Eigener Host `help.example`, Overlay `aeneas_infra/compose.zammad.yml`, eigene Postgres-Instanz plus Elasticsearch. Backup der Zammad-Volumes. Agenten mit MFA.

Portal verlinkt, speichert keine Ticket-Texte doppelt.

Nichtmitglieder und die Öffentlichkeit erreichen denselben Helpdesk ohne Login: Webformular und Funktionspostfächer beim Mailanbieter, siehe [mail.md](mail.md).
