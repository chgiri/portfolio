# GenAI Portfolio

### Hi, I'm a Senior Software Engineer building hands-on GenAI expertise on a 17-year Java/Spring Boot foundation

I spent most of my career building traditional enterprise software. These six repos are where I taught myself GenAI engineering from scratch — deliberately covering different techniques (RAG, protocol integration, structured extraction, cross-language ML, cross-document synthesis, graph-based multi-hop detection) rather than one deep pattern repeated six times, with the debugging stories left in, not smoothed over.

---

### 🏦 [banking-ai-agent](https://github.com/chgiri/banking-ai-agent)
**RAG · Tool Calling · Multi-Agent Orchestration**

A banking assistant combining Retrieval-Augmented Generation over policy documents, per-document scoped Q&A, tool-calling with code-enforced account authorization (never trusting the model to scope its own permissions), and an intent-routing layer across all three. Built with Spring Boot, Spring AI, pgvector, and Google Gemini.

- Grounded answers with source citations, verified against deliberate out-of-scope questions
- A two-step propose/confirm pattern for irreversible actions — adversarially tested, not just assumed safe
- Honest documentation of what the orchestrator *isn't*: a router, not collaborating agents — precision over overselling

---

### 🔌 [banking-mcp-server](https://github.com/chgiri/banking-mcp-server)
**Model Context Protocol**

A standalone MCP server exposing `banking-ai-agent`'s capabilities to MCP-compatible AI clients (Claude Desktop, Claude Code, MCP Inspector) — calling the original app over its REST API rather than reaching into its internals, the way you'd wrap a real production service you don't own.

- A documented, unsolved-on-purpose limitation: account-scoped tools aren't exposed here, because MCP's bean-discovery model doesn't map cleanly onto per-request account binding

---

### 💰 [ai-loan-underwriting](https://github.com/chgiri/ai-loan-underwriting)
**Structured Extraction · Deterministic Decisioning · Cross-Language ML**

A loan underwriting service where the LLM extracts structured data and explains a decision — but never makes the decision itself. That's handled by a deterministic hard-rules engine plus a machine-learned risk score, called over gRPC from a separate Python service.

- Every rule independently testable and auditable — a rejection traces to the exact threshold that fired
- `HIGH` risk routes to human review, not auto-rejection, mirroring the same automate-the-clear-cases philosophy as the banking project's transfer flow
- A real cross-language contract (Protobuf) between Java and Python, including a live-debugged classpath BOM conflict

---

### 🐍 [loan-underwriting-ml](https://github.com/chgiri/loan-underwriting-ml)
**Python · scikit-learn · gRPC**

The ML half of the loan project: trains and serves a logistic regression default-risk model over gRPC. Logistic regression chosen deliberately, not by default — lending is a regulated domain where explainability matters, and coefficient-level interpretability beats black-box accuracy here.

- Openly documents its own limitation: trained on synthetic data, not real historical outcomes
- Server reflection enabled, so any standard gRPC tool can discover and call it without needing the `.proto` file separately

---

### 👥 [ai-attrition-insight](https://github.com/chgiri/ai-attrition-insight)
**Cross-Document Synthesis · Structured Extraction · Independent Validation**

An HR-domain tool that synthesizes recurring themes across an employee's unstructured feedback — 1:1 notes, engagement surveys, peer feedback — surfacing patterns no keyword search or dashboard could find, since the same concern is expressed in completely different words by different people at different times. Deliberately scoped around a problem that requires genuine language understanding, not a database query with an AI label on it. Pure Java/Spring AI, no companion service — a different kind of GenAI problem than the other four.

- The core proof: three feedback entries with *zero shared vocabulary* correctly recognized as the same recurring theme — and, just as important, a genuinely unrelated case correctly recognized as no pattern at all
- A built-in, non-circular validation mechanism: a departed employee's exit interview is never used as input signal, only to independently check afterward whether a pre-departure pattern prediction actually matched their real stated reason for leaving
- An explicit, hard-coded safety rule preventing the model from ever predicting departure — it reports a pattern in language, never a verdict on a person — paired with an upfront README section on why this must never be deployed as covert surveillance

---

### 🕸️ [ai-fraud-ring-detector](https://github.com/chgiri/ai-fraud-ring-detector)
**Graph Database · Multi-Hop Traversal · Neo4j**

A fraud detection tool that finds accounts connected through shared attributes — phone numbers, addresses, beneficiaries — even across applications that never reference each other directly. Built specifically to justify a NoSQL choice architecturally, not by convenience: this is the one problem in the portfolio where the *query itself* (multi-hop relationship traversal) requires a graph database, not just where a graph database happens to be usable. First project here without a vector store or embeddings at all — detection happens entirely through graph structure.

- The core proof: two applications with zero direct connection to each other, correctly found to be linked two hops apart, purely through a shared intermediate account — something no keyword search, SQL join, or similarity search could surface
- Severity is derived deterministically from the graph data in code, never left to the model's judgment — same "code decides, LLM explains" discipline as every other project, applied to a graph instead of a rules engine
- A real, honestly-documented bug: a Cypher query counted raw graph edges instead of logical account-to-account hops, which silently prevented the severity signal from ever elevating correctly — caught by testing the *meaning* of output, not just that a response came back successfully

---

### How These Fit Together

Two paired problems, each split into a "core service" and a "why it's separate" companion for a stated architectural reason — plus two standalone projects proving the underlying techniques transfer to different domains and data models entirely, without forcing an unnecessary split just for symmetry:

```
banking-ai-agent      ──(REST)──►  banking-mcp-server
  (RAG, tools, orchestration)      (protocol wrapper for external AI clients)

ai-loan-underwriting   ──(gRPC)──►  loan-underwriting-ml
  (extraction, rules, explanation)      (cross-language ML service)

ai-attrition-insight              ai-fraud-ring-detector
  (cross-document synthesis,        (graph traversal, Neo4j —
   standalone — no companion         standalone, no companion
   needed)                           needed)
```

Every split (and every non-split) was a deliberate choice, not a default — documented explicitly in each repo's own README.

---
