# Optionale Module

Kein zweiter Kern. Was hier steht, ist **Compose-Overlay**, gleicher Traefik, gleicher Realm `aeneas`. Ein Verein oder der Gesamtverein schaltet es ein; andere laufen ohne. Kein eigenes GitHub-Repo pro Produkt, keine 180 Instanzen.

Kern bleibt: Keycloak, Portal, CAV, Zammad, Frappe Learning, Matrix/Element, Nextcloud (nur Backoffice). Optional kommt **nach** dem Testserver-Kern, nicht davor.

Filter für jedes Overlay: Docker Compose, Keycloak-OIDC, kein Campus-/k8s-Stack, Solo-Betrieb erklärbar, Rechte aus denselben Gruppen (`verein:*`, `mitgliedschaft:*`, `rolle:*`, `schulung:*`). Kein Keycloak-UMA.

## Wiki

**Ja, optional**, wenn Satzung, FAQ und Prozesshandbuch die Zammad-Knowledge-Base und die LMS-Kurse sprengen.

Heute schon Inhalt, ohne Extra-Dienst:

| Inhalt | Ort |
| --- | --- |
| Pflicht lesen und bestehen | Frappe Learning |
| Support-Artikel, „wie stelle ich ein Ticket“ | Zammad Knowledge Base |
| Amtsakten, interne Dateien | Nextcloud |
| Kurzer Linktree-Text | Portal |

Eigenes Wiki erst, wenn Vorstände **lebende Handbücher** pflegen (Satzung kommentiert, Ausgabe-Prozess, Präventionskonzept) und Mitglieder das lesen sollen, ohne Nextcloud-Konto.

**Produkt: BookStack** (eine Instanz, `wiki.example`). Bücher/Regale je Verein oder Thema, Rollen auf Keycloak-Gruppen mappen, OIDC, Docker, UI auf Deutsch tragbar. Wiki.js nur, wenn Markdown und optional Git-Sync gewünscht sind — dann immer noch kein Mitglieder-Git.

Nicht: MediaWiki, XWiki, Outline (zu viel Drumherum). Nicht: Nextcloud Collectives für alle Mitglieder (widerspricht „kein Mitglieder-Nextcloud“).

Portal: Link „Wiki“, nur `mitgliedschaft:aktiv` (plus `schulung:onboarding`, wenn der Verein das verlangt). Öffentliche Satzung kann eine **Leseseite ohne Login** sein; der Rest hinter SSO.

## Git

**Nein als Mitgliederdienst.** Mitglieder versionieren keine Repos. Satzung und Konzepte gehören ins Wiki oder nach Nextcloud, nicht nach Merge-Requests.

Aeneas-Code bleibt **GitHub** (`aeneas_*`). Internes Forgejo/Gitea nur, wenn der Gesamtverein selbst Software entwickelt — das ist Betrieb der Plattform, kein Vereinsmodul. **GitLab weglassen** (RAM und Pflege wie Moodle).

Wiki.js darf intern in ein privates Git spiegeln. Das ersetzt keinen Git-Server für 20k Konten.

## Dokumentenablage / DMS

**Nur Nextcloud.** Kein Paperless daneben.

Nextcloud Group Folders sind die Amts-Ablage: Ordner, Rechte aus Keycloak (`rolle:*`, `verein:*`, `backoffice`), Collabora, Kalender. Zammad bleibt der Auftrag; fertige PDFs und Scans landen in der Cloud. Mitglieder bekommen weiter **kein** Cloud-Konto; ihre Belege liegen im Portal/CAV.

**Flow reicht nicht als Rechnungs-BPM.** Die eingebaute Flow-Engine plus App **Approval**: Datei in Ordner → Tag, Benachrichtigung, eine oder verkettete Freigabe (z. B. Ausgabe, dann Vorstand). Keine Beträge, kein Skonto, kein DATEV-Konto, kein Zahlungslauf, kein Eingangsrechnungsbuch. Nextcloud Flow **mit Windmill** (AppAPI) wäre genau der Extra-Stack, den wir bei n8n/Camunda ausgeschlossen haben — nicht einplanen.

Rechnungsfreigabe im Verein — **kein neues Produkt**:

