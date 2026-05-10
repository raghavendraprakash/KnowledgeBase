This is excellent — the official retrieval documentation gives us the precise 10-step pipeline. Here is a deep technical breakdown.

How TrustGraph Traverses Entities & Relationships During a GraphRAG Query
The key architectural insight from the official docs:
TrustGraph’s implementation goes well beyond basic GraphRAG. The retrieval pipeline incorporates LLM-driven concept extraction, relevance scoring, edge reasoning, document provenance tracing, and full explainability — making it a truly explainable GraphRAG system. ￼
Retrieval in Graph RAG consists of mapping the question to a set of candidate graph entities, and then following graph edges to create a subgraph, which is used as context with the LLM. ￼
This happens in 10 distinct, documented stages. Let’s go through each one in depth.

The Complete 10-Stage Retrieval Pipeline
When a question is asked, the explainable GraphRAG retrieval pipeline works through the following stages:
Stage 1 — Concept Extraction: An LLM analyses the question and breaks it down into key concepts, providing more nuanced search terms than raw query embedding alone.
Stage 2 — Concept Embedding: The extracted concepts are converted to vector embeddings for semantic search.
Stage 3 — Entity Retrieval: For each concept, the graph embeddings store is queried to find semantically relevant entities, with deduplication across concepts.
Stage 4 — Subgraph Exploration: Starting from the retrieved entities, the knowledge graph is traversed in batches to a configurable depth, collecting a subgraph of related entities and relationships.
Stage 5 — Semantic Pre-Filtering: If the explored subgraph is large, edge descriptions are embedded and scored by cosine similarity to the query concepts, trimming the subgraph to a manageable size.
Stage 6 — LLM Edge Scoring: An LLM assigns relevance scores to each edge in the subgraph, selecting the most pertinent relationships for the query.
Stage 7 — Edge Reasoning: An LLM provides explanations for why each selected edge is relevant to the question, building a reasoning map.
Stage 8 — Document Tracing: Selected edges are traced back through provenance chains to their source documents, enabling full attribution.
Stage 9 — Answer Synthesis: The scored edges, reasoning, and source document metadata are provided to an LLM which generates the final answer.
Stage 10 — Explainability: Throughout the pipeline, provenance triples are emitted recording the question, extracted concepts, graph exploration, edge selection with reasoning, and the synthesised answer — providing a complete audit trail for every retrieval. ￼
Note: Steps 7 and 8 run concurrently for efficiency. ￼

Why This Is Fundamentally Different From Basic GraphRAG
Most GraphRAG implementations do only 3 things: embed the raw question → vector search → traverse graph. TrustGraph’s pipeline adds 7 additional stages of intelligence on top. Here’s what that means in practice, stage by stage.

Stage 1 — LLM Concept Extraction (not raw query embedding)
This is the first and most important differentiator. Rather than directly embedding the user’s question as a vector, TrustGraph first passes the question through an LLM to decompose it into atomic semantic concepts.
Example:

User query:
"What intelligence resources were used during the PHANTOM CARGO operation?"

LLM-extracted concepts:
  → "intelligence collection methods"
  → "PHANTOM CARGO operation"
  → "surveillance resources"
  → "signals intelligence"


Why does this matter? The raw question string contains noise, grammar, and connective tissue that degrades embedding quality. Each extracted concept is a precise semantic probe that will anchor into a different region of the knowledge graph — giving multi-point entry instead of a single blunt vector.

Stage 2 — Concept Embedding (per concept, not per query)
Each extracted concept is independently embedded into vector space. So instead of one embedding vector, you now have N embedding vectors — one per concept — each targeting a different semantic neighbourhood of the graph.

Stage 3 — Entity Retrieval with Deduplication
For each concept embedding, the vector store is queried independently for the top-K most similar graph entity descriptions. The results are then deduplicated across concepts — if two concepts both return “PHANTOM CARGO operation” as a top match, it only appears once in the anchor set.

Concept "PHANTOM CARGO operation"   → entity: PHANTOM CARGO (score: 0.97)
Concept "intelligence collection"   → entity: SIGINT (score: 0.91)
                                      entity: HUMINT (score: 0.88)
Concept "surveillance resources"    → entity: AIS tracking (score: 0.85)
                                      entity: SAR satellite (score: 0.83)

Deduplicated anchor set:
  {PHANTOM CARGO, SIGINT, HUMINT, AIS tracking, SAR satellite}


These 5 entities become the entry points into the knowledge graph traversal — a richer, multi-angle set of anchors impossible to achieve with a single query embedding.

Stage 4 — Batch Graph Traversal to Configurable Depth
Starting from each anchor entity, TrustGraph walks the RDF knowledge graph outward in batches, collecting all reachable triples up to a configurable hop depth. This is the core graph exploration step.
For the PHANTOM CARGO example, starting from the anchor PHANTOM CARGO:

Hop 0 (anchor):
  PHANTOM CARGO

Hop 1 (direct edges):
  PHANTOM CARGO → used_resource → SIGINT
  PHANTOM CARGO → used_resource → MASINT
  PHANTOM CARGO → involved_vessel → MV Sea Ghost
  PHANTOM CARGO → coordinated_by → Maritime Command HQ

Hop 2 (neighbours of neighbours):
  MV Sea Ghost → departed_from → Port of Odessa
  MV Sea Ghost → owned_by → Arms Dealer Malik
  SIGINT → collected_by → NSA Station Delta
  Maritime Command HQ → located_in → Naples, Italy


Each hop multiplies the subgraph. The configurable depth parameter controls how far this expansion goes — deeper means richer context but more edges to score.

