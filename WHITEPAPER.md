# EMA v3.4: Neuro-Symbolic Cognitive Architecture with Homeostatic Regulation, Closed-Loop Metacognition (MPL), and Theory of Mind

**Authors:** EMA Architecture Team  
**Version:** 3.4 (Architectural, Engineering & Neurobiological Specification Release)  
**Date:** November 2026  
**License:** Apache License 2.0  

---

## Abstract

Contemporary Large Language Models (LLMs) achieve remarkable results in statistical token prediction and surface-level textual reflection, yet from a cognitive systems engineering perspective they remain stateless, reactive, and devoid of persistent identity. Whenever a standard LLM claims uncertainty, emotional tension, or self-reflection, that behavior is merely an ephemeral linguistic artifact generated within a single context window rather than a grounded computational state.

This whitepaper defines **EMA (Episodic Memory Agent) v3.4**—a hybrid neuro-symbolic cognitive architecture in which **executive cognitive control** is strictly decoupled from **natural-language generation**. Within EMA, a local LLM operates solely as a semantic parser and speech-realization organ, while agency, autobiographical continuity, and self-regulation are governed by deterministic computational engines: a **5-Dimensional Episodic Memory modeled after the hippocampal complex**, a **Multi-Hop Knowledge Graph (Neo4j)**, a **Dense Vector Store (Qdrant)**, a **Homeostatic Drive Engine**, an **Autonomous Thought Loop with a silent Inner Scratchpad**, a **Theory of Mind module (`TheoryOfMindEngine`)**, and a **Three-Level Metacognitive Processing Loop (MPL)** backed by **TimescaleDB**.

---

## 1. System Architecture and Production Hybrid Stack

EMA v3.4 is engineered around a dual-mode persistence layer with seamless *graceful degradation*:
* **Path B (Distributed Production Stack):** Integrates **Qdrant** (128D cosine vector indexing with metadata filtering), **Neo4j 5** (property graph storing `:Entity`, `system:self`, `:MetaObservation`, and `:UserModel` nodes), **TimescaleDB** (time-series hypertables for the 6D MPL vector with `time_bucket` aggregations), and **APScheduler** (concurrent background execution).
* **Path A (Standalone Local Mode):** Zero-dependency fallback utilizing **SQLite** and lightweight Python daemon threads, preserving full cognitive operation when distributed container services are offline.

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

---

## 2. Neurobiological Analogy: Five Memory Drawers vs. The Hippocampal Complex

The `MemoryItem` data structure in EMA is not an arbitrary software partitioning; it is a functional engineering implementation of the human **hippocampal–entorhinal circuit (EC–DG–CA3–CA1)** responsible for memory consolidation, *pattern separation*, and *pattern completion*.

```mermaid
flowchart LR
    INPUT["Sensory / Textual Input<br/>(Dialogue / PDF / Web)"] --> EC["Entorhinal Cortex (Attention Gateway)<br/>AttentionGateway & OllamaEncoders"]
    
    subgraph HIPPO["Hippocampal Complex — 5 Memory Drawers of EMA"]
        D1["1. Semantic Drawer<br/>(Qdrant 128D Cosine)<br/>~ Temporal Cortex / Dentate Gyrus (DG)"]
        D2["2. Graph Drawer<br/>(Neo4j :Entity & system:self)<br/>~ CA3 Field (Autoassociative Network)"]
        D3["3. Episodic Drawer<br/>(SQLite + Timestamp + superseded_by)<br/>~ CA1 Field (Temporal Sequencing)"]
        D4["4. Affective Drawer<br/>(VAD Vector + Discomfort)<br/>~ Amygdala & Ventral Hippocampus"]
        D5["5. Functional Drawer<br/>(Utility & Goal Priority)<br/>~ Prefrontal Cortex (vmPFC)"]
    end

    EC --> D1 & D2 & D3 & D4 & D5
    D1 & D2 & D3 & D4 & D5 --> SLEEP["Sleep Phase (sleep_phase)<br/>Cortico-Hippocampal Consolidation & Self Audit"]
```

### Comparative Matrix: EMA Memory Drawers vs. Hippocampal Neurobiology

