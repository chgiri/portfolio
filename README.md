# GenAI Portfolio

### Hi, I'm a Senior Software Engineer building hands-on GenAI expertise on a 17-year Java/Spring Boot foundation

I spent most of my career building traditional enterprise software. These four repos are where I taught myself GenAI engineering from scratch — deliberately covering different techniques (RAG, protocol integration, structured extraction, cross-language ML) rather than one deep pattern repeated four times, with the debugging stories left in, not smoothed over.

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

### How These Fit Together

Two independent problems, each split into a "core service" and a "why it's separate" companion, for two different concrete reasons:

```
banking-ai-agent      ──(REST)──►  banking-mcp-server
  (RAG, tools, orchestration)      (protocol wrapper for external AI clients)

ai-loan-underwriting   ──(gRPC)──►  loan-underwriting-ml
  (extraction, rules, explanation)      (cross-language ML service)
```

Every split was made for a stated architectural reason, not by default — documented explicitly in each repo's own README.

---


