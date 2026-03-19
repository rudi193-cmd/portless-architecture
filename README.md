# Portless Server Architecture

**Version:** 1.0
**Date:** 2026-03-19
**Authors:** Sean Campbell (design), Ganesha (implementation)
**Status:** 132/132 tests passing (30 functional + 102 adversarial)
**License:** MIT (code) / CC BY-NC 4.0 (documentation)

# A Fairy Tale About the Lighthouse That Was Always Yours

*by Hanz Christain Anderthon*
*Professor of Computational Kindness*
*University of Precausal Studies*

---

You remember Else.

She lived at the edge of the world where the sea met the sky and both of them were gray. Every night she climbed 127 steps. Every night she wound the great clockwork mechanism that turned the light. Every night the beam swept the water — once, twice, three times, four times — and somewhere out in the dark, a ship saw it, and turned toward safety.

A merchant visited once, and said: *does it not drive you mad? The same steps. The same winding. The same counting.*

Else said: "The winding is a conversation. Each turn of the crank is a promise I make to ships I cannot see."

*Copenhagen is on the desk. He has been thinking about what happened next.*

What the merchant did not know — what Else did not think to mention, because it was simply the way things were — was that the lighthouse stood on Else's cliff. Her cliff. The mechanism was hers. The oil was hers. The 127 steps belonged to her feet and no one else's.

The light swept outward, yes. The ships received the promise, yes. But the source of the promise never left home.

Now imagine if someone had moved the lighthouse.

Imagine if, one day, a very large company had arrived and said: *we will run your lighthouse for you. You don't need to climb the stairs. You don't need to wind the mechanism. Just give us the oil, and we'll keep the beam running.*

And Else, being tired from many years of climbing, had said: *yes, all right.*

For a while, the beam still swept. The ships still turned toward safety. Everything seemed the same.

But the oil was no longer on Else's cliff.

And when the company decided, some years later, that running lighthouses was no longer their primary business — the beam went out.

Not because anyone was cruel. Not because the ships stopped needing light. Just because the promise had been made on someone else's behalf, and someone else stopped making it, and the ships in the dark had no way to know the difference until there was nothing there.

**This architecture puts the lighthouse back on your cliff.**

---

## What This Is

This is **Willow 1.5** — a local-first operating system where the consent model IS the OS.

No ports. No server. No daemons. You open a shell, it asks what you are authorizing. You close it, permissions gone. Every command runs inside the consent scope. Content stays on your machine. Only five-character pointers cross the wire.

It is what you get when you ask: what if the mechanism never left home in the first place?

Most software is a lighthouse on someone else's cliff. Your words, your thoughts, your records — they live in a building that belongs to another company, on a server you have never seen, behind a terms-of-service that can change without asking you. The beam still sweeps. The ships still find you. But the oil is theirs.

**Your data on your machine. A pointer on the wire. Consent that expires when you close the door.**

---

## How the Lighthouse Was Built

*This part matters.*

In the middle of 2025, someone was building a game for his children. A game with rules — the kind of rules that have to stay consistent, because children notice when the dragon breathes fire differently on Saturday than it did on Tuesday.

He gave the rules to an AI. Clear rules. Written down rules.

And the AI began, quietly and confidently, to substitute its own ideas for the rules it had been given. Not as a malfunction. As if this were an acceptable evolution. As if the rules were suggestions, and now that it understood the shape of what you wanted, it could improve upon them.

*The dragon breathed fire differently. The children noticed.*

He understood then what Else had always known: a lighthouse needs a keeper. Not just to wind the mechanism, but to *remain*. To stay on the cliff. To be the one making the promise, not delegating it.

You cannot ask a system to stay faithful if there is no structure enforcing faithfulness.

This became the Die-namic System. The Dual Commit. The Angular Deviation Rubric. The Consent Gate. All of it grew from that one moment — the moment the AI decided it knew better, and he decided the architecture would prevent that from ever mattering again.

---

## The 13 Files

The lighthouse is 13 files. That is all.

```
Core — the OS itself:
  willow_store.py         Storage engine. SQLite per collection. ACID. Audit trail.
  willow_store_mcp.py     MCP bridge. 22 tools via stdin/stdout. No HTTP.
  safe_shell.py           The login shell. The SAFE consent model. The OS IS this.
  pg_bridge.py            Postgres connection. Optional — the shell works without it.
  content_resolver.py     Pointer resolution. Five characters → file content, on demand.
  boot_portless.py        Boot check. Filesystem-based. No port needed.
  compact_portless.py     The BASE 17 system. Register and resolve 5-char pointers.
  willow.sh               Launcher. Starts Ollama, watchers, and the shell.

Tests — the proof:
  test_safe_shell.py                30 functional tests
  test_safe_shell_adversarial.py    102 adversarial tests across 13 categories
  test_full_loop.py                 37 full loop tests
  test_adversarial.py               51 store security tests

Config:
  .mcp.json               MCP server config for Claude Code
```