| EMA Drawer | Engineering Implementation | Neurobiological Counterpart | Functional Role in Cognition |
| :--- | :--- | :--- | :--- |
| **1. Semantic** (`semantic_vector`) | Dense 128D embeddings in **Qdrant** with cosine similarity and `memory_type` filtering. | **Dentate Gyrus (DG) & Temporal Neocortex** | **Pattern Separation:** Projects overlapping stimuli into an orthogonal vector space, enabling semantic retrieval even when exact lexical phrasing differs. |
| **2. Graph** (`GraphSlice` / `GraphTriple`) | `(Subject)–[Predicate]–>(Object)` triples in **Neo4j** with `confidence` weights and `system:self` node. | **Hippocampal CA3 Field (Recurrent Autoassociative Network)** | **Pattern Completion:** Multi-hop reconstruction of an entire causal or conceptual chain from a single cue entity (`find_graph_paths`). |
| **3. Episodic** (`MemoryMeta`) | ISO timestamp, `provenance` tag, `is_active` flag, `superseded_by` pointer, and `decay_factor`. | **Hippocampal CA1 Field (Time Cells)** | **Spatiotemporal Grounding & Continuity of "Self":** Preserves chronological order and non-destructive belief revision (the agent remembers *what* it previously believed and *why* it updated its stance). |
| **4. Affective** (`AffectiveSlice`) | Valence–Arousal–Dominance (VAD) coordinates coupled with cognitive discomfort in `HomeostasisEngine`. | **Amygdala & Ventral Hippocampus** | **Salience Tagging:** Amplifies memory traces associated with error or contradiction, shortening the idle silence threshold to prioritize conflict resolution. |
| **5. Functional** (`FunctionalSlice`) | Operational utility scores, alignment with user goals, and `goal_starvation` tracking. | **Ventromedial Prefrontal Cortex (vmPFC)** | **Value-Based Gating:** Selects memories relevant to active operational tasks while suppressing unproductive background speculation. |

---

## 3. Engineering Specification of Core Functions and Algorithms

### 3.1 Cognitive Orchestration & Epistemic Hierarchy (`NaturalLearningOrchestrator`)
* **`classify_utterance(text: str) -> dict`:** Deterministic-semantic input classifier. Identifies conversational intent (`FACT`, `RESOLUTION`, `QUERY`), detects user pushback (`is_correction`), and extracts target entities requiring state updates (`resolved_entity`).
* **`resolve_entity_conflict(entity_hint, new_memory_id, is_explicit_resolution) -> int`:** Non-destructively archives superseded beliefs (`is_active = False`, `superseded_by = new_memory_id`, reducing `decay_factor` by 90%) simultaneously across **SQLite**, **Qdrant**, and **Neo4j**. Protected by the metacognitive **`DEPRECATION_BRAKE`**.
* **`process_query(query: str) -> OrchestratorResponse`:** Enforces a strict three-stage epistemic retrieval pipeline:
  1. **Local Document Priority (`_find_document_evidence` + `_get_neo4j_graph_context`):** Queries vectors originating from ingested documents (`provenance: "doc:*"`) in Qdrant (`score >= 0.30`) alongside 1–3 hop relationship paths in Neo4j (`ANSWERED_FROM_LOCAL_DOCUMENTS`).
  2. **Epistemic Curiosity (`WebKnowledgeTool.execute`):** Queries **Open-Meteo** or **Wikipedia** when local documents lack coverage (`RESOLVED_VIA_WEB_SEARCH`).
  3. **Observer-Supervised Cautious Synthesis:** Fuses top Qdrant vectors (excluding `memory_type="meta"`) with Neo4j graph paths (`SYNTHESIZED_WITH_NEO4J_AND_QDRANT`).

``````mermaid
sequenceDiagram
    actor User
    participant Orch as NaturalLearningOrchestrator
    participant ToM as TheoryOfMindEngine
    participant Qdrant as Qdrant (128D)
    participant Neo4j as Neo4j (Graph)
    participant Web as WebKnowledgeTool
    participant MPL as MPL Controller

    User->>Orch: Submit Query (QUERY)
    Orch->>ToM: observe_turn() & get_prompt_context()
    Orch->>Qdrant: 1. Search local documents (provenance="doc:*")
    Orch->>Neo4j: 1. Traverse 1..3 hop paths (find_graph_paths)
    alt Strong Local Document Match (score >= 0.30)
        Qdrant-->>Orch: Publication Chunks
        Neo4j-->>Orch: Entity Relationship Paths
        Orch-->>User: Grounded Response [ANSWERED_FROM_LOCAL_DOCUMENTS]
    else No Local Document Match
        Orch->>Web: 2. Check Open-Meteo / Wikipedia
        alt External Data Retrieved
            Web-->>Orch: Verified Text
            Orch->>Qdrant: Persist New Knowledge (web_search)
            Orch-->>User: Synthesized Response [RESOLVED_VIA_WEB_SEARCH]
        else Unavailable / Vetoed by Observer
            Orch->>Qdrant: 3. General RAG (exclude_meta=True)
            Orch->>Neo4j: 3. Multi-Hop Graph Context
            Orch-->>User: Cautious Synthesis [SYNTHESIZED_WITH_NEO4J_AND_QDRANT]
        end
    end
    Orch->>MPL: run_cycle(trigger="turn_query")
