# AMC 8 Intelligent Exam Preparation Platform — Master Plan

**Document Status:** Approved Draft v1.0
**Owner:** Bill / Joysort
**Last Updated:** March 2026
**Short Name:** `amc-prep`

---

## Vision

An intelligent, adaptive platform that helps students systematically prepare for the AMC 8 exam — not by drilling random questions, but by building genuine mathematical understanding at the atomic knowledge level, diagnosing real cognitive gaps through conversation, and generating targeted assessments that mirror the intentional complexity of real AMC 8 problems.

---

## Core Design Principles

1. **Atomic knowledge before synthesis** — Students master individual concepts before combinations are tested
2. **Intent-aware assessment** — Every question is tagged with *why* it exists, not just what it covers
3. **Diagnosis over scoring** — Getting wrong or right matters less than understanding the reasoning pattern behind it
4. **Real data over assumptions** — No major design decision about the diagnostic engine is made without real student interaction data
5. **LLM as infrastructure, not product** — LLMs power the backend analysis and tutoring; the product is the pedagogy

---

## Technical Stack Decisions

| Layer | Choice | Rationale |
|-------|--------|-----------|
| **Framework** | Next.js (App Router) | Vercel-native, full-stack, SSR/SSG support |
| **Language** | TypeScript | End-to-end type safety |
| **Deployment** | Vercel | Already linked to repo; zero-config deploys |
| **Database** | Vercel Postgres (or Neon) | Vercel-friendly managed PostgreSQL |
| **ORM** | Prisma or Drizzle | Type-safe DB access, migration support |
| **LLM Integration** | Claude Agent SDK | Agentic workflows for annotation, tutoring, diagnosis |
| **Auth** | TBD (NextAuth / Clerk) | To be decided in Phase 2 |
| **Styling** | TBD (Tailwind CSS likely) | To be decided in Phase 2 |

**Known Scale Concern:** Claude Agent SDK usage per-student may become costly at scale. Architecture should allow for caching, batching, and potential fallback to lighter models for routine interactions.

---

## Phase Structure

### Phase 0 — Research & Data Gathering (CRITICAL FOUNDATION)

**Nature:** Research and manual work, tracked as tasks. Executed by the user with the Coder agent. No application code — data collection and schema design only.

**Goal:** Assemble the raw materials that every subsequent phase depends on.

#### 0A: Curriculum Mapping

- Obtain the official AMC 8 syllabus and topic outline from MAA
- Identify atomic knowledge points — the smallest independently testable concepts (e.g., "properties of isosceles triangles" rather than just "geometry")
- Map dependencies between knowledge points (e.g., LCM requires prime factorization)
- Build a knowledge dependency graph — this becomes the backbone of the adaptive learning path

**Key resources:**
- MAA official AMC 8 problem index and topic classification
- AoPS (Art of Problem Solving) AMC 8 topic breakdowns
- Reference: *Competition Math for Middle School* (Jason Batterson), AoPS Volume 1

#### 0B: Question Corpus Collection

- Collect all authenticated AMC 8 past papers (2000–present, officially released by MAA)
- Collect high-quality mock test banks from trusted sources (AoPS, MATHCOUNTS prep materials)
- Tag each question with: year, problem number, topic area (initial rough tag)
- Store in structured format with fields: question text, answer choices, correct answer, source, year

#### 0C: Annotation Framework Design

Draft the extraction schema — the structured format that captures the pedagogical intent of each question:

| Field | Description | Example |
|-------|-------------|---------|
| Primary knowledge point | Core concept being tested | Divisibility rules |
| Secondary knowledge points | Supporting concepts required | Odd/even number properties |
| Cognitive angle | How the problem tests the concept | Pattern recognition vs. direct computation |
| Trap / distractor | Intentional wrong path designed for the problem | Off-by-one counting error |
| Required connections | Cross-topic links student must make | Geometry + number theory |
| Difficulty factors | What makes it hard beyond base knowledge | Multi-step reasoning chain |
| Solution strategies | Valid approaches to solve | Backsolving, casework, direct formula |

**Validation requirement:** Schema must be manually validated on ~30 questions before any batch processing begins.

#### Phase 0 Deliverables

- [ ] AMC 8 topic outline document (from MAA)
- [ ] Knowledge dependency graph (structured data, not just whiteboard)
- [ ] Complete question corpus (2000–present) in structured format
- [ ] Validated annotation schema (tested on 30+ questions)
- [ ] Sample annotated questions (the 30+ validation set)

---

### Phase 1 — Knowledge Base & Question Analysis Engine (HIGH EMPHASIS)

**Nature:** Engineering phase. Build the static infrastructure — annotated question corpus and rules engine.

**Goal:** A fully annotated question database and content layer that powers everything downstream.

#### 1A: LLM-Powered Batch Annotation Pipeline

- Build a structured LLM pipeline (Claude Agent SDK) with human review checkpoints
- Process full authenticated question corpus using the Phase 0C schema
- Human review: spot-check ~10% of annotations for quality; refine prompts as needed
- **Output:** Fully annotated question database (source of truth)

#### 1B: Pattern Extraction & Rule Generation

- Analyze annotated corpus to find:
  - Most frequent knowledge points
  - Common knowledge point combinations
  - Difficulty progression across years
  - Most common cognitive angles per topic