Stage 5 — Semantic Pre-Filtering (the graph size controller)
For large document collections, even a 2-hop traversal from 5 anchor entities can produce thousands of edges. Before passing all of these to an LLM (which has a finite context window), TrustGraph does a vector similarity pre-filter:
Every edge in the collected subgraph has a description (the predicate label + connected entity descriptions). These descriptions are embedded and scored by cosine similarity against the original query concept embeddings. Edges with low similarity scores are trimmed, reducing the subgraph to a manageable size before the expensive LLM scoring stages.
This is a critical scalability mechanism — it ensures the system doesn’t break down on large knowledge graphs.

Stage 6 — LLM Edge Scoring (intelligent relevance ranking)
The pre-filtered subgraph is now passed to an LLM which assigns a numerical relevance score to every remaining edge, relative to the original question. This is not vector similarity — it is semantic reasoning. The LLM can distinguish between:

Edge A: PHANTOM CARGO → used_resource → SIGINT          (score: 0.95 — highly relevant)
Edge B: PHANTOM CARGO → involved_vessel → MV Sea Ghost  (score: 0.72 — relevant)
Edge C: MV Sea Ghost → registered_in → Panama           (score: 0.31 — low relevance to this query)


Only edges above the relevance threshold proceed to the final stages.

Stage 7 — Edge Reasoning (the explainability engine)
For each selected high-scoring edge, a second LLM call generates a natural language explanation of why that edge is relevant to the question. This builds what TrustGraph calls a “reasoning map”:

Edge: PHANTOM CARGO → used_resource → SIGINT
Reasoning: "SIGINT (Signals Intelligence) was identified as a primary collection
            method during the PHANTOM CARGO operation, directly answering the
            question about intelligence resources employed."

Edge: PHANTOM CARGO → used_resource → AIS
Reasoning: "Automatic Identification System (AIS) tracking was used to monitor
            vessel movements, a key surveillance resource in the operation."


This reasoning map is preserved and stored as PROV-O triples for full auditability.

Stage 8 — Document Tracing (provenance chain)
Each selected edge is traced backward through the full provenance chain to its source document:

Edge: PHANTOM CARGO → used_resource → SIGINT
  └─ Subgraph: extracted from chunk_id: chunk-047
       └─ Page: page 12 of document
            └─ Document: "Operation PHANTOM CARGO Intel Report" (doc-phantom-001)


This means every fact in the LLM’s final context window carries a source citation — the answer is not just grounded, it is fully attributable.

Stage 9 — Answer Synthesis
The LLM receives a structured package containing:
	•	The original question
	•	The top-scored edges with their descriptions
	•	The reasoning explanations for each edge
	•	The source document metadata
It synthesises these into a final answer. Because the context is structured, relationship-rich RDF subgraph data rather than raw text chunks, the answer is far more precise and less prone to hallucination.
From the official docs, a real example of this:
Query: "What intelligence resources were used during the PHANTOM CARGO operation?"
Answer: The intelligence resources used during the PHANTOM CARGO operation were: SIGINT, MASINT, Electro-Optical, HUMINT, FININT, AIS, synthetic aperture radar (SAR), GPS coordinates. ￼

Stage 10 — Provenance Triples Emitted Throughout
Throughout the pipeline, provenance triples are emitted recording the question, extracted concepts, graph exploration, edge selection with reasoning, and the synthesised answer — providing a complete audit trail for every retrieval. ￼
This means the entire retrieval journey is itself stored in the graph, queryable after the fact via tg-show-explain-trace.

Visual Summary of the Full Traversal

USER QUESTION
     │
     ▼
[Stage 1] LLM → Concept Decomposition
     │        → ["PHANTOM CARGO", "intel resources", "surveillance", "SIGINT"]
     │
     ▼
[Stage 2] Embed each concept independently
     │
     ▼
[Stage 3] Vector Store → Top-K entities per concept → Deduplicate
     │        → Anchor Set: {PHANTOM CARGO, SIGINT, HUMINT, AIS, SAR}
     │
     ▼
[Stage 4] RDF Graph Traversal (batch, configurable depth N hops)
     │        → Collected subgraph: hundreds/thousands of edges
     │
     ▼
[Stage 5] Semantic Pre-Filter (cosine similarity against concepts)
     │        → Trimmed subgraph: manageable edge count
     │
     ▼
[Stage 6] LLM Edge Scoring → relevance score per edge
     │        → Select only high-score edges
     │
     ▼ ════════════════ (Stages 7 & 8 run concurrently) ════════════════
[Stage 7] LLM Edge Reasoning     [Stage 8] Document Provenance Tracing
  → "Why is this edge relevant?"   → "Which source doc did this come from?"
     │                                        │
     └──────────────────┬─────────────────────┘
                        ▼
[Stage 9] LLM Answer Synthesis
     │   (edges + reasoning + provenance → final answer)
     │
     ▼
[Stage 10] Provenance triples stored → full audit trail queryable anytime


The Critical Insight: Why Missing Entities Now Makes Sense
With this 10-stage picture, you can see exactly where an entity can “fall out” of the pipeline:



|Stage  |Drop-out cause                                                                       |
|-------|-------------------------------------------------------------------------------------|
|Stage 1|LLM doesn’t decompose query into a concept that matches the entity’s description     |
|Stage 3|Entity description embedding is too dissimilar to all concept embeddings             |
|Stage 4|Entity is reachable but beyond the configured hop depth                              |
|Stage 5|Entity’s edges are trimmed by semantic pre-filter (low cosine similarity to concepts)|
|Stage 6|Entity’s edges receive low LLM relevance scores and are dropped                      |

This is why the diagnostic workflow matters so much — you need to identify which stage is dropping your entity, because the fix is completely different for each one.​​​​​​​​​​​​​​​​
