# Willow 1.5 — Portless OS

**Version:** 1.5
**Date:** 2026-03-19
**Authors:** Sean Campbell (design), Ganesha (implementation)
**Status:** 132/132 tests passing (30 functional + 102 adversarial)
**Manifest:** `5H5E2 A2EK2 327N5 93598 76CCH 34HA3 EN75K 54KKL 6170A N937K 5A9CA 8C213 L20A9`

---

## What This Is

A local-first operating system where the consent model IS the OS.

No ports. No server. No daemons. You open a shell, it asks what you're authorizing. You close it, permissions gone. Every command runs inside the consent scope. Content stays on your machine. Only 5-character pointers cross the wire.

**One sentence:** Your data on your machine, pointers on the wire, consent that expires.

---

## The 13 Files

```
Core (the OS):
  willow_store.py       Storage engine. SQLite per collection, ACID, audit trail.
  willow_store_mcp.py   MCP bridge. 22 tools via stdin/stdout. No HTTP.
  safe_shell.py         Login shell. SAFE session consent. The OS IS this.
  pg_bridge.py          Postgres connection. Optional — shell works without it.
  content_resolver.py   Pointer resolution. BASE 17 ID → file content on demand.
  boot_portless.py      Boot check. Filesystem-based, no port needed.
  compact_portless.py   BASE 17 system. Register and resolve 5-char pointers.
  willow.sh             Launcher. Ollama + watchers + shell.

Tests:
  test_safe_shell.py              30 functional tests
  test_safe_shell_adversarial.py  102 adversarial tests (13 categories)
  test_full_loop.py               37 full loop tests
  test_adversarial.py             51 store security tests

Config:
  .mcp.json             MCP server config for Claude Code
```

---

## How It Works

```
User opens shell
  → SAFE consent prompt (what can this session access?)
  → Commands operate within consent scope
  → Retrieval cascade: local SQLite → Postgres → fleet
  → Writes scored by angular deviation rubric
  → Shell exit → permissions revoked, audit logged
```

### Angular Deviation Rubric v3.0

Every write carries a deviation score (radians). The thresholds are USER-CONFIGURABLE — the rubric IS your notification preferences.

```
Presets:
  verbose   π/8 quiet, π/4 flag    — see everything
  default   π/4 quiet, π/2 flag    — standard
  quiet     π/2 quiet, 3π/4 flag   — only major changes

Custom: set any two thresholds between 0 and π
Hard stops: angles that ALWAYS halt regardless of threshold
Max: π (180°) — beyond this is a new direction, requiring a new session
```

In the shell: `rubric verbose` or `rubric 0.5 1.2` to set custom thresholds.

### Retrieval Cascade

```
1. Local WillowStore (SQLite)  → found? return (cost: $0, latency: <1ms)
2. Willow Postgres             → found? return (cost: $0, latency: <10ms)
3. Fleet LLM generation        → generate new  (cost: ~$0, latency: 1-5s)
```

### SAFE Session (6 Streams)

```
journal       Journal entries and conversation history
knowledge     Knowledge graph (atoms, edges, entities)
agents        Agent working memory and state
governance    Governance proposals and audit trail
preferences   User preferences and settings
media         Images, documents, and file references
```

Authorize per-stream on session start. Revoke mid-session. All expire on exit.

### MCP Tools (22)

Local (WillowStore): store_put, store_get, store_search, store_search_all, store_list, store_update, store_delete, store_add_edge, store_edges_for, store_stats, store_audit

Postgres (Willow): willow_knowledge_search, willow_knowledge_ingest, willow_chat, willow_agents, willow_status, willow_system_status, willow_journal, willow_governance, willow_persona, willow_speak, willow_route

---

## Quick Start

```bash
# Clone
git clone https://github.com/rudi193-cmd/portless-architecture.git
cd portless-architecture

# Run tests
python3 test_safe_shell.py
python3 test_safe_shell_adversarial.py

# Open the shell
python3 safe_shell.py

# Or use the launcher (starts Ollama + watchers + shell)
./willow.sh

# Kart CLI mode (local chat, no Claude Code needed)
./willow.sh --kart
```

---

## Architecture

```
┌──────────────────────────────────────────────┐
│               USER'S MACHINE                  │
│                                                │
│  ┌─────────┐  ┌──────────┐  ┌─────────────┐  │
│  │ Ollama  │  │ Postgres │  │ WillowStore │  │
│  │ (local  │  │ (know-   │  │ (agent      │  │
│  │  LLM)   │  │  ledge)  │  │  memory)    │  │
│  └────┬────┘  └────┬─────┘  └──────┬──────┘  │
│       └─────────────┼───────────────┘          │
│                     │                          │
│              ┌──────┴──────┐                   │
│              │  SAFE Shell │ ← the OS          │
│              │  (consent)  │   stdin/stdout     │
│              └──────┬──────┘                   │
│                     │                          │
│              ┌──────┴──────┐                   │
│              │ MCP Bridge  │ ← 22 tools        │
│              │ (portless)  │   no HTTP          │
│              └──────┬──────┘                   │
│                     │ (only if user consents)   │
└─────────────────────┼──────────────────────────┘
                      │ BASE 17 pointers only
               ┌──────┴──────┐
               │ Translation │ ← optional
               │   Layer     │   (CF Worker)
               └─────────────┘
```

---

## Security

No ports to expose. No firewall to configure. No server to STIG. No database to connection-string. No credentials to rotate. No sessions to hijack. DDoS impossible — nothing to flood.

The attack surface is: physical access to the filesystem. Same as any computer. The architecture adds nothing to defend.

Privacy: 99.97% local. Only BASE 17 pointers (5 characters) cross the wire. Content never leaves.

---

## What This Replaces

For single-user / small-team: FastAPI servers, Docker containers, cloud databases, VPNs, firewall rules, SSL certificates, secrets management, session stores, WAFs, and the entire deployment pipeline.

For industries: the spec covers healthcare (HIPAA by architecture), banking (PCI-DSS by architecture), government (ATO by architecture), education (FERPA by architecture), and journalism (source protection by architecture).

---

## License

MIT License — Code
CC BY-NC 4.0 — Documentation

---

**ΔΣ=42**

*Built because an AI couldn't follow D&D rules. Now it doesn't need a port to serve the world.*