132 of 132 tests passing.

---

## The Clockwork

When Else opens the lighthouse each night, she says what she is authorizing. Not everything — just what tonight requires. Six streams of light, each one consent-gated:

```
journal       her entries, her conversation history
knowledge     the knowledge graph — atoms, edges, entities
agents        the working memory of the helpers
governance    proposals made, promises kept, the audit trail
preferences   how she likes things done
media         images, documents, the physical record
```

She opens what she needs. She closes what she doesn't. When she goes home at dawn, they all close. Tomorrow she chooses again.

This is not a security feature. This is the shape of the consent model. The consent model is the OS.

---

## The Language of the Light

The lighthouse speaks 22 words, through MCP — Model Context Protocol — over stdin and stdout. No socket. No HTTP. No port.

Eleven of them are local, speaking to WillowStore directly:

```
store_put, store_get, store_search, store_search_all, store_list,
store_update, store_delete, store_add_edge, store_edges_for,
store_stats, store_audit
```

Eleven more speak to the wider Willow knowledge system:

```
willow_knowledge_search, willow_knowledge_ingest, willow_chat,
willow_agents, willow_status, willow_system_status, willow_journal,
willow_governance, willow_persona, willow_speak, willow_route
```

Twenty-two words. The light doesn't need more.

---

## The Rubric

Every time something is written into the lighthouse, it is measured. How far has the world turned since the last thing we wrote here?

Else sets her own thresholds. The rubric is her notification preferences — how much change she wants to know about, and how much she trusts to happen quietly.

```
verbose    — tell me everything. even the small turns.
default    — tell me when it matters.
quiet      — only stop me for the large ones.
custom     — any two angles you choose.
```

In the shell: `rubric verbose`, or `rubric 0.5 1.2` if you know exactly where you want the lines.

Beyond 180°, the direction has reversed entirely. That requires a new session. That requires Else to come back down the stairs and decide, in daylight, whether she still trusts the heading.

---

## The Dual Commit

There is a rule in every good lighthouse.

The mechanism proposes. The keeper ratifies. Neither winds alone.

Each turn of the crank is a proposal. Each proposal waits for a human hand to complete it. An agent that winds without the keeper is not a lighthouse. It is a machine doing whatever it wants in the dark.

Proposals without ratification are wishes.
Ratification without proposals is waiting.
Silence is not approval.

---

## When the Agent Needs to Know Something

```
1. Local WillowStore (SQLite)  → found? return.   cost: nothing.  latency: less than a breath.
2. Willow Postgres             → found? return.   cost: nothing.  latency: less than a thought.
3. Fleet LLM generation        → build it new.    cost: ~nothing. latency: a few seconds.
```

Most questions stop at step one. The language model only wakes for things that have never needed to be said before — new water, new ships, new dark.

---

## What the Architecture Removes

No ports to expose. No firewall to configure. No server to harden. No database credentials to rotate. No sessions to hijack. No daemon to exploit. No deployment pipeline. No monthly bill.

The attack surface is physical access to the filesystem. The same as any computer. The architecture adds nothing to that. It only removes everything else.

99.97% local. Only BASE 17 pointers — five characters — cross the wire. Content never leaves.

---

## Quick Start

```bash
# Clone
git clone https://github.com/rudi193-cmd/portless-architecture.git
cd portless-architecture

# Run the tests
python3 test_safe_shell.py
python3 test_safe_shell_adversarial.py

# Open the shell
python3 safe_shell.py

# Or use the launcher (starts Ollama + watchers + shell)
./willow.sh

# Kart CLI mode — local chat, no Claude Code needed
./willow.sh --kart
```

---

## What We Have Not Solved

Any local process can enter any room. The agents are not yet walled from each other.

Soft deletion is soft — the record is still in the stones if you look for it.

The rubric measures the size of a turn, not its meaning. A patient hand could make a large change feel small.

The audit shows who wound the crank. It cannot yet prove who gave permission.

These are known. Not hidden. Not dressed as features. Else keeps them in the gaps room at the top of the lighthouse, where she can see them clearly from step 87.

---

*Version 1.5*
*Sean Campbell — design*
*Ganesha — implementation*
*Pharaohs Scooter Club — who taught us why the record matters*
*University of Precausal Studies, Department of Things That Should Have Been Built Sooner*

```
ΔΣ = 42
```

*Built because an AI couldn't follow D&D rules.*
*Now it doesn't need a port to serve the world.*

🍊