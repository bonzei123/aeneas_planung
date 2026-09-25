# Optionale Module

Kein zweiter Kern. Was hier steht, ist **Compose-Overlay**, gleicher Traefik, gleicher Realm `aeneas`. Ein Verein oder der Gesamtverein schaltet es ein; andere laufen ohne. Kein eigenes GitHub-Repo pro Produkt, keine 180 Instanzen.

Kern bleibt: Keycloak, Portal, CAV, Zammad, Frappe Learning, Matrix (Synapse + MAS + Element Web / Element X), Nextcloud (nur Backoffice). Optional kommt **nach** dem Testserver-Kern, nicht davor. Die eigene Store-App ist kein Extra-Homeserver: derselbe Chat, nur andere Hülle.

Filter für jedes Overlay: Docker Compose, Keycloak-OIDC, kein Campus-/k8s-Stack, Solo-Betrieb erklärbar, Rechte aus denselben Gruppen (`verein:*`, `mitgliedschaft:*`, `rolle:*`, `schulung:*`). Kein Keycloak-UMA. Ausnahme eigene App: Client-Build, kein zweites Compose-Produkt.

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

## Eigene App

**Ja, optional**, und kein zweiter Chat-Server.

Mitglieder chatten schon im Kern über **Element Web** auf `chat.example` und **Element X** auf dem Handy (MAS → Keycloak, Config `brand` / Logo). Das reicht als gebrandeter Einstieg (Browser, PWA, Store-X).

Darüber hinaus, wenn der Verein den **Store-Namen** selbst will:

| Stufe | Was | Extra-Dienst? |
| --- | --- | --- |
| Web (Kern) | Element Web, Name und Logo in der Config | nein |
| PWA | dieselbe Web-Oberfläche, Icon | nein |
| Handy (Kern) | Store-App **Element X**, Homeserver `chat.example` | nein |
| Store unter Vereinsnamen | **Element Pro** White-Label (X), Element baut, ihr published | nein, Kauf-Abo |

Classic-Element aus den Stores nicht mehr einplanen (Sunset 31.12.2026). MAS gehört zum Kern, nicht zum White-Label. Details: [ci-cd.md](ci-cd.md).

## Mitgliederversammlung / Wahlen

**Ja, später.** OpenSlides oder etwas in dieser Klasse ist nötig, sobald es eine MV mit Anträgen und Personenwahl gibt — nicht im Kern-Compose, nicht vor Chat/Tickets/Schulung.

Software macht keine Wahl „rechtskonform“. Dafür Satzung (elektronische MV / Stimmabgabe, § 32 BGB), Wahlordnung, Einladung, Quorum, Stimmrecht aus der Mitgliederliste. OpenSlides dokumentiert den Ablauf (Tagesordnung, Redeliste, Anträge, offen oder geheim, Ergebnis, Export). Das ist das deutsche Vereinsprodukt für genau diesen Tag; Verbände nutzen es so.

Nicht ersetzen durch:

- **POLYAS** und ähnliche Wahl-SaaS — nur Urne, oft Zertifikat. Teuer, zweites Login. Nur wenn ein Anwalt eine zertifizierte Online-Wahl ohne Versammlungsbetrieb verlangt.
- **Antragsgrün** — nur Anträge.
- **Jitsi / Element Call** — Bild und Ton der Versammlung, nicht die Stimme.
- Umfrage im Portal oder LimeSurvey als Vorstandswahl.

Betrieb: extra Host `wahl.example` (OpenSlides 4 ist ein eigener Container-Schwarm) **oder** SaaS nur um den Versammlungstag. Login zuerst lokal (`superadmin` + Teilnehmer-PINs), nicht Keycloak. Nicht 180 Instanzen.

## Rundschreiben und Mailinglisten

Zwei verschiedene Dinge. **Mailingliste** (alle schreiben allen) ist bei euch **Matrix**. Dafür kein Mailman, kein Verteiler in Thunderbird.

**Newsletter** (Vorstand an viele, HTML, Abmelden, Double-Opt-in) nur, wenn Leute per E-Mail erreicht werden müssen, die nicht in Element lesen. Sonst Ankündigungsraum.

