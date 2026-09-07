# Java → Python for AI-First & Agentic Engineering
### A Hands-On Curriculum for Forward Deployed Engineer (FDE) Readiness

**Audience:** Experienced Java developers transitioning to Python for AI-first / agentic application development
**Format:** Each module pairs a short concept brief with a mandatory hands-on lab. No module should be "lecture only."
**Design principle:** An FDE is judged on how fast they can go from a vague client problem to a working, demoable system on unfamiliar infrastructure. Every lab should end with something that *runs*, not just compiles.

---

## Module 0 — Orientation: What "AI-First" and "FDE" Actually Demand
- Contrast the Java enterprise mindset (compile-time safety, heavy frameworks, long release cycles) with the Python AI mindset (fast iteration, duck typing, notebook-to-production gap)
- Walk through the FDE role archetype: embedded with the client, owns the full stack from data ingestion to demo, works under ambiguity, must communicate with non-engineers
- **Lab:** Take a vague one-paragraph client brief ("automate our support ticket triage") and produce a 1-page technical approach + architecture sketch in 90 minutes

## Module 1 — Python Fluency for Java Developers
- Dynamic typing vs. static typing; type hints (`typing`, `mypy`) as the bridge
- Data model differences: dataclasses/Pydantic vs. POJOs; duck typing vs. interfaces; `__init__`/dunder methods vs. constructors
- Comprehensions, generators, iterators vs. Java Streams
- Context managers (`with`) vs. try-with-resources
- Decorators vs. Java annotations — conceptual and practical differences
- Packaging & environments: `venv`/`poetry`/`uv` vs. Maven/Gradle; `pyproject.toml`
- **Lab:** Port a small Java service (e.g., an inventory REST API) to idiomatic Python, preserving type safety with Pydantic + mypy, and diff the design decisions

## Module 2 — Modern Python Application Engineering
- Async/await and the event loop vs. Java threads/executors
- FastAPI (or Flask) for services; dependency injection patterns in Python
- Testing: pytest, fixtures, mocking vs. JUnit/Mockito
- Logging, structured logging, and config management (12-factor style)
- **Lab:** Build an async FastAPI microservice with typed request/response models, pytest suite, and structured JSON logging; containerize it with Docker

## Module 3 — LLM Fundamentals
- Tokens, context windows, embeddings, temperature/sampling — the mental model, not the math
- Prompting patterns: zero/few-shot, chain-of-thought, structured output (JSON mode/tool schemas)
- Calling model APIs (Anthropic/OpenAI SDKs) — sync, streaming, async
- Cost/latency tradeoffs and model selection heuristics for client engagements
- **Lab:** Build a CLI tool that takes unstructured text (e.g., raw client emails) and returns validated structured JSON via schema-constrained prompting; add streaming output

## Module 4 — Retrieval-Augmented Generation (RAG)
- Embeddings and vector similarity; chunking strategies and their failure modes
- Vector stores: Chroma/FAISS/pgvector/Pinecone — tradeoffs for on-prem vs. cloud client environments
- Hybrid search (BM25 + vector), reranking
- Evaluation of retrieval quality (precision/recall on a golden set)
- **Lab:** Build a document Q&A system over a real corpus (e.g., internal policy PDFs); measure and improve retrieval quality with a small eval set before/after chunking changes

