# Truman — Real-Time Enterprise Intelligence Agent

Truman is an **AI decision infrastructure** that connects fragmented enterprise data, reasons across domains, **predicts likely outcomes**, and recommends actions **before damage happens**. Instead of producing more dashboards, Truman turns messy enterprise data into a **living knowledge network** (events → nodes → links → predictions → actions) with transparent evidence and confidence. 

---

## Problem

Enterprise teams are **data-rich but insight-poor**:

* Data is split across departments and systems (CRM, ERP, finance, ops, etc.).
* Signals are disconnected, making real-time correlation hard.
* Humans spend days triangulating stale reports.
* Decisions can become politics-driven rather than evidence-driven. 

---

## Solution

**Truman** unifies enterprise signals into an event-based graph and runs a **multi-agent reasoning workflow** to produce explainable insight + prediction packages.

High-level concept:

1. **Collect** new/changed data from sources
2. **Normalize** into structured Events
3. **Reason** across events using multi-agent stages (Propose → Critique → Resolve)
4. **Link** events/nodes into a knowledge network with confidence scoring
5. **Predict** risks/opportunities when triggers + thresholds are met
6. **Plan** recommended actions (optionally automatable) 

---

## What Truman Produces

Truman standardises everything into nodes you can store, query, and visualise:

* **Event** (atomic facts extracted from raw data)
* **Information Node** (structured insight derived from events)
* **Prediction Node** (forecast + probability/confidence + linked evidence)
* **Plan** (recommended actions; “proceed/cancel”) 

---

## Key Features

* **Cross-domain correlation:** links signals across departments using an explainable graph. 
* **Event-first ingestion:** converts emails/reports/docs/PDFs into normalised Events. 
* **Multi-agent reasoning:** reduces single-LLM hallucination via propose/critique/resolve workflow. 
* **Predictive outcomes:** generates scenario-based predictions and recommended actions. 
* **Transparent confidence:** linking and predictions carry confidence + explanations + timestamps. 
* **Cost-aware processing:** crawl/LLM runs **on demand** (idle when there’s no new data). 

---

## System Architecture

### Components

* **MCP Server / Data Sources**
  Fetches data only when new incoming data appears; otherwise idles to reduce running cost. 

* **AI Crawl + Raw Layer**
  Receives heterogeneous raw inputs (emails, reports, PDFs, docs). 

* **Event Builder**
  Summarizes/structures raw inputs into **Events** (one file → multiple events possible), then stores them. 

* **Vector & Event Store (Supabase)**
  Stores Events, Entities, Links, and Nodes for retrieval + graph traversal. 

* **Multi-Agent Reasoning Layer**
  Generates Information/Prediction Nodes via staged reasoning. 

* **Planning Agent**
  Turns a Prediction Node into an executable plan (docs/emails/proposals), with user approval. 

### Architecture Flow (text diagram)

```text
Sources (CRM/ERP/Emails/Docs/PDFs)
          │
          ▼
     MCP / AI Crawl  ── (idle when no new data)
          │
          ▼
       Raw Data
          │
          ▼
   Event Builder (summarize → extract → normalize)
          │
          ▼
 Supabase (Event + Vector Store)
          │
          ▼
 Multi-Agent Reasoning (Propose → Critique → Resolve)
          │
          ▼
 Information Nodes + Prediction Nodes (linked graph)
          │
          ▼
     Planning Agent (one-click approve/cancel)
```
<img width="1183" height="633" alt="image" src="https://github.com/user-attachments/assets/0d93da34-bb5b-4b25-9841-abb585d55a99" />

---

## User Flow

1. **Ingest**

   * New/changed data arrives in enterprise systems.
   * MCP crawl fetches only the new content; stays idle otherwise. 

2. **Normalize**

   * Raw data is summarized and converted into **Events**.
   * Events are stored in Supabase. 

3. **Reason**

   * Events are sent into the Multi-Agent Reasoning Layer.
   * Output becomes new **Information** and/or **Prediction** nodes. 

4. **Act**

   * Planning agent proposes actions and generates artifacts (emails/docs/proposals).
   * User clicks:

     * **Proceed** → execute automations
     * **Cancel** → discard prediction node 

---

## Data Model

