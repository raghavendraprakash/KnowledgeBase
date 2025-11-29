The 2-hour "Fundamentals of Agentic AI" course outline is condensed into 10 short videos (8-12 minutes each), focusing on Python-based hands-on demos using Langflow's open-source visual builder. Videos emphasize drag-and-drop flows starting from the Simple Agent template, covering tool attachment (e.g., Calculator, URL, News Search), Agent component setup, and basic multi-agent patterns via nested Agents as tools.[1][2][3]

## Video 1: Course Intro (8 min)
Introduces agentic AI as LLMs plus tools and orchestration for autonomous tasks; outlines learning path for developers/students to build/deploy Langflow agents.[2][1]
Targets: quick setup of Langflow (pip install, langflow run) and API keys for open models like Grok or local Ollama.[3][1]

## Video 2: Generative AI to Agents (10 min)
Recaps LLM limits (no actions/memory); defines agents via Langflow's Agent component with reasoning loops, tools, and built-in chat memory.[4][1]
Demo: Blank flow → Add Chat Input/Output + Agent → Test basic chat.[1]

## Video 3: Langflow Basics (10 min)
Covers drag-drop interface, components (e.g., LLM providers), Tool Mode for any component, and connecting to Agent's Tools port.[2][1]
Demo: Simple Agent template → Configure OpenAI/Groq model, add API key, playground test.[3][2]

## Video 4: Tool Use Pattern (12 min)
Agent invokes tools (functions) for external actions; Langflow wraps components like Calculator/URL as tools with descriptions for LLM selection.[4][1]
Demo: Attach Calculator + URL tools → Query "Convert 200 USD to INR" (fetches rate, calculates).[2]

## Video 5: Planning Pattern (10 min)
Decomposes goals into steps; use sequential/parallel chains or simple graphs in Langflow flows.[1][3]
Demo: Multi-step flow (Plan → Tool1 → Tool2) for "Research latest AI news and summarize key points" using News Search + URL.[1]

## Video 6: Reflection Pattern (10 min)
Agent critiques/iterates outputs via self-loops; configure via Agent instructions + secondary LLM critique tool.[1]
Demo: Build reflection flow → Generate code snippet → Critique → Revise; test with math problem.[3][1]

## Video 7: Multi-Agent Systems (12 min)
Orchestrator Agent delegates to specialist sub-Agents (as tools); handles collaboration via nested flows.[5][1]
Demo: Manager Agent + Order Lookup sub-Agent + Product Agent → Query "Check order status #123 and suggest alternatives".[5]

## Video 8: RAG + Memory in Agents (10 min)
Integrates vector search as tool; Agent uses built-in rolling chat memory (session_id) for context.[3][1]
Demo: Add RAG tool (Vector Store + Embeddings) → Persistent memory test across queries.[1]

## Video 9: Safety + Deployment (10 min)
Guardrails (instructions, parsing errors), observability (verbose logs); export as API/JSON, deploy locally.[2][1]
Demo: Add rate limits/custom instructions → Generate API key → Python client call to flow.[2]

## Video 10: Mini-Project (12 min)
Build/deploy "Research Assistant" Agent: planning + tools (News/URL/RAG) + reflection + API export.[3][2][1]
Includes testing checklist, common pitfalls (tool errors, loops), and next steps (custom Python components).[1]

Sources
[1] Use Langflow agents https://docs.langflow.org/agents
[2] How to Create Your First AI Agent | Langflow https://www.langflow.org/blog/create-your-first-ai-agent
[3] Langflow: A Guide With Demo Project - DataCamp https://www.datacamp.com/tutorial/langflow
[4] Agents https://docs.langflow.org/components-agents
[5] ADVANCED Python AI Agent Tutorial - Using RAG, Langflow & Multi ... https://www.youtube.com/watch?v=QmUsG_3wHPg
[6] How to Build a Simple AI Agent with Langflow and Composio https://www.langflow.org/blog/build-simple-ai-agent-with-langflow-composio
[7] How to Build a Deep-Research Multi‑Agent System https://www.langflow.org/blog/how-to-build-a-deep-research-multi-agent-system
[8] Build your own AI coding agent with Langflow https://www.langflow.org/blog/ai-coding-agent-langflow
[9] Langflow Documentation: What is Langflow? https://docs.langflow.org
[10] Langflow | Low-code AI builder for agentic and RAG applications https://www.langflow.org
[11] Building an End-to-End Agentic AI RAG System with Langflow and ... https://www.krishnaik.in/blog/3-%20End%20To%20End%20Agentic%20AI%20RAG%20With%20Langflow%20With%20Data%20Stax%20VectorDB
[12] AI Agents in LangGraph https://www.deeplearning.ai/short-courses/ai-agents-in-langgraph/
[13] LangFlow Agents: Build & Deploy AI Agents Easily - Leanware https://www.leanware.co/insights/langflow-agents
[14] Build an AI agent with Langflow, Granite 4.0 models, and watsonx ... https://developer.ibm.com/tutorials/develop-langflow-tools-watsonx-orchestrate-granite/
[15] Langflow https://www.geeksforgeeks.org/artificial-intelligence/langflow/
[16] Langflow is a powerful tool for building and deploying AI ... https://github.com/langflow-ai/langflow
[17] How to Build a Basic Agentic Workflow using DataStax Langflow https://www.youtube.com/watch?v=LuJ_FM1l1OA
[18] LangFlow Tutorial: Building Production-Ready AI ... https://www.firecrawl.dev/blog/langflow-tutorial-visual-ai-workflows
