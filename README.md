# EMA v3.4 — Episodic Memory & Metacognitive Agent

[![Architecture](https://shields.io)](./WHITEPAPER.md)
[![Status](https://shields.io)](#roadmap)
[![Stack](https://shields.io)](./WHITEPAPER.md)
[![License](https://shields.io)](./LICENSE)

> **Repository Status:** Architecture & Technical Specification Release (`v3.4`). The complete formal specification, neurobiological mapping, and mathematical formulation are available now. Source code packaging is underway and will be released in staged milestones (see [Roadmap](#roadmap)).

> **Beyond stateless chatbots:** A neuro-symbolic cognitive architecture that decouples **executive self-regulation** from **text generation**—combining 5D Episodic Memory modeled after the hippocampal complex, a Multi-Hop Knowledge Graph, Homeostatic Drives, an Autonomous Stream of Consciousness with a silent Inner Scratchpad, Theory of Mind, and a Closed-Loop 6D Metacognitive Controller.

**Read the Full Technical Whitepaper:**
- [English Version (`WHITEPAPER.md`)](./WHITEPAPER.md)
- [Wersja Polska (`WHITEPAPER_PL.md`)](./WHITEPAPER_PL.md)

---

## Core Architectural Pillars

In typical LLM applications, "self-reflection" is an ephemeral textual illusion that vanishes once the context window closes. **EMA v3.4** reverses this hierarchy: the LLM serves solely as a semantic parser and natural-language realization organ, while persistent identity and behavioral control are governed by deterministic mathematical engines:

- **5-Dimensional Episodic & Semantic Memory (Hippocampal Model):** Simultaneous encoding across dense vector space (**Qdrant** 128D cosine), a multi-hop relational graph (**Neo4j** with dedicated `system:self`, `:MetaObservation`, and `:UserModel` nodes), and local audit storage with non-destructive belief revision (`is_active`, `superseded_by`).
- **Closed-Loop Metacognition (`MPL`):** Continuously tracks a **6D Meta-State Vector** (`emotional_volatility`, `epistemic_speculation`, `decision_reflectiveness`, `belief_stability`, `goal_starvation`, `environmental_friction`) in **TimescaleDB**, triggers structured LLM self-diagnosis on anomalies, and applies **TTL hysteresis controls** (`CAUTIOUS_TONE`, `DEPRECATION_BRAKE`, `GOAL_PRIORITY`, `VAD_ATTRIBUTION_FILTER`).
- **Theory of Mind & Silent Inner Scratchpad:** Models the interlocutor's knowledge level, goals, and communication style (`:UserModel`), while filtering internal monologue through a silent scratchpad before vocalizing thoughts aloud.
- **Homeostasis & Autonomous Thought Loop:** Regulated by **Curiosity**, **Cognitive Discomfort**, and **Computational Fatigue**. During idle periods, EMA spontaneously audits contradictions, verifies uncertain graph triples against Wikipedia, and enters **Auto-Sleep** to consolidate memory and crystallize new rules.
- **Document-First Epistemic Retrieval:** Multi-format ingestion (`.pdf`, `.docx`, `.txt`, `.md`, and remote `URL`) with strict epistemic priority—grounding answers in verified local knowledge before querying external web tools.

---

## High-Level System Architecture

```mermaid
flowchart TB
    subgraph IN["1. Ingestion, CLI & Observability Layer"]
        CLI["Executive CLI (ema/cli.py)<br/>/tom | /cypher | /prod | /meta | /eksport_mpl | /wczytaj | /sen | /ja"]
        DOC["DocumentIngestor (doc_loader.py)<br/>PDF (page slicing) | DOCX (text+tables) | TXT/MD/CSV | Remote URL"]
        WEB["Web Dashboard v2.4 (:8050)<br/>Top-N Graph Canvas | Memory Curation | 6D MPL Charts"]
        EXP["KnowledgeGraphExporter & TTS<br/>JSON | GraphML | MPL CSV (UTF-8 BOM) | SAPI5 FIFO Voice"]
    end

    subgraph PERC_LAYER["2. Perception, Theory of Mind & 5D Encoding"]
        CLASS["classify_utterance()<br/>FACT | RESOLUTION | QUERY | is_correction | resolved_entity"]
        TOM["TheoryOfMindEngine (user_model.py)<br/>observe_turn() -> expertise_level, known_topics, knowledge_gaps, active_goals"]
        ATT["AttentionGateway (compute_attention_weights)<br/>Dynamic 5D Drawer Routing"]
        ENC["OllamaEncoders (qwen2.5:7b)<br/>5D Feature & SPO Triple Extraction"]
    end

    subgraph HIPPO["3. 5-Drawer Hippocampal Memory Complex (MemoryItem)"]
        D1["1. Semantic Drawer<br/>semantic_vector (128D Cosine)<br/>~ Dentate Gyrus (Pattern Separation)"]
        D2["2. Graph Drawer<br/>GraphTriple (S-P-O, confidence) & system:self<br/>~ CA3 Field (Pattern Completion)"]
        D3["3. Episodic Drawer<br/>timestamp, provenance, is_active, superseded_by, decay_factor<br/>~ CA1 Time Cells"]
        D4["4. Affective Drawer<br/>VAD Coordinates & Cognitive Discomfort<br/>~ Amygdala Salience"]
        D5["5. Functional Drawer<br/>Operational Utility & Goal Alignment<br/>~ Prefrontal Cortex (vmPFC)"]
    end

    subgraph EXEC["4. Epistemic Retrieval & Anti-Hallucination Pipeline (process_query)"]
        PRIO1["Stage 1: Local Document Priority<br/>_find_document_evidence() + find_graph_paths(1..3 hops)<br/>-> [ANSWERED_FROM_LOCAL_DOCUMENTS]"]
        PRIO2["Stage 2: Epistemic Curiosity Tool<br/>WebKnowledgeTool (Open-Meteo Weather & Wikipedia)<br/>-> [RESOLVED_VIA_WEB_SEARCH]"]
        CONF_RES["resolve_entity_conflict()<br/>Non-destructive archiving (guarded by DEPRECATION_BRAKE)"]
        OBS["Doubter & Observer (observer.py)<br/>Contradiction Check & Hypothesis Veto<br/>-> [SYNTHESIZED_WITH_NEO4J_AND_QDRANT]"]
    end

    subgraph AUTO["5. Autonomous Stream of Consciousness, Homeostasis & Sleep"]
        HOMEO["HomeostasisEngine<br/>Curiosity (C) | Cognitive Discomfort (D) | Fatigue (F)"]
        SCRATCH["_filter_via_inner_scratchpad()<br/>Silent Monologue Filter + doc:* Isolation (_is_meaningful_memory)"]
        WIKI_BG["_autonomous_wiki_verification()<br/>Background check of triples (conf < 0.91 -> 0.92)"]
        SLEEP["sleep_phase() & Auto-Sleep (F >= 0.95)<br/>Self-Error Audit | Rule Crystallization | Fatigue Reset (F -> 0.05)"]
    end

    subgraph MPL_SYS["6. Closed-Loop Metacognition (MPL) & Path B Persistence"]
        MPL1["MPL Level 1: compute_state_vector() (6D)<br/>v_VAD | r_spec | r_refl | s_belief | g_starv | f_env"]
        MPL2["MPL Level 2: _run_llm_interpretation()<br/>4-Point Anomaly Diagnosis -> :MetaObservation"]
        MPL3["MPL Level 3: _apply_regulations() (TTL Hysteresis)<br/>CAUTIOUS_TONE | DEPRECATION_BRAKE | GOAL_PRIORITY | VAD_ATTRIBUTION_FILTER"]
        SCHED["APScheduler (120s Periodic Trigger)"]
        QDRANT[("Qdrant (:6333)<br/>ema_memories (exclude_meta)")]
        NEO4J[("Neo4j (:7687)<br/>:Entity | system:self | :MetaObservation | :UserModel")]
        TIMESCALE[("TimescaleDB (:5432)<br/>meta_telemetry Hypertable")]
        SQLITE[("SQLite (ema_memory.db)<br/>Local Mirror & Fallback")]
    end

    CLI <--> CLASS
    DOC -->|Chunks| ENC
    WEB <--> SQLITE
    WEB <--> MPL1
    EXEC --> EXP

    CLASS --> TOM --> ATT --> ENC
    ENC --> D1 & D2 & D3 & D4 & D5

    D1 <--> QDRANT
    D2 <--> NEO4J
    D3 & D4 & D5 <--> SQLITE
    TOM --> NEO4J

    ATT --> PRIO1
    PRIO1 -->|No doc match| PRIO2
    PRIO1 -->|Hit| OBS
    PRIO2 --> OBS
    CLASS -->|RESOLUTION| CONF_RES
    CONF_RES <--> D3

    EXEC <--> HOMEO
    HOMEO <--> SCRATCH
    SCRATCH --> WIKI_BG
    WIKI_BG <--> D2
    HOMEO -->|F >= 0.95| SLEEP
    SLEEP <--> D2 & D3

    SCHED --> MPL1
    EXEC --> MPL1
    SCRATCH --> MPL1
    MPL1 --> TIMESCALE
    MPL1 -->|Anomaly| MPL2 --> NEO4J
    MPL1 --> MPL3
    MPL2 --> MPL3
    MPL3 -->|Modulates| EXEC
    MPL3 -->|Modulates| SCRATCH
    MPL3 -->|Guards| CONF_RES
```

*(Full architectural diagrams, neurobiological comparisons, sequence flows, and mathematical formulations are detailed in [`WHITEPAPER.md`](./WHITEPAPER.md) and [`WHITEPAPER_PL.md`](./WHITEPAPER_PL.md).)*

---

## Roadmap

- [x] **Milestone 1 — Theoretical Foundation & Architecture Specification (`v3.4`):** Formalization of the 5D hippocampal memory model, homeostatic drives, 6D Metacognitive Processing Loop (MPL), Theory of Mind, silent Inner Scratchpad, and hybrid Path A/Path B persistence design.
- **Milestone 2 — Core Cognitive Engine & Standalone Mode (Path A):** Release of the core Python runtime with local SQLite persistence, Ollama bridge, autonomous thought loop, and Sleep Phase consolidation.
- **Milestone 3 — Distributed Production Stack (Path B):** Release of Docker Compose orchestration (`TimescaleDB + Qdrant + Neo4j`), `APScheduler` integration, and Cypher multi-hop graph querying.
- **Milestone 4 — Observability Dashboard & Multi-Format Ingestion:** Release of the interactive Web UI (`Top-N` graph visualization, 6D telemetry charts, CSV/GraphML export) and PDF/DOCX/URL document loaders.

---

## Citation & Research

If you reference **EMA** or the **Metacognitive Processing Loop (MPL)** architecture in your research or engineering work, please cite [`WHITEPAPER.md`](./WHITEPAPER.md).

## License

This project is licensed under the **Apache License 2.0** — see the [`LICENSE`](./LICENSE) file for details.