Truman stores information in layers:

* **Raw layer:** store data exactly as received.
* **Processed layer:** normalize into:

  * **Event** (what happened)
  * **Entity** (who/what it’s about)
  * **Link** (how it connects)
  * **Information/Prediction nodes** (reasoned outputs) 

Each node should include metadata for auditability:

* timestamp
* why it exists (trigger)
* evidence summary
* linked nodes
* confidence/probability
* recommended actions 

---

## Multi-Agent Reasoning

To reduce hallucination and improve robustness, Truman reasons in three stages:

1. **Propose**
   4 AIs generate independent diagnosis, expected outcomes, and recommendations.

2. **Critique**
   The 4 AIs challenge each other on missing data, weak logic, weak evidence, and unstable assumptions.

3. **Resolve**
   Truman summarizes the strongest combined reasoning into a final structured “insight package”. 

---

## Linking & Confidence

Truman links nodes based on **entities + relationship rules** and only auto-links when confidence is high enough.

### Entity matching

* Each event carries 1–N normalized entities/tags (company, region, service, product, metric, etc.).
* Links are created when shared entities exceed a threshold (entity-first for explainability; embeddings can be secondary later). 

### Confidence scoring (example approach)

* Add points for supporting evidence (exact ID matches and trusted sources add more; fuzzy matches add less)
* Apply penalties for conflicts/inconsistencies
* Auto-link only if confidence exceeds threshold (example: 0.60–0.85 depending on rule strictness) 

**Link metadata must include:**

* why the link was created
* timestamp
* supporting evidence
* insight/context 

---

## Prediction Nodes

Truman generates a **Prediction Node** only when:

1. New events match a predefined “meaningful trigger” (e.g., breaking change, critical access change, inventory below threshold).
2. Signal is abnormal or historically linked to real outcomes (trend check).
3. Expected impact is high enough AND evidence confidence passes a threshold. 

**Prediction Node metadata includes:**

* why this prediction was generated
* linked Information nodes
* recommendation
* probability / confidence score 

### Probability (conceptual)

Truman can:

* translate a question into an “impact statement”
* retrieve relevant nodes via entities + graph traversal (favor recent, high-confidence links)
* generate 3–4 plausible outcomes
* score evidence and normalize into probabilities
* recommend low-regret + conditional actions and “signals to watch” 

---

## Planning Agent

* Links only to **Prediction Nodes**
* Produces:

  * consequences insight
  * recommended solution steps
  * auto-generated artifacts (docs/emails/proposals)
* User chooses:

  * **Proceed** → execute actions
  * **Cancel** → delete the prediction node 

---

## Tech Stack

Current stack (prototype):

* **Database / Store:** Supabase (events + vectors + node metadata) 
* **Local hosting:**

  * MCP server
  * Local LLM: Nanbeige4.1-3B (HuggingFace) 
* **Reasoning runtime:** multi-agent pipeline (Propose/Critique/Resolve) 

---

## Business Value

* **Massive time saved:** replaces manual weekly reporting and status chasing.
* **Better decisions:** correlates signals across teams with evidence and confidence.
* **Early warning:** predicts risk scenarios early enough to prevent damage. 

---

## Scenarios

### Example: “Data rich, insight poor”

A finance/ops leader needs to pull from multiple systems and triangulate manually. Truman unifies signals into events/nodes and provides connected, explainable insights. 

### Example: External + internal signal fusion (Top Glove)

Without external context, the company may look like it’s heading toward bankruptcy. With outside signals (e.g., emerging pandemic) + internal capability (mask production capacity), Truman can infer a demand shock and recommend early capacity/inventory shifts. 

### Example: Human question → predicted outcomes

Input: “Is it better to buy a Microsoft license for 10 years, or buy 1-year licenses for multiple stuff?”
Truman converts it into an impact statement (cost, flexibility, compliance, vendor risk), retrieves related nodes, simulates outcomes, and recommends a decision with confidence.

---


## Limitations

* Prototype linking thresholds and confidence formulas are rule-driven; production would need monitoring, evaluation, and tuning.
* Automations (email/docs execution) should ship with permissioning, audit logs, and safe rollback.
* Prediction quality depends on coverage + quality of connected signals across domains.

---
