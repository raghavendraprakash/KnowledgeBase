# Speaker Script — "What Skills Will Tomorrow's Engineers Need?"
### 20-Minute Keynote | AI First Engineering
---

> **How to use this script**
> — Times are cumulative from session start.
> — [PAUSE] = hold for 2–3 seconds; let the room absorb.
> — [SLIDE] = advance the slide.
> — Words in *italics* are emphasis cues.
> — Aim for conversational delivery, not verbatim reading.

---

## SLIDE 1 — Title Slide
**[0:00 – 0:45] | ~45 seconds**

*(Walk to centre stage. Say nothing for 5 seconds. Look at the audience.)*

"I want to ask you a simple question. And I want you to actually think about the answer — not just nod along.

*When was the last time the code you wrote… genuinely changed how a business worked?*

Not fixed a bug. Not added a feature. But fundamentally changed something.

[PAUSE]

Hold that thought. In the next 20 minutes, I'm going to tell you why that question matters more today than it ever has."

**[SLIDE]**

---

## SLIDE 2 — Opening Provocation
**[0:45 – 2:30] | ~1 min 45 sec**

"Open your minds for the next 20 minutes.

I have three numbers for you.

**3x.** Enterprise GenAI projects will triple by next year. Not double. Triple.

**72%.** Nearly three quarters of engineering roles now list AI skills as a requirement. Not preferred. *Required.*

**Zero.** Zero companies are hiring what I'd call 'prompt-only engineers.' Someone who just talks to a chatbot is not an engineer. That's a user.

[PAUSE]

So here is the uncomfortable truth: *the industry is changing faster than most engineering curricula.* And the gap between what universities teach and what companies need has never been wider.

But here's the good news — and I mean this sincerely — *the gap is also the opportunity.*

The engineers who understand what's coming, who build the skills this era demands, will be the ones who shape it. Everyone else will be maintained by it.

Let's talk about what's coming."

**[SLIDE]**

---

## SLIDE 3 — The Paradigm Shift
**[2:30 – 4:30] | ~2 min**

"We've been through paradigm shifts before. And we always underestimated them at the time.

When the mobile era arrived in the 2000s, companies said: 'We'll just make our website responsive.' Within five years, companies that didn't rebuild *everything* around the mobile experience were gone.

When cloud arrived in the 2010s, companies said: 'We'll just host our servers with AWS.' Within five years, the infrastructure itself became invisible. Scalable. On-demand. The entire model of software delivery changed.

[PAUSE]

We are *now* at the same inflection point with AI.

And I'm going to tell you something that is absolutely critical to internalise:

*AI is no longer something you add to a system. AI IS the system.*

Generative models. Intelligent agents. Machine learning pipelines. These are not features. They are the *architecture*. They are the core fabric.

The shift from prototype to production in AI is not just a software engineering challenge. It is a fundamentally *new discipline*. And that's precisely what I want to walk you through.

[PAUSE]"

**[SLIDE]**

---

## SLIDE 4 — AI Enabled vs AI First
**[4:30 – 7:00] | ~2 min 30 sec**

"Now let me give you a real-world example — because this distinction is something most engineers get wrong, and most companies get wrong.

Think about an expense claim system. The kind every company uses.

The *old way:* Employee logs in, fills in the form, uploads the invoice, submits. Maybe the latest version uses AI to extract text from the uploaded PDF. That's nice. That saves 30 seconds.

That is what I call **AI Enabled.** The business process is *identical*. There's no reimagination. The employee still needs to remember to log in. Still needs to submit. The AI is a convenience layer.

Now imagine this:

The company mandates its own wallet app on employees' phones. Any business expense — taxi, hotel, lunch with a client — is paid directly through that wallet. In the background, the AI automatically categorises the expense, matches it to the project code, cross-checks against the policy, and files the claim. The employee doesn't log into anything. The expense is done *before they've even gotten out of the cab.*

Better still — before the trip happens, the AI has already booked the hotel and the cab, based on the employee's travel preferences and meeting schedule.

[PAUSE]

*That* is **AI First.** The business process itself was reimagined. The employee's productivity didn't improve slightly — it improved *entirely*.

Here's the test. Two questions. Write these down:

One: Is this AI feature impactful? The answer must be YES.

Two: Can the end objective be achieved without this AI feature? The answer must be NO.

If both conditions are met — you are thinking AI First. If not, you are just adding a feature.

[PAUSE] Let that sink in."

**[SLIDE]**

---

## SLIDE 5 — The Two-Question Test
**[7:00 – 8:00] | ~1 min**

"I'm going to leave this slide up for just a moment because I want it to burn into your memory.

[PAUSE — read the slide slowly, let audience absorb]