1. Mail/Scan → Zammad-Gruppe Finanzen (Organisation = Zweigverein) plus PDF in Nextcloud `Posteingang/<verein>`.
2. Pflichtfelder und Checkliste im Ticket (Betrag, Kreditor, 4-Augen).
3. Zahlen über die Vereinsbank; buchen später in **DATEV/Lexoffice** (nicht FOSS, Steuerberater).
4. Nextcloud Approval höchstens als Datei-Häkchen, nicht als Buchhaltung.

Akaunting, InvoiceShelf, Invoice Ninja: kein oder schwaches Keycloak, Fokus Ausgangsrechnung. Dolibarr hat OIDC, ist aber ERP (wie ERPNext) — Dateien, Tickets und Mandanten hätten wir dreifach. Details: [tickets.md](tickets.md).

Paperless-ngx **nicht** einplanen. Dieselbe PDF in Cloud *und* Paperless ist doppelte Wahrheit. Scan → Ordner „Posteingang“ in Nextcloud reicht.

Nicht: Alfresco, Mayan, OpenKM, SeedDMS.

## Weitere optionale Dienste

Nur was zum Verein passt und den Filter oben übersteht.

| Modul | Produkt | Für wen | Urteil |
| --- | --- | --- | --- |
| Mitglieder-Handbuch | BookStack | aktiv, nach Schulung laut Verein | ja, siehe oben |
| Amts-Ablage / Scan | Nextcloud Group Folders | nur Amt | Kern, nicht Overlay; kein Paperless |
| Video-Sprechstunde / MV | **Jitsi** hinter Traefik, JWT an Keycloak | aktiv + Amt | ja; BigBlueButton wie Moodle zu schwer |
| E-Mail-Rundschreiben | **Listmonk** | wer nicht in Matrix liest | ja; SMTP bleibt beim Anbieter, kein Mailserver |
| Amts-Passwörter | **Vaultwarden** | `rolle:*` / `backoffice`, MFA | ja; nicht für Mitglieder |
| Mitgliederversammlung | **OpenSlides** | aktiv, oft nur am Versammlungstag | später; deutsches Vereinsprodukt, extra Host |
| Monitoring | Uptime Kuma o. ä. | nur Betrieb | ja, kein Mitglieder-Feature |
| Mitglieder-Termine | zunächst Portal oder Matrix; sonst Mobilizon | aktiv | erst wenn der Kalender in Nextcloud den Mitgliedern fehlt |
| Umfragen | Zammad / Portal klein; sonst LimeSurvey | aktiv | nur bei echtem Bedarf |
| eSignatur Mandat/Satzung | Docuseal o. ä. | Beitrag, Amt | später, Zahlungsdienst zuerst |

**Nicht** (Doppel oder zu schwer):

| Idee | Warum nicht |
| --- | --- |
| Paperless-ngx neben Nextcloud | doppelte Ablage, zwei Rechte, kein Sync |
| GitLab / Forgejo für Mitglieder | siehe Git |
| Discourse / Forum | Matrix ist der Mitgliederkanal |
| BigBlueButton, Moodle, ILIAS | RAM und Pflege |
| Zweite Cloud / OnlyOffice extra | Collabora in Nextcloud reicht |
| n8n / Camunda / Nextcloud-Windmill | Aufträge bleiben Zammad; Buchen in DATEV/Lexoffice |
| Akaunting / Invoice Ninja / Dolibarr als Rechnungs-ERP | zweites Geldsystem; Dolibarr = ERPNext-Klasse |
| Immich, persönlicher Foto-Stack | kein Vereinszweck |
| ERPNext, CiviCRM | Fachkern ist der CAV |
| Öffentliches CMS (Ghost, WordPress) | Portal + Impressum; Marketing-Site darf extern bleiben |

## Hosts (nur wenn Overlay an)

| Host | Dienst |
| --- | --- |
| `wiki.example` | BookStack |
| `meet.example` | Jitsi |
| `news.example` | Listmonk (Admin); Zustellung per SMTP |
| `pass.example` | Vaultwarden |

Portal-Linktree zeigt nur, was der Verein eingeschaltet hat und wozu Token/Gruppe passt.

## Anschluss an SSO

Jeder optionale Client: OIDC, PKCE, Groups-Mapper Claim `groups`, Redirect nur die eigene Callback-URL. Kein zweites Benutzerverzeichnis. Abschalten = Overlay runter, Client in Keycloak disabled, Link aus dem Portal.

Backup: wie Kern, Volume plus DB des Overlays, restic nach außen. [betrieb.md](betrieb.md).
