<!-- ======================= WiFiStone-Retweet ======================= -->
> 🧩 **WiFiStone-Retweet** — ce serveur MCP fait partie de l'écosystème
> **WiFiStone-Retweet**. Repo vendored : binaires Beacon pré-packagés,
> install sans fetch réseau, aucun secret dans le dépôt.
<!-- ================================================================== -->

# beacon-mcp

Serveur MCP **Beacon** ([beacon.sh](https://beacon.sh)) — expose l'activité
locale des agents IA (prompts, tool calls, commandes, fichiers, approvals,
mémoire cross-harness) au format JSON compatible Wazuh, via MCP stdio ou HTTP.

Binaires vendored : **Beacon v1.3.20** (`2f1f0161`, build 2026-09-22),
release officielle beacon.sh — Windows x64.

## Contenu

```
bin/            beacon.exe, beacon-hooks.exe, beacon-otelcol.exe (v1.3.20)
scripts/        install-endpoint.ps1, uninstall-endpoint.ps1 (vendor)
config/         exemples assainis (aucun secret, chemins génériques)
docs/           référence CLI complète générée via `beacon docs`
```

## Install

```powershell
# 1. Copier bin/ vers %ProgramFiles%\Beacon\bin  (ou tout dossier du PATH)
# 2. Endpoint per-user (recommandé) :
beacon endpoint install
#    ou endpoint système (service Windows) :
.\scripts\install-endpoint.ps1

# Vérifier
beacon endpoint status
beacon mcp doctor
```

## Config MCP client

```json
{
  "mcpServers": {
    "beacon": {
      "command": "beacon",
      "args": ["mcp", "serve", "--transport", "stdio"]
    }
  }
}
```

Variante Windows absolue : `"command": "C:/Program Files/Beacon/bin/beacon.exe"`.
Transport HTTP loopback : `beacon mcp serve --transport http` (défaut `127.0.0.1:8766`).

## Outils exposés

| Tool | Rôle |
|---|---|
| `search_activity` | Recherche filtrée dans les logs d'activité agents |
| `summarize_activity` | Synthèse d'activité sur une fenêtre temps/filtres |
| `get_activity_event` | Détail d'un événement par ID |
| `list_activity_filters` | Valeurs de filtres disponibles dans les logs |
| `search_memory` | Recherche dans la mémoire projet approuvée |
| `get_memory` / `get_memory_context` | Lecture mémoire cross-harness |

## Sécurité

- **Aucun secret dans le repo** : config env-first (`BEACON_*`), les valeurs
  vivent hors dépôt.
- **Données runtime jamais commitées** : `~/.beacon/` (logs, `traces.db`,
  `memory.db`, spool, `auth/session.json`) — voir `.gitignore`.
- Le collector otelcol tourne en loopback (4317/4318) avec
  `redact_secrets: true` — les secrets sont masqués dans les logs eux-mêmes.
- Endpoint `endpoint/` et scripts : binaires et scripts officiels beacon.sh,
  hash non modifié depuis l'install.

## Docs

Référence CLI complète : [`docs/beacon.md`](docs/beacon.md)
(190 pages générées par `beacon docs` — commandes `endpoint`, `mcp`, `ci`,
`cloud`, `rules`, `scan`, `memory`, `traces`, `token-usage`…).

## Licence

Code/structure du repo : MIT. Binaires `bin/*.exe` et scripts `scripts/`
: © Beacon (beacon.sh), vendored tels quels pour déploiement reproductible —
pas de redistribution modifiée.
