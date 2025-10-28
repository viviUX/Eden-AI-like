
# Eden AI–Equivalent Product — Documentation Pack
**Last updated:** 2025-10-27

This document packages the plan we discussed — what to build, how to build it, and how long it takes — in a format your team can scan quickly, discuss, and import into tools like **Notion** or **Confluence**.

> **How to use this pack**
> 1. Import this `.md` file into Notion or Confluence (or paste the content).
> 2. Upload the `CSV` files as tables or import them into Sheets/Excel for tracking.
> 3. (Optional) Attach the Gantt image to your page, or recreate it with your PM tool.

---

## 1) Goals & Non‑Goals

**Goals**
- Ship an *Eden AI–like* aggregator: unified API across LLM/speech/vision/OCR/translation, with **playground**, **monitoring**, **workflows**, and a **hosted RAG assistant**.
- Provide **price/latency visibility** and **cost controls**; support **BYO provider keys**.
- Deliver **SDKs & integrations** so customers can integrate quickly.

**Non‑Goals (for v1)**
- Perfect parity on every provider or feature.
- Custom on‑prem deployments (can be a follow‑on).
- Advanced router/guardrails beyond basic safety (post‑v1).

---

## 2) Feature Overview (v1 target)

- **Unified API & Adapters**: Normalized requests/responses; async jobs; rate limits & quotas.
- **Monitoring & Cost Reporting**: Request history, traces, per‑org budgets.
- **Playground & Provider Comparison**: Side‑by‑side outputs, latency and **price**.
- **Workflow Builder**: Visual nodes (LLM, AI features, logic, code node, web scraper), webhooks.
- **Hosted RAG Assistant**: Ingestion (files/URLs), chunk/embeddings, retriever + chat API.
- **SDKs & Integrations**: JS/Python, LangChain, Zapier/Make, Bubble.
- **Enterprise**: SSO, audit logs, DPA, data‑flow docs.

See **Capability Matrix** (CSV) for in/out of scope.

---

## 3) Architecture & Subsystems (high‑level)

- **API gateway & adapter layer** with a capabilities matrix and error taxonomy.
- **Identity, tenancy, billing** with credits/metering and BYO keys (vault + rotation).
- **Async job manager** (polling and webhooks) for long‑running tasks (STT/OCR).
- **Observability**: logs, metrics, traces; request & workflow run history.
- **Playground**: shared evaluation surface; code‑snippet copy.
- **Workflow engine**: DAG execution, retries, run inspector, partial re‑run.
- **RAG**: ingestion pipelines, vector DB, retriever, LLM chat scaffold.
- **Security & compliance**: SSO, RBAC, audit logs, data residency notes.

---

## 4) Delivery Plan & Timeline

### 4.1 Midpoint schedule (example)
This is a realistic, *single‑track* schedule assuming a 10–12 person team and parallel workstreams.

![Gantt](EdenAI_Gantt.png)

| Phase | Start (Week) | Duration (wks) | End (Week) | Dependencies |
|---|---:|---:|---:|---|
| Phase 0 — Product & architecture | 1 | 2 | 2 | — |
| Phase 1 — Core platform spine | 2 | 5 | 6 | P0 |
| Phase 2 — Unified API + first connectors | 3 | 4 | 6 | P1 (partial) |
| Phase 3 — Monitoring & developer UX | 3 | 4 | 6 | P1 (partial) |
| Phase 4 — Playground & provider comparison | 3 | 4 | 6 | P1 (partial) |
| Phase 5 — Workflow engine & builder | 6 | 8 | 13 | P1–P4 foundations |
| Phase 6 — Hosted RAG assistant | 7 | 6 | 12 | P2 (LLM/Embeddings) |
| Phase 7 — SDKs & integrations | 9 | 4 | 12 | P2/P3 |
| Phase 8 — Enterprise & compliance | 10 | 4 | 13 | P1–P3 |
| Phase 9 — Hardening & pilots (parity‑plus) | 13 | 10 | 22 | P5–P8 feature‑complete |


**Milestones**
- **MVP Aggregator (P0–P4 complete + basic hardening):** ~Weeks 1–12
- **Eden‑equivalent v1 (P0–P8 complete + P9 pilots):** ~Weeks 1–22 (≈ **20–25 weeks** total)

> See the *Timeline (range)* CSV for earliest/latest end‑weeks by phase.

### 4.2 Dependencies (summary)
- P2–P4 depend on P1 (gateway, auth, metering).
- P5 relies on P1–P4 foundations; P6 uses P2 (LLM/Embeddings) and storage.
- P7 builds on P2/P3; P8 adds enterprise SSO/audit/compliance.

---

## 5) Team & Roles

- **Backend (5–7)**: API gateway, adapters, async jobs, metering/billing, workflow engine, RAG backend.
- **Frontend (2)**: Console, playground, workflow UI, docs site.
- **Infra/SRE (1)**: Kubernetes/Terraform, observability, secrets, scaling, CI/CD.
- **ML/Applied (1)**: Eval harness, embeddings/retrieval tuning, cost/latency benchmarking.
- **Design/PM/QA (1–2)**: UX for playground/builder, release cadence, automated tests.

---

## 6) Risks & Mitigations
See **Risk Register** (CSV). Top items:
- Provider churn/outages → abstraction + fallbacks.
- Cost surprises → per‑org budgets/alerts + playground cost hints.
- Workflow/RAG complexity → minimal viable nodes; eval harness; strong run‑inspector.

---

## 7) Assumptions
- Team size: ~10–12 (can scale schedule if smaller).
- Provider coverage: 2–3 per feature in v1 (add more post‑v1).
- Regions & privacy: default to US/EU; redact PII by default in logs.

---

## 8) Open Questions
- Which vector DB and queueing system?
- Minimum list of day‑1 providers per feature?
- Pricing model (vendor rate only vs. platform margin)?
- Data residency requirements by customer segment?

---

## 9) How to import this document

- **Notion**: Create a page → “Import” → **Text & Markdown** → upload this `.md` file. Then add the CSVs as tables or databases.
- **Confluence**: Create a page → paste Markdown content (Confluence Cloud converts most Markdown) → attach CSVs and insert as tables.

---

## 10) Changelog
- {today_str}: Initial draft generated for team review.

---

### Appendices

**A. API Shape (sketch)**

```http
POST /v1/llm/chat
  body: {{ messages: [...], provider: "auto|openai|anthropic|..." , ... }}
  -> {{ id, provider, output, usage, cost, latency_ms }}

POST /v1/speech/transcribe
  body: {{ file: ..., provider: "...", ... }}
  -> async job id; poll GET /v1/jobs/{id}
```

**B. Workflow Nodes (initial set)**
- LLM Chat, Embeddings, STT, TTS, OCR/DocParse, Translate
- Logic: branch, map, reduce
- Tools: Code, Web scraper, Webhook In/Out

**C. RAG**
- Ingest (files/URLs) → chunk → embed → index
- Query: retrieve top‑k → synthesize with LLM → cite sources

---

*Prepared for import into your documentation workspace.*
