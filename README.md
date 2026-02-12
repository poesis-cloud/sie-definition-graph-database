# SIE Definition Graph Database

This module packages the **Definition Graph Store** for SIE as a graph database (Neo4j) deployment.

In SIE's six-plane, two-layer architecture, the Definition Graph Store provides **graph traversal access** over GSM (Generative System Model) definitions. It is CDC-synchronized from the primary Definition Store (PostgreSQL), which remains the source of truth for asserted definitions.

## Purpose

GSM definitions form a rich graph of systemic primitives (Systems, Components, Mechanisms, Interfaces, Channels, Interactions, etc.) with typed relationships (governance authority, structural composition, behavioral coupling). While PostgreSQL handles ACID transactions and relational integrity, Neo4j provides:

- **Graph traversal queries**: navigate PPRC chains (Principle → Policy → Rule → Control → Mechanism) efficiently
- **Impact analysis**: trace which primitives are affected by a definition change (multi-hop traversal)
- **Coherence analysis**: detect circular dependencies, unreachable definitions, structural anomalies
- **Architecture visualization**: feed graph data to diagram renderers for governance visualization
- **Cross-system governance**: traverse SystemGovernance relationships (heterarchy) across supersystem/subsystem boundaries

## Architecture

```
┌──────────────────────────┐      CDC       ┌──────────────────────────┐
│  Definition Store        │ ─────────────→ │  Definition Graph Store  │
│  (PostgreSQL + JSONB)    │  (Debezium /   │  (Neo4j)                 │
│  Source of truth         │   pg-to-neo4j) │  Read-only projection    │
└──────────────────────────┘                └──────────────────────────┘
```

- **Write path**: all mutations go through the Definition Manager → PostgreSQL → CDC → Neo4j
- **Read path**: graph traversal queries go directly to Neo4j; relational/transactional queries go to PostgreSQL
- Neo4j is a **read-only projection** — never written to directly by application code

## What's in here

- `definition/`: graph schema definitions, Cypher migration scripts, CDC mapping configuration
- `README.md`: this file
- `LICENSE`: proprietary license

## Notes

This repo is in a design-oriented phase; deployment scaffolding (Helm charts, Docker compose) will be added when the graph schema stabilizes.
