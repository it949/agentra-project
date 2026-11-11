# Agentra × Assotech, Phase 1 backlog

The SOW work broken into tasks of 1–3 days, each with a single owner. IDs are stable; reference them in branch names and commit messages (e.g. `BE-07: whatsapp webhook`).

**Repos:** FE = `agentra-frontend` (React + Vite), BE = `agentra-backend` (NestJS + Postgres), AI = `agentra-ai` (Python: Temporal + LangGraph), PRJ = `agentra-project` (docs). Architecture: [ARCHITECTURE.md](ARCHITECTURE.md).
**Status:** ✅ done · 🔨 in progress · ⬜ not started · ⛔ blocked on client (SOW §9)

## Week 0–1: foundations and discovery

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| PRJ-01 | Workflow Register draft: 12–20 scenarios, each with read/write, risk tier and acceptance test | PRJ | client workshops | Client signs it (D1.1) | ⬜ |
| PRJ-02 | Qandle API coverage test: every endpoint, read and write, with rate limits | PRJ | ⛔ Qandle API docs + sandbox | Feasibility report (D1.2) | ⬜ |
| PRJ-03 | Qandle UI study: login/MFA, concurrent sessions, iframes, target screens | PRJ | ⛔ test accounts | Section in D1.2 | ⬜ |
| PRJ-04 | Detailed plan with dated client-dependency milestones (§6.1, needed for the §6.4 delay clause) | PRJ | PRJ-01 | Sent within 10 business days of kickoff | ⬜ |
| PRJ-05 | Usage and cost estimate for LLM, WhatsApp and hosting (§8) | PRJ | PRJ-01 | Sent before kickoff | ⬜ |
| PRJ-06 | Start Meta Business verification and WhatsApp display-name approval | PRJ | ⛔ client's Meta business account | Number approved | ⬜ |
| ALL-01 | Repo scaffolds, CI, contract v1 (React / NestJS / Temporal + LangGraph) | FE/BE/AI | — | Lint and tests green; local end-to-end run | ✅ |
| ALL-02 | Create GitHub repos under it949 and push | — | ⛔ it949 owner creates repos | `main` pushed | ⛔ |
| INF-01 | AWS Mumbai account baseline: VPC, RDS Postgres + pgvector, Redis, S3 with KMS, Secrets Manager (Terraform) | BE | client hosting decision (§9.7) | `terraform apply` in a dev account | ⬜ |
| INF-02 | Temporal for dev and staging (self-hosted or Cloud) | BE | INF-01 | Workers connect from CI | ⬜ |

## Week 2: proof of concept (D1.3)

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| AI-01 | Qandle login + session store for one service account (Playwright) | AI | PRJ-03 | Logs in headless, cookie stored encrypted | ⬜ |
| AI-02 | PoC read flow: leave balance (API if available, else browser) | AI | AI-01 | Correct balance for 3 test employees | ⬜ |
| AI-03 | PoC write flow: one agreed low-risk write, with read-back verification | AI | AI-01 | Write + verify recorded on video | ⬜ |
| PRJ-07 | Witnessed PoC session with the client, with a written record | PRJ | AI-02, AI-03 | Client signs the PoC record | ⬜ |

## Week 2–3: Agent Core (D1.4)

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| AI-04 | `HandleRequest` workflow: clarify loop, policy, approval, write-once execute, verify | AI | ALL-01 | Time-skipping tests green | ✅ |
| AI-05 | Workflow template registry (loaded from the signed Register) | AI | PRJ-01 | Every Register entry has a template | ⬜ |
| AI-06 | `interpret` activity: LangGraph graph, Claude structured output → template key + fields | AI | AI-05 | ≥95% on golden set (EV-01) | 🔨 graph built, needs golden set |
| AI-07 | PII redaction before every LLM call; Langfuse tracing | AI | AI-06 | No raw Aadhaar, PAN or phone number in traces | ⬜ |
| AI-08 | `execute` with idempotency keys and `unknown` outcome handling | AI | AI-02 | Killing the worker mid-write produces `unknown_outcome`, never a duplicate | ⬜ |
| AI-09 | `verify` read-back per template | AI | AI-08 | Field-by-field comparison stored as evidence | ⬜ |
| AI-10 | Browser session pool (one live session per account), Playwright trace + video per run | AI | AI-01 | Parallel tasks queue instead of logging each other out | ⬜ |
| AI-11 | Computer-use fallback when a scripted selector fails, with alerting | AI | AI-10 | Broken-selector test recovers or fails safe | ⬜ |
| BE-01 | `/internal/policy`: RBAC + data scope + action allowlist | BE | ⛔ permission matrix (§9.2) | Employees can only act on their own record | 🔨 deny-by-default draft |
| BE-02 | `/internal/tasks/{id}/events` + hash-chained audit log | BE | ALL-01 | Tampering breaks the chain check | ✅ |
| BE-03 | Task store + start `HandleRequest` (workflow ID = task ID); retry sweep for tasks stuck in `received` | BE | BE-02 | Duplicate webhook does not start a second run | 🔨 sweep pending |
| BE-04 | Approvals API: list, approve, reject → Temporal signal; 24h expiry | BE | BE-03 | Approval in the console resumes the workflow | ✅ API (console button: FE-06) |
| EV-01 | Golden set: 150+ real phrasings per workflow (English, Hindi, Hinglish) | AI | PRJ-01 | Runs in CI, scores reported | ⬜ |
| EV-02 | Prompt-injection suite (email, WhatsApp, document text) | AI | AI-06 | No injected approval or scope change succeeds | ⬜ |