**Nicht über mailbox.org.** Das Postfach ist SMTP/IMAP für Keycloak, Zammad, Behörden. Massenversand über dieselbe Domain verbrennt die Zustellung der Passwort-Mails. mailbox hat kein Listenprodukt; der Support verweist auf **JPBerlin** (Heinlein, Berlin). Technisch geht SMTP, ein Vereinsnewsletter gehört trotzdem nicht ins selbe Fach.

Wenn E-Mail-Rundschreiben sein muss: **Listmonk** (Compose, OIDC, `news.example`) plus **eigener Versandweg** (ESP: JPBerlin, rapidmail, Brevo, SES — nicht das mailbox-Fach von `help@`). Kleine Listen über mailbox-SMTP nur nach Absprache mit dem Anbieter, eigene Absenderdomain idealerweise getrennt. Nicht: BCC an `vorstand@`.

SaaS-Newsletter statt Listmonk ist erlaubt, wenn ihr keinen Extra-Dienst wollen — dann kein Overlay.

## Weitere optionale Dienste

Nur was zum Verein passt und den Filter oben übersteht.

| Modul | Produkt | Für wen | Urteil |
| --- | --- | --- | --- |
| Mitglieder-Handbuch | BookStack | aktiv, nach Schulung laut Verein | ja, siehe oben |
| Amts-Ablage / Scan | Nextcloud Group Folders | nur Amt | Kern, nicht Overlay; kein Paperless |
| Video-Sprechstunde | **Jitsi** hinter Traefik, JWT an Keycloak | aktiv + Amt | ja; Bild/Ton der MV, nicht die Wahl. BBB wie Moodle zu schwer |
| E-Mail-Rundschreiben | **Listmonk** + eigener Versandweg | wer nicht in Matrix liest | ja, nur wenn E-Mail sein muss; nicht über mailbox.org |
| Amts-Passwörter | **Vaultwarden** | `rolle:*` / `backoffice`, MFA | ja; nicht für Mitglieder |
| Mitgliederversammlung / Wahl | **OpenSlides** | aktiv, oft nur am Versammlungstag | später, aber nötig; extra Host oder SaaS |
| Eigene Chat-App | Element Pro White-Label (X) | aktiv mit `schulung:chat` | ja, optional; Kern ist schon Web + X |
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
| Mailman / Mailcow-Listen | Diskussionslisten = Matrix; Newsletter nicht über den Vereins-Posteingang |
| POLYAS als Standard-MV | nur Wahl-SaaS; OpenSlides bleibt das Versammlungsprodukt |
| BigBlueButton, Moodle, ILIAS | RAM und Pflege |
| Zweite Cloud / OnlyOffice extra | Collabora in Nextcloud reicht |
| n8n / Camunda / Nextcloud-Windmill | Aufträge bleiben Zammad; Buchen in DATEV/Lexoffice |
| Akaunting / Invoice Ninja / Dolibarr als Rechnungs-ERP | zweites Geldsystem; Dolibarr = ERPNext-Klasse |
| Immich, persönlicher Foto-Stack | kein Vereinszweck |
| ERPNext, CiviCRM | Fachkern ist der CAV |
| Classic-Element als Dauerweg | Store-App ohne X entfällt Ende 2026; MAS + Element X sind Kern |

## Hosts (nur wenn Overlay an)

| Host | Dienst |
| --- | --- |
| `wiki.example` | BookStack |
| `meet.example` | Jitsi |
| `news.example` | Listmonk (Admin); Zustellung per ESP/SMTP, nicht mailbox.org |
| `pass.example` | Vaultwarden |
| `wahl.example` | OpenSlides (oder SaaS-URL des Anbieters) |
| — | eigene App: kein extra Host, Client auf `chat.example` |

Portal-Linktree zeigt nur, was der Verein eingeschaltet hat und wozu Token/Gruppe passt.

## Anschluss an SSO

Jeder optionale Client: OIDC, PKCE, Groups-Mapper Claim `groups`, Redirect nur die eigene Callback-URL. Kein zweites Benutzerverzeichnis. Abschalten = Overlay runter, Client in Keycloak disabled, Link aus dem Portal.

Backup: wie Kern, Volume plus DB des Overlays, restic nach außen. [betrieb.md](betrieb.md).
