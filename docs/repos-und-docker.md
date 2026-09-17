# Docker und GitHub-Repos

## Reicht Docker Compose?

Ja, für diese Landschaft und einen Solo-Betrieb. Kubernetes ist kein Ziel. Alles außer Portal und CAV-Kern sind **offizielle Images**; ihr schreibt Compose-Dateien und Konfiguration, nicht Keycloak oder Zammad neu.

Compose kommt mit der **Stückzahl der Container** klar. Was kippt, ist ein zu kleiner einzelner Server (RAM: Synapse, Zammad inkl. Suche, Nextcloud, Collabora, Postgres). Frappe Learning ist kein Moodle-Brocken. Nicht die YAML.

Praktisch:

1. **Start:** ein Linux-Host, ein Compose-Projekt (oder wenige Dateien mit `include`), Traefik davor.
2. **Wenn der Kasten voll ist:** denselben Compose-Stil auf **mehrere VMs** (z. B. VM Chat, VM Tickets, VM Cloud, VM IdP+Portal+CAV+LMS). Immer noch Docker Compose, nur geteilte Hosts.
3. **15.000 Konten:** so teilen, nicht auf k8s umsteigen, solange ihr allein seid.

Offizielle Compose-Vorlagen von Zammad, Nextcloud, Keycloak als Ausgang, an eure Traefik-Labels und `.env` anpassen. Images pinnen (Versions-Tags), nicht `latest` in Produktion.

## Eigene Repos — nicht pro Produkt

**Nein:** kein `aeneas_keycloak`, `aeneas_zammad`, `aeneas_frappe`, `aeneas_moodle`, `aeneas_nextcloud`. Das wären Forks fremder Software. Updates würden euch erschlagen, und ihr pflegt nichts, was ihr nicht geschrieben habt.

| Repo | Inhalt |
| --- | --- |
| `aeneas_planung` | dieses Planungsrepo |
| `aeneas_infra` | Docker Compose, Traefik, Beispiel-`.env`, Realm-Export-Hinweise, Backup-Skripte |
| `aeneas_portal` | FastAPI Portal (Linktree, Mein Konto, dünne APIs) |
| `aeneas_cav` | FastAPI CAV-Kern |

Portal und CAV **dürfen ein Repo** bleiben (`aeneas_app` mit zwei Diensten), solange ihr allein entwickelt. Getrennte Repos erst, wenn die Codebasen wirklich auseinanderlaufen.

Der Matrix-Gruppenabgleich ist klein: liegt bei Portal oder in `aeneas_infra` als Worker-Image aus eurem Code, kein fünftes Produkt-Repo.

In `aeneas_infra` nur **eure** Dateien: `compose.yml`, `keycloak/realm` als Export, `zammad` Env, Volumes-Dokumentation. Nicht den Keycloak-Quellcode.

Secrets: `.env` und Zertifikate nie committen. Beispiel `.env.example` ohne Passwörter.