*Is this AI feature impactful?* → YES.

*Can the end objective be achieved without it?* → NO.

This is your filter. Every time you are designing a feature, an application, a system — run it through this test. If you can't answer both questions the right way, go back to the drawing board.

Because here's what will happen if you don't: your company will call itself 'AI First' in its marketing materials while its engineers are still writing the same code they were writing five years ago. And the market will find out.

[PAUSE]

Now I want to shift. Because thinking AI First is the *what.* What comes next is the *how.* And the how is where it gets genuinely hard."

**[SLIDE]**

---

## SLIDE 6 — Five Engineering Frontiers
**[8:00 – 9:00] | ~1 min**

"There are five engineering challenges that define this new era. Five places where traditional software engineering — the kind we've been teaching and practicing for decades — simply *breaks down.*

You cannot use the same tools. You cannot use the same mental models. You need new skills.

Let me walk you through each one.

[PAUSE]"

**[SLIDE]**

---

## SLIDE 7 — Challenges 01 & 02: Evaluation + RAG
**[9:00 – 12:00] | ~3 min**

"**Challenge One: Non-Determinism and the Evaluation Crisis.**

Here's something that will fundamentally break the way you think about testing.

In traditional software: same input → same output. Always. Every time. That's the contract. That's how unit tests work. That's how CI/CD pipelines work.

In AI applications: same prompt → *different* output. Every. Single. Time.

Your model is probabilistic. It is non-deterministic by design. So how do you write a unit test for a response that is slightly different every time? How do you know if your new model version is *better* or just *different?*

The CI/CD pipelines that the entire software industry built over the last 20 years — they *break* when the output is fluent text or an image or an agentic action.

This is not a small problem. This is a foundational engineering challenge.

The skill you need? **LLM-as-a-Judge** evaluation — using AI to assess AI output at scale. Semantic versioning for prompts — because your prompts are now production assets, not throwaway strings. And statistical safety thresholds — because quality in AI systems is a *distribution*, not a binary.

[PAUSE]

**Challenge Two: State, Memory and Latency in RAG.**

RAG — Retrieval-Augmented Generation — is how you ground AI in your company's actual data. But it introduces massive orchestration complexity.

You're pulling data from vector databases. Parsing multi-modal formats — PDFs, images, audio. Constructing context windows. Managing multi-turn conversations where the model needs to *remember* what was said three exchanges ago. All of this needs to happen in milliseconds, because users have no patience for a slow AI.

The skills here are: hybrid search architectures — combining vector search with keyword search and full-text search. Semantic caching layers — so you're not making expensive model calls for questions that were already asked. And edge-hosted or fine-tuned smaller models to bring latency down to where it's acceptable.

[PAUSE]"

**[SLIDE]**

---

## SLIDE 8 — Challenge 03: Multi-Agent Security
**[12:00 – 14:00] | ~2 min**

"I'm going to say something and I want you to really hear it.

*A hallucinating agent — given the right permissions — can delete your production database.*

[PAUSE]

This is not hypothetical. This is not science fiction. This is a real engineering risk that exists today.

Here's how it happens. You build an AI agent that handles customer service. It has access to the CRM update API. It has access to the email system. It has access to the order management system — because why wouldn't it? It needs those to do its job.

Now, an external API it depends on throws an unexpected error. The agent tries to retry. It fails. It tries a different approach. It gets confused. It enters what we call a **hallucination loop** — reasoning in circles, burning tokens with every iteration, potentially taking destructive actions it was never meant to take.

[PAUSE]

The consequences? Massive token costs. Corrupted data. In the worst case — leaked PII or deleted records.

This is what happens when you give an AI agent high privileges without guardrails.

The engineering discipline here is explicit and non-negotiable:

**Deterministic guardrails** around every agent execution layer. Your agent does not take an action that has not been pre-approved structurally.

**Human-in-the-Loop checkpoints** for high-risk actions. The agent presents, a human approves.

**Sandboxed microservices** — so when an agent uses a tool, it runs in an isolated environment that can be contained if something goes wrong.

[PAUSE]

Security in AI systems is not an afterthought. It is a *first-class engineering concern.*"

**[SLIDE]**

---

## SLIDE 9 — Challenges 04 & 05: Data + Cost
**[14:00 – 16:30] | ~2 min 30 sec**

"There's an old saying: *show me your friends, and I'll tell you who you are.*

In AI engineering, that saying becomes: *show me your data, and I'll tell you how good your AI application is.*

**Challenge Four: Data Quality and Governance.**

Enterprise data is a mess. Let me be direct about this. It is fragmented across legacy systems. It is siloed in departmental databases that haven't talked to each other in a decade. It is unstructured — documents, emails, images, meeting transcripts — all in different formats.