```
```

### 3.2 Three-Level Metacognitive Processing Loop (`MetacognitiveProcessingLoop`)

```mermaid
flowchart LR
    subgraph L1["Level 1: Deterministic Monitoring (6D)"]
        VEC["compute_state_vector()<br/>Compute vector m_t in [0,1]^6<br/>(Filter: exclude memory_type='meta')"]
        TS[("TimescaleDB<br/>meta_telemetry")]
        VEC --> TS
    end

    subgraph L2["Level 2: Anomaly Interpretation (LLM)"]
        ANOM{"_detect_anomaly()<br/>Thresholds Exceeded?"}
        DIAG["_run_llm_interpretation()<br/>Structured 4-Point JSON Diagnosis"]
        N_OBS[("Neo4j<br/>:MetaObservation")]
        ANOM -->|Yes| DIAG --> N_OBS
    end

    subgraph L3["Level 3: Hysteresis Regulation (TTL)"]
        REG["_apply_regulations()<br/>• CAUTIOUS_TONE (TTL 3)<br/>• DEPRECATION_BRAKE (TTL 4)<br/>• GOAL_PRIORITY (TTL 3)<br/>• VAD_ATTRIBUTION_FILTER (TTL 2)"]
    end

    TS --> ANOM
    ANOM -->|No| REG
    N_OBS --> REG
    REG -->|Modulates behavior in subsequent turns| VEC
```

* **Level 1 (`compute_state_vector`):** Computes $\mathbf{m}_t = [v_{\text{VAD}}, r_{\text{spec}}, r_{\text{refl}}, s_{\text{belief}}, g_{\text{starv}}, f_{\text{env}}]^T \in [0,1]^6$ strictly from deterministic telemetry.
* **Level 2 (`_run_llm_interpretation`):** Generates a 4-point structured self-diagnosis persisted as a `:MetaObservation` node in Neo4j upon threshold breaches.
* **Level 3 (`_apply_regulations`):** Applies turn-bounded TTL hysteresis controls (`CAUTIOUS_TONE`, `DEPRECATION_BRAKE`, `GOAL_PRIORITY`, `VAD_ATTRIBUTION_FILTER`).

### 3.3 Theory of Mind (`TheoryOfMindEngine`) & Silent Inner Scratchpad
* **`observe_turn(client, user_text, intent, is_correction)`:** Maintains a persistent **`(:UserModel)`** node in **Neo4j** tracking `expertise_level`, `preferred_style`, `known_topics`, `knowledge_gaps`, and `active_goals`.
* **`_filter_via_inner_scratchpad(draft_thought, category)`:** Evaluates idle thoughts in a silent scratchpad (`CICHA MYŚL (SCRATCHPAD)`) before speaking aloud, while `_is_meaningful_memory` isolates ingested documents (`doc:*`) from self-identity contradiction checks.

---

## 4. Summary of Emergent Cognitive Properties

| Cognitive System Criterion | Implementation in EMA v3.4 | Verifiable Engineering Artifact |
| :--- | :--- | :--- |
| **Self & Other Modeling** | Strict separation between **`system:self`** and **`:UserModel`** (interlocutor knowledge & goals). | Neo4j Cypher queries & `/tom` command. |
| **Recursive Self-Modeling** | Processing information about internal processing (6D state vector $\to$ `:MetaObservation` $\to$ TTL regulation). | TimescaleDB hypertable & `/eksport_mpl` CSV export. |
| **Information Integration & Memory** | Simultaneous 5D synchronization across Qdrant, Neo4j, TimescaleDB, and SQLite with `superseded_by` chains. | `ProductionHybridStorage` adapter layer. |
| **Behavioral Flexibility & Control** | Hypothesis vetoing via `Observer`, `DEPRECATION_BRAKE`, `CAUTIOUS_TONE`, and silent `Inner Scratchpad`. | Observer flags & Web Dashboard v2.4 logs. |