## Module 5 — Knowledge Graphs & Ontologies for LLM Grounding
*(This connects directly to ontology/knowledge-graph work you've done — worth treating as a differentiator module, not filler.)*
- When graph-structured retrieval beats pure vector RAG (multi-hop reasoning, entity disambiguation, compliance traceability)
- Building a lightweight domain ontology (OWL/RDF or simple property graph) and querying it (SPARQL/Cypher)
- GraphRAG patterns: LLM-assisted entity/relation extraction into a graph, then grounding generation on graph traversal
- **Lab:** Take a small structured dataset, build an ontology/graph (Neo4j or RDFLib), and wire an LLM agent to answer multi-hop questions by traversing the graph instead of flat retrieval

## Module 6 — Tool Use, Function Calling & MCP
- Function/tool calling mechanics across major model APIs
- Designing tool schemas that are unambiguous to an LLM (this is a skill, not boilerplate)
- Model Context Protocol (MCP): building an MCP server, exposing tools/resources, connecting a host
- Error handling and retries when the "caller" is a model, not a compiler
- **Lab:** Build an MCP server exposing 2–3 real tools (e.g., a database query tool, a ticketing-system tool) and connect it to a client; verify the agent chooses tools correctly under ambiguous prompts

## Module 7 — Agentic Architectures
- Single-agent loops (ReAct-style: reason → act → observe) built from scratch before using a framework
- Framework survey and when to use which: LangGraph (explicit state machines), CrewAI (role-based multi-agent), AutoGen (conversational multi-agent), or a lightweight custom orchestrator
- Planning, memory (short-term vs. persistent), and guardrails against infinite loops/runaway tool calls
- Multi-agent patterns: supervisor/worker, debate, pipeline
- **Lab A:** Build a ReAct agent from scratch (no framework) with 3 tools, to internalize the loop
- **Lab B:** Rebuild the same agent in LangGraph as an explicit state graph; compare debuggability and control

## Module 8 — Evaluation, Observability & Reliability
- Why "it worked in the demo" isn't enough for client deployments
- Tracing agent runs (LangSmith, or custom structured tracing) — this is where FDEs live during client debugging sessions
- Building eval harnesses: golden datasets, LLM-as-judge, regression testing for prompts
- Handling non-determinism: retries, fallbacks, human-in-the-loop escalation
- **Lab:** Instrument the Module 7 agent with full tracing, then build an eval harness that catches a regression when a prompt or tool is deliberately broken

## Module 9 — Deployment, Security & Enterprise Integration
- Containerization and deployment patterns for agentic services (stateless vs. stateful agents)
- Auth patterns for client environments (OAuth, API keys, service accounts) and secrets management
- Data privacy/compliance considerations when agents touch client data (PII handling, on-prem vs. cloud model calls)
- Integrating with enterprise systems: databases, ticketing systems (Jira/ServiceNow), Slack/Teams, internal APIs
- **Lab:** Deploy the agent stack behind an authenticated API, connect it to a real external system (e.g., Slack bot or Jira integration), and document the security posture as a client would require

## Module 10 — The FDE Skillset Specifically
- Rapid prototyping under ambiguity: time-boxed spikes, "good enough" architecture decisions, when to throw away a prototype
- Client-facing technical communication: translating agent behavior/limitations for non-technical stakeholders; live-debugging in front of a client
- On-site/embedded engineering constraints: working against client data you can't fully see, unfamiliar legacy systems, restrictive networks
- **Lab (Capstone):** Given a new, previously unseen client brief and a messy sample dataset, build and demo an end-to-end agentic solution (ingestion → grounding → agent → interface) in a fixed time window (e.g., 2 days), then present it as if to a client stakeholder — followed by a mock Q&A/objection-handling session

---

## Suggested Sequencing & Time Allocation
| Phase | Modules | Focus | Suggested Duration |
|---|---|---|---|
| Foundations | 0–2 | Python fluency, service engineering | 2 weeks |
| Core AI Engineering | 3–5 | LLMs, RAG, knowledge graphs | 2–3 weeks |
| Agentic Systems | 6–8 | Tools, agents, evaluation | 3 weeks |
| Production & Role Readiness | 9–10 | Deployment, FDE capstone | 2 weeks |

## Cross-Cutting Design Notes for the Course Itself
- **Every lab uses a "client-shaped" problem**, not a toy dataset — this is what actually differentiates FDE training from generic ML-engineering training
- **Introduce ambiguity deliberately**: incomplete specs, dirty data, contradictory requirements — mirror real engagements
- **Pair each framework lab with a "build it raw first" lab** (as in Module 7) so learners understand what the framework is abstracting, not just how to call it
- **Grade on demoability and debuggability**, not just correctness — an FDE's output is judged live, in front of clients
- Consider a running **capstone client persona** across the whole course (a fictional but consistent company) so later modules build on earlier artifacts instead of starting fresh each time

---

*This outline is designed to be adapted — trim Module 5 (knowledge graphs) if the audience's client base doesn't need multi-hop reasoning, or expand it if it does; that module is a strong differentiator for engagements involving compliance, complex enterprise data, or reasoning-heavy use cases.*