Your ETL pipelines — Extract, Transform, Load — are brittle. They break when the data schema changes. They're slow. And increasingly, you also need real-time streaming data — because an AI that responds to information that's six hours old is not useful in a live business environment.

And here is the one that should make every engineer pause: *PII, confidential information, and copyrighted content must never enter your AI models. Not accidentally. Not through a poorly designed ingestion pipeline.*

Data engineering — Apache Kafka, Apache Pulsar, Neo4J, DGraph — these are not niche skills anymore. They are the *foundation* of AI First systems.

[PAUSE]

**Challenge Five: Cost Management.**

Here is a scenario that happens to AI teams more often than anyone admits.

You launch your AI application. It gets traction — users love it. Traffic spikes. And suddenly your cloud bill for that month is five times what you projected.

Why? Because every user query was hitting your most expensive, most powerful reasoning model. The model that charges by the token. The model that you don't actually need for simple queries.

The engineering skill here is **semantic routing.** You build a router that looks at every incoming query and decides: is this simple enough for a lightweight, cheap model? Or does this require deep reasoning that justifies a large model?

You also need to build cost models upfront — cost attribution per feature, per query type. Because a solution that works but that your customers can't afford to scale is not a solution.

[PAUSE]"

**[SLIDE]**

---

## SLIDE 10 — The New Engineer Identity
**[16:30 – 18:30] | ~2 min**

"Let me bring this home.

Industry does not expect you to write every line of code. That expectation — the one that drove engineering education for the last 30 years — is changing.

What industry needs now is *architects.*

Not architects who draw boxes in PowerPoint. Engineers who *architect AI systems* — who design the pipelines, build the guardrails, engineer the memory layers, instrument the feedback loops.

The four verbs that define this new identity are on the screen:

**Build** — data pipelines, guardrails, memory systems.

**Design** — feedback loops, evaluation suites, cost models.

**Govern** — security policies, data lineage, reliability at scale.

**Harness** — capable AI models, selected and deployed cost-efficiently and securely.

[PAUSE]

The coder of yesterday becomes the architect of tomorrow's AI systems.

And I want to be clear — this is not a threat. This is the most exciting engineering moment in a generation. The engineers who lean into this, who build these skills, who embrace this architectural mindset — *they are the ones who will define what AI First looks like in practice.*

[PAUSE]"

**[SLIDE]**

---

## SLIDE 11 — Call to Action
**[18:30 – 20:00] | ~1 min 30 sec**

"We have covered a lot in 20 minutes. And I know that's a lot to absorb.

So I want to leave you with one concrete thing. Not five. Not a reading list. *One thing.*

[PAUSE — scan the room]

When you walk out of here today, I want you to pick *one skill* from these five:

LLM-as-a-Judge. Semantic Caching. HITL Workflows. Kafka or Neo4J. Semantic Routing.

Just one. Go home and spend an hour on it tonight. Read one article. Watch one video. Build one small prototype.

Because the gap between prototype and production is where great engineers are forged. That gap is *where you are right now* — and what you do in it determines everything.

[PAUSE]

The industry is moving. The paradigm has shifted. The question is no longer *whether* AI First will define engineering — it already does.

The question is whether *you* will define AI First.

[PAUSE]

Thank you."

---

## Delivery Notes

| Slide | Key Technique | Watch Out For |
|-------|---------------|---------------|
| 1 (Title) | Start with silence — 5 full seconds | Don't rush the opening hook |
| 2 (Stats) | Read each stat slowly, one at a time | Don't rattle through all three quickly |
| 3 (Timeline) | Use hand gesture for "Mobile → Cloud → AI" left to right | Point to screen naturally |
| 4 (Comparison) | Tell the expense claim story as a *story*, not a list | Avoid reading the slide bullets |
| 5 (Test) | Pause after each question. Let audience think | Don't answer immediately — hold the beat |
| 6 (5 Challenges) | Build anticipation — "Let me walk you through each one" | Don't pre-summarise all five |
| 7 (Eval + RAG) | The unit test line should land as a revelation | Slow down: "same input → same output" |
| 8 (Agents) | "A hallucinating agent can delete your DB" — dead pause after | Do NOT soften this with "but don't worry" |
| 9 (Data + Cost) | The data-is-a-mess section: be direct, almost blunt | Avoid being too technical on tools |
| 10 (Identity) | Land the four verbs slowly, one at a time | Don't rush to the next — let each verb land |
| 11 (CTA) | The final pause before "Thank you" should be 3 seconds | Don't say "So, um, thank you" — just "Thank you" |

---
