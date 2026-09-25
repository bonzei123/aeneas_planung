# CI/CD — Corporate Identity und Corporate Design

In diesem Repository meint **CI/CD** Erscheinungsbild und Marke (Corporate Identity / Corporate Design), nicht GitHub-Pipelines.

Ziel: Mitglieder und Ämter sehen durchgängig denselben Namen, dasselbe Logo, dieselben Farben — so weit die jeweilige Software das hergibt. Vollständiges White-Label (eigene Store-Apps ohne Fremdname) ist nicht überall kostenlos.

Zentrale Dateien (Logo, Farbe, Schrift) einmal festlegen und in jedes Produkt übernehmen. Portal und CAV-Kern sind zu 100 % euer Design, weil der Code euch gehört.

## Kurzüberblick

| Produkt | Web brandbar | Eigene Handy-App unter Vereinsnamen |
| --- | --- | --- |
| Portal / CAV | vollständig | ja, wenn ihr eine baut |
| Keycloak (Login) | ja, Login-Theme | entfällt (Browser) |
| Matrix | kein UI | entfällt |
| Element Web | Logo, Name, Farben, Welcome — kein komplettes Private Label | entfällt (Browser / PWA) |
| Element X (Store) | Homeserver voreinstellbar, Name bleibt „Element X“ | White-Label nur als **Element Pro** (Kauf) |
| Zammad | Name, Logo, CSS | entfällt (Browser) |
| Frappe Learning | Name, Logo, Farben in Settings | entfällt (Browser) |
| Nextcloud | Theming-App (Name, Logo, Farbe) | offizielle Clients oder GmbH-Branding |
| BookStack / Jitsi | jeweiliges Theming, soweit vorhanden | entfällt |

## Keycloak

Ja. Sichtbar für alle: die **Login-Seite** (und E-Mails).

- Eigenes Theme (erweitert `keycloak`/`base`): Logo, CSS, Texte, optional Keycloakify (React).
- Account Console ebenfalls thembar; Admin-Konsole braucht ihr nicht öffentlich zu branden.
- Realm-Einstellungen: Anzeigename.

Das ist der erste Eindruck vor dem Portal. Lohnt sich früh.

## Matrix und Element

**Matrix** ist das Protokoll (Synapse der Server). Es hat keine Mitglieder-Oberfläche. Branden könnt ihr nur den **Client**.

**Element** ist der übliche Client dazu, von der Element-Firma (früher Riot):

- Element Web (Browser, das setzt ihr selbst, Kern)
- Element X (Handy-Stores; Classic-App ist zum 31.12.2026 aus den Stores)
- optional Element Pro (White-Label auf X, Abo)

Web: in `config.json` u. a. `brand` (Anzeigename statt „Element“), Logo auf der Login-Seite, Hintergrund, Farben/`custom_themes`, eigene Welcome-Seite. Die Doku sagt ausdrücklich: **kein vollständiges Private Label**, aber ein klarer Vereinslook ist üblich.

Handy: Store-App **Element X**, Homeserver `chat.example`. Login über MAS → Keycloak (derselbe Button wie im Web). Die Store-Identität bleibt „Element“, bis ihr **Element Pro** kauft (Element baut die App, ihr published unter eigenem Store-Konto). Eigenen Classic-Fork nicht mehr einplanen. [optionale-module.md](optionale-module.md).

Andere Matrix-Clients (FluffyChat, Cinny) sind teils leichter zu themen, dann habt ihr aber zwei Welten.

## Zammad

Ja, ohne Fork. Unter Einstellungen → Branding: **Produktname**, **Logo** (Login und Oberfläche). Zusätzlich eigenes CSS. Reicht für Helpdesk unter Vereinsnamen („Aeneas Support“ statt „Zammad Helpdesk“).

## Frappe Learning

Ja, ohne Moodle-Theme-Ballast. Unter Settings → Branding: Name, Logo, Favicon, Farben. Login über Keycloak, also dasselbe Login-Theme wie der Rest.

Keine Store-App. Start: Browser, gebrandet. Kurse sollen nach Verein aussehen, nicht nach „Frappe“. Moodle-Themes und Plugin-Stores entfallen mit dem Produkt.

## Nextcloud

Ja, eingebaute **Theming-App**: Instanzname, Slogan, Logo, Primärfarbe, Login-Hintergrund, Impressum. Nutzer-Themes könnt ihr abschalten, damit das CI hält.

Desktop- und Handy-Clients aus den Stores heißen **Nextcloud**. Fertig gebrandete Sync-Clients verkauft die Nextcloud GmbH. Für wenige hundert Backoffice-Nutzer reicht oft die offizielle App plus gebrandete Web-UI.

Collabora: nur schwach thembar; im iFrame der Cloud oft akzeptabel.

Optionale Overlays (BookStack, Listmonk): Name und Logo in deren Settings, Farben soweit die Software das hergibt. Kein pixelgleiches Portal. Details der Produkte: [optionale-module.md](optionale-module.md).

## Was ihr selbst setzt (Portal)

Hauptmenü, Mein Konto, CAV-Oberfläche: euer HTML/CSS. Das soll die **Führungs-CI** sein (Farbe, Logo, Schrift). Die anderen Apps nähern sich dem an, ersetzen es nicht pixelgleich.

Praktische Reihenfolge: Logo+Farbpalette definieren → Keycloak-Login → Portal → Element Web → Zammad → Frappe Learning → Nextcloud → optionale Overlays. Element-Pro-Store-App nicht als erstes versprechen.