- Generate question generation rules: structured templates defining valid, intent-driven questions per knowledge point

#### 1C: Knowledge Point Content Layer

For each atomic knowledge point, generate or curate:
- Concise concept explanation (LLM-assisted)
- 2–3 worked examples at increasing difficulty
- Common misconceptions / traps
- Connection map to related knowledge points

---

### Phase 2 — MVP: Core Student-Facing Product

**Nature:** Full-stack engineering. First user-facing release.

**Goal:** A working product students can use that generates real interaction data.

#### 2A: Knowledge Map UI

- Visual or list-based representation of the knowledge dependency graph
- Students see: covered, unlocked, not-yet-reached concepts
- Each node links to: learning material, practice questions, performance data

#### 2B: Single-Concept Practice Mode (Training Mode)

- Student selects a knowledge point
- System generates or retrieves targeted questions
- **Key feature:** Student must explain reasoning in text before submitting
- System captures: answer, time taken, reasoning text
- Post-submission: worked solution + 1–2 probing follow-up questions

#### 2C: Combined Concept Tests

- Unlocked after individual concept practice
- Uses Phase 1B combination patterns to construct multi-concept problems mirroring real AMC 8
- Test mode: answer submission only
- Post-test: AI probing for wrong answers (find misconception) and right answers (verify genuine understanding vs. lucky guess)

#### 2D: Basic Diagnostic Dashboard

- Per-student view:
  - Mastery level per knowledge point (practice + reasoning quality)
  - Weak areas flagged
  - Recommended next steps

---

### Phase 3 — Diagnosis Engine (Iterative, Research-Driven)

**Nature:** Research phase. Cannot be properly designed in advance — requires real student data from Phase 2.

**Goal:** Build adaptive diagnosis and learning path optimization.

#### 3A: Real Interaction Data Collection

- Gather student reasoning texts, probing Q&A conversations, error patterns from Phase 2
- Let patterns emerge from real data — do not build on assumptions

#### 3B: Iterative Diagnostic Model Development

- Analyze follow-up question effectiveness (revealed misconceptions vs. failed)
- Identify which question angles produce most diagnostic signal
- Build reasoning analysis model mapping explanations to conceptual gaps
- Refine probing question library based on what works

#### 3C: Adaptive Learning Path

- Dynamically reorder student learning paths based on diagnostic output
- Surface specific remediation content for identified gaps
- Connect findings back to the knowledge dependency graph

---

## Architecture Notes

### Data Model Core Entities (Preliminary)

- **KnowledgePoint** — atomic concept with dependencies
- **Question** — problem text, choices, answer, source metadata
- **QuestionAnnotation** — Phase 0C schema fields linked to a question
- **Student** — user profile and auth
- **PracticeSession** — student attempt: answer, time, reasoning text
- **DiagnosticResult** — per-student per-knowledge-point mastery assessment

### Key Technical Considerations

- **Knowledge graph as relational data:** PostgreSQL with adjacency list or materialized path for the dependency graph. No need for a separate graph DB at this scale.
- **LLM cost management:** Cache annotation results. Batch process where possible. Consider lighter models for routine probing questions. Agent SDK for complex diagnostic workflows only.
- **Data integrity:** Question corpus is the foundation — invest in validation, deduplication, and versioning early.

### CRITICAL ISSUE: LLM Pipeline Infrastructure

> **Severity: HIGH — Unresolved architectural blocker**
>
> The LLM pipeline (batch annotation in Phase 1A, real-time tutoring/probing in Phase 2B/2C, diagnostic analysis in Phase 3) conflicts with Vercel's serverless model:
> - **Serverless timeout limits** (10s hobby / 60s pro) are insufficient for multi-step agentic LLM workflows
> - **Claude Agent SDK** requires long-running processes that don't fit request/response serverless functions
> - **Cost at scale** — per-student agentic calls could be prohibitively expensive
> - **Concurrency** — many students triggering agent workflows simultaneously
>
> **This must be resolved after Phase 0 research is complete, before Phase 1 engineering begins.** Possible directions: external worker service, edge functions, dedicated compute, queue-based async processing, or hybrid architecture. Decision deferred until research clarifies actual pipeline requirements.

---

## Open Decisions (To Be Resolved)

- [ ] **LLM pipeline architecture (CRITICAL)** — how to run long-running agentic LLM workflows given Vercel's serverless constraints. See "Critical Issue" above.
- [ ] ORM choice: Prisma vs. Drizzle
- [ ] Auth provider: NextAuth vs. Clerk vs. other
- [ ] UI component library / styling approach
- [ ] Graph visualization library for knowledge map
- [ ] Student data privacy / COPPA compliance (AMC 8 targets students age 10–14)

---

## Immediate Next Actions

1. **Phase 0A:** Obtain official AMC 8 topic outline from MAA
2. **Phase 0B:** Collect past papers (2000–present)
3. **Phase 0C:** Draft and validate annotation schema on 5–30 questions
4. **Phase 0A:** Sketch knowledge dependency graph

*Phase 0 is executed by the user with the Coder agent. Brainstormer agent will decompose Phases 1–3 into epics once Phase 0 research is sufficiently complete.*
