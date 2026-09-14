# Tickets: Zammad

Kein eigenes Helpdesk. **Zammad** (Open Source) mit Keycloak-OIDC. Last bereits mehrere hundert Tickets pro Monat — genau das, wofür Zammad gebaut ist.

Portal bleibt der Einstieg: Link „Support“. Mitglieder und Ämter landen in Zammad, nicht in Nextcloud Deck.

## Warum Zammad in Ordnung ist

- Queue, Nummer, Filter, Suche, Zuweisung, Anhänge, Wissenstexte
- OIDC gegen Keycloak, Gruppen als Agenten-Rollen
- Organisationen in Zammad ≈ Zweigvereine
- Agenten = Support und Ämter; Kunden = Mitglieder
- Kein Rad: ihr konfiguriert, ihr schreibt keinen Ticketkern

Nicht Nextcloud (Mitglieder haben kein Konto). Nicht FastAPI-Helpdesk.

## Amtsaufgaben

Zammad kann das über **Ticket-Vorlagen / Makros / Checklisten**, nicht über ein zweites Aufgabenprodukt.

Beispiel: neuer Vorstand → Makro oder Portal-Knopf (dünner API-Aufruf) legt ein Ticket mit Checkliste an:

1. Notartermin Vereinsregister
2. Gewerberegisterauszug
3. Bankvollmacht
4. Präventionsunterlagen
5. Zugänge prüfen

Zuweisung an die Person oder an die Zammad-Gruppe „Vorstand Wanne-Eickel“. Ergebnis (PDF, Termin) darf in Nextcloud landen; der Auftrag bleibt das Zammad-Ticket.

Der einzige eigene Code: optional Portal ruft Zammad-API auf, wenn eine Keycloak-Gruppe „Vorstand“ neu ist. Die Queue selbst ist Zammad.

## Betrieb

Eigener Host `help.example`, eigene Postgres, Redis wie in der Zammad-Doku. Backup der Zammad-Daten. Agenten mit MFA.

Portal verlinkt, speichert keine Ticket-Texte doppelt.

Nichtmitglieder und die Öffentlichkeit erreichen denselben Helpdesk ohne Login: Webformular und Funktionspostfächer beim Mailanbieter, siehe [mail.md](mail.md).