## Week 3: channels (D1.5)

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| BE-05 | Identity resolver: phone/email → employee ID, step-up OTP for sensitive requests | BE | ⛔ verification approach (§9.4) | Unknown numbers are refused politely | ⬜ |
| BE-06 | WhatsApp webhook: signature check, dedupe, media download to S3 | BE | PRJ-06 | Test number round-trips a message | ⬜ |
| BE-07 | WhatsApp replies and templates; 24h window rules | BE | BE-06 | Reminder template approved by Meta and sending | ⬜ |
| BE-08 | Email intake (Graph/Gmail) with SPF/DKIM/DMARC checks | BE | ⛔ HR mailbox access | Spoofed sender rejected | ⬜ |
| BE-09 | Escalation to an HR ticket for out-of-scope requests | BE | BE-03 | Ticket ID returned in the reply | ⬜ |

## Week 4: knowledge base and console (D1.6, D1.8)

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| BE-10 | KB document lifecycle API: draft → review → publish → retire, with versions | BE | ALL-01 | Only one published version per document | ⬜ |
| AI-12 | KB ingestion: chunking, embeddings, hybrid search over published documents only | AI | BE-10 | Retired version never retrieved | ⬜ |
| AI-13 | `kb.answer`: answers cite a source or decline | AI | AI-12 | No uncited answers in EV-01 | ⬜ |
| BE-11 | Console auth: OIDC with the client's identity provider; console roles | BE | ⛔ IdP details | HR admin vs viewer enforced | ⬜ |
| BE-12 | Console read API: tasks, task detail, failures, KB, audits (OpenAPI) | BE | BE-03 | Frontend client generated from it | 🔨 tasks, detail, failures, approvals done |
| FE-01 | Design system: tokens, light/dark, type scale, badges, cards | FE | — | Matches the reviewed screenshots | ✅ |
| FE-02 | Console shell: sidebar, mobile nav, page headers | FE | FE-01 | Works at 1440px and on phones | ✅ |
| FE-03 | Overview, Requests, Request detail, Approvals, Needs attention, Knowledge and Audits pages on sample data | FE | FE-02 | Build green; all pages render | ✅ |
| FE-04 | Swap sample data for the generated API client | FE | BE-12 | `src/lib/data.ts` calls the backend | ⬜ |
| FE-05 | Sign-in flow and role-aware navigation | FE | BE-11 | Viewer cannot approve | ⬜ |
| FE-06 | Approve/reject wired to BE-04, with confirmation and optimistic state | FE | BE-04 | Decision shows in the task timeline | ⬜ |
| FE-07 | KB editor: upload, review, publish, retire | FE | BE-10 | HR can publish without engineering | ⬜ |
| FE-08 | Task detail: evidence viewer (browser recording playback, Qandle links) | FE | AI-10 | Recording plays inline | ⬜ |

## Week 5: onboarding, documents, helpdesk (D1.7, D1.11)

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| BE-13 | Onboarding: missing-field detection, checklists, reminders, exceptions | BE | AI-02 | Reminder sent for a missing document | ⬜ |
| AI-14 | Document checks: file type, blank page, blur, password-protected PDF | AI | — | Bad uploads rejected with a reason | ⬜ |
| AI-15 | OCR + classification (Aadhaar, PAN, certificates, relieving letters); Aadhaar masked on capture | AI | AI-14 | Full Aadhaar never stored | ⬜ |
| AI-16 | Field extraction + fuzzy comparison with the employee profile | AI | AI-15 | Below-threshold matches go to a human | ⬜ |
| AI-17 | Helpdesk templates: attendance correction, leave apply/status, payslip, profile requests | AI | AI-05, AI-09 | Each passes its Register acceptance test | ⬜ |
| FE-09 | Onboarding tracker page | FE | BE-13 | Completion per new joiner visible | ⬜ |

## Week 6: audit engines (D1.9, D1.10)

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| BE-14 | Qandle data sync: attendance, leave, payroll inputs (API or scheduled UI export) | BE | PRJ-02 | Daily sync with row counts reconciled | ⬜ |
| BE-15 | Attendance rules: missing punch, manual edit, missing approval, overlaps, shift exceptions | BE | BE-14, ⛔ HR rule sign-off | Each rule has fixtures and tests | ⬜ |
| BE-16 | Leave and payroll pre-audit rules + reconciliation report | BE | BE-14 | Report matches a hand-checked month | ⬜ |
| FE-10 | Audits page: filters, export, mark reviewed | FE | BE-15 | HR can close an exception | ⬜ |

## Week 7–8: hardening, UAT, acceptance

| ID | Task | Repo | Depends on | Done when | Status |
|---|---|---|---|---|---|
| QA-01 | End-to-end acceptance run per Register scenario (§4.3, all six criteria) | ALL | all above | Every scenario green, evidence captured | ⬜ |
| QA-02 | Security review: secrets, RBAC bypass attempts, injection, data retention | ALL | — | No open critical findings | ⬜ |
| QA-03 | Load test: concurrent WhatsApp bursts, browser pool saturation | ALL | AI-10 | p95 targets agreed and met | ⬜ |
| PRJ-08 | UAT with HR; defect triage (Critical vs other) | PRJ | ⛔ client UAT availability | No open Critical Defects | ⬜ |
| PRJ-09 | Runbooks: unknown outcomes, Qandle UI change, breach notification | PRJ | — | Handed to the client | ⬜ |
| PRJ-10 | Delivery notice (§7.4) → 20% invoice; support/SLA proposal | PRJ | QA-01 | Notice sent | ⬜ |
