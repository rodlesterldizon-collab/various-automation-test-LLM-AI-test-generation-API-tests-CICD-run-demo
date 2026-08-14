# AI-Augmented Playwright Automation Portfolio Showcase

[![CI Status](https://img.shields.io/github/actions/workflow/status/rodlesterldizon-collab/automation-test/playwright.yml?style=for-the-badge&logo=github-actions&label=CI%20Status)](https://github.com/rodlesterldizon-collab/automation-test/actions/workflows/playwright.yml)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Playwright](https://img.shields.io/badge/Playwright-45BA4B?style=for-the-badge&logo=playwright&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2671E5?style=for-the-badge&logo=githubactions&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![JMeter](https://img.shields.io/badge/JMeter-D22128?style=for-the-badge&logo=apachejmeter&logoColor=white)

> **Built by Rod Lester Dizon**
>
> This repository is a **portfolio and feature showcase** designed to highlight various test automation capabilities, architectural patterns, and AI-assisted engineering workflows. Rather than functioning as a live, production-deployed infrastructure, it serves as a comprehensive reference portfolio demonstrating multi-layer test automation across UI, API, LLM evaluation, Accessibility, and Performance testing. Public demo targets (SauceDemo, ReqRes, Deque) are used intentionally to provide a stable, observable environment for showcasing these feature implementations.

---

## 📑 Table of Contents

- [📐 Portfolio Scope & Known Trade-offs](#-portfolio-scope--known-trade-offs)
- [🗂️ Test Map](#️-test-map)
- [💼 Engineering Value & ROI](#-engineering-value--roi)
- [🛠️ Tech Stack](#️-tech-stack)
- [🏗️ Architecture & Design Patterns](#️-architecture--design-patterns)
- [🧪 Test Coverage](#-test-coverage)
  - [UI Tests — SauceDemo](#ui-tests--saucedemo)
  - [API Tests — ReqRes](#api-tests--reqres)
  - [LLM Evaluation — Promptfoo + Groq](#llm-evaluation--promptfoo--groq)
  - [Accessibility Audits — Axe-core + Lighthouse](#accessibility-audits--axe-core--lighthouse)
  - [Performance & Load Testing — JMeter via Taurus](#performance--load-testing--jmeter-via-taurus)
- [🚀 CI/CD Pipeline](#-cicd-pipeline)
- [🤖 AI-Augmented Engineering Workflow](#-ai-augmented-engineering-workflow)
- [⚡ Quick Start](#-quick-start)


---

## 📐 Portfolio Scope & Known Trade-offs

This section documents the deliberate engineering trade-offs made within the constraints of a zero-budget, personal free-tier environment. A senior engineer is accountable for the decisions they make — including knowing what they would do differently with proper infrastructure.

| Area | Decision Made in This Portfolio | What a Production Implementation Would Change |
| :--- | :--- | :--- |
| **API test depth** | Broad scenario coverage (50 tests); assertion depth limited to avoid triggering shared ReqRes rate limits | Deep schema/contract validation (OpenAPI/Pact), idempotency probes, auth-boundary checks — requires a private staging API with dedicated throughput |
| **Rate-limit handling** | `HTTP 429` responses handled gracefully to prevent public CI pipeline breakage | 429s classified as infrastructure SLA failures; separate pipeline telemetry tracks throttle events distinct from product failures |
| **Quality gates** | Accessibility and performance jobs run as non-blocking telemetry (`continue-on-error: true`) | Blocking severity policy for `Critical`/`Serious` WCAG violations; lightweight performance smoke gate with explicit SLA threshold enforcement |
| **LLM evaluation breadth** | 3-prompt golden dataset; constrained by free-tier token limits per CI run | Domain-specific dataset with paraphrase/adversarial variation, deterministic checks, evaluator agreement metrics, dedicated API quota |
| **Workflow permissions** | Reduced to `contents: read` (least-privilege) after initial review | Job-level permission scoping per step, OIDC token federation for artifact publishing instead of PAT |
| **Secret handling** | Hardcoded fallback key removed; secrets injected via GitHub Actions only; `.env.example` documents all required keys | Vault-backed secret rotation, secret scanning pre-commit hooks (e.g., `detect-secrets`), environment-scoped secret access |
| **CI pipeline scope** | Single workflow file covers all jobs; `workflow_dispatch` toggles allow selective suite execution | Matrix-based environment promotion: dev → staging → production gates with separate approval workflows |

> [!IMPORTANT]
> These are not gaps from lack of knowledge — they are deliberate scope decisions driven by infrastructure budget. The architecture patterns, vocabulary, and upgrade paths documented above reflect how each area would be implemented in a funded enterprise environment.

---

## 🗂️ Test Map

A quick orientation of the `tests/` directory and what lives where:

```
tests/
├── accessibility/          # Accessibility audits — Axe-core WCAG scans and Lighthouse
│                           #   lab audits across mobile & desktop viewports
│
├── all-perf/               # Performance & load testing — Apache JMeter test plans
│                           #   and Taurus (bzt) YAML configs executed here
│
├── api-llm/                # LLM evaluation suite — runs a curated golden dataset
│                           #   against an LLM to verify response accuracy and
│                           #   groundedness (i.e., the model's answers stay
│                           #   factually anchored to expected ground-truth data).
│                           #   Originally built for Google Gemini; switched to
│                           #   Groq (llama-3.1-8b-instant) to avoid free-tier
│                           #   rate-limit constraints
│
├── api/                    # API tests — REST endpoint validation against the
│                           #   ReqRes mock API (GET, POST, PUT, PATCH, DELETE)
│
├── pages/                  # Page Object Model (POM) — page classes generated
│                           #   via the Playwright AI Planner, Generator, and
│                           #   Healer agents for the SauceDemo login & checkout
│                           #   demo flows
│
├── login.spec.ts           # Login spec — authentication tests tied to the POM
│                           #   page objects (multi-persona login matrix)
├── checkout-flow.spec.ts   # Checkout spec — end-to-end checkout flow for the
│                           #   SauceDemo ecommerce demo (cart, shipping, totals)
└── seed.spec.ts            # Seed spec — lightweight smoke / sanity check
```

---

## 💼 Engineering Value & ROI

This framework is built around four engineering ROI pillars that directly map to what engineering orgs care about:

| Pillar | What Was Built | Business Impact |
| :--- | :--- | :--- |
| **Minimize Flakiness** | Defense-in-Depth selector hierarchy + self-healing locators | Reduces selector-driven test breakage across UI flows |
| **Accelerate Pipelines** | Dockerized Playwright runners on GitHub Actions | ~1-minute end-to-end build time with containerized environment parity |
| **Validate AI Features** | Programmatic LLM evaluation with golden dataset + LLM-as-judge grading | Catches model regressions before they reach production |
| **Performance Monitoring** | JMeter via Taurus non-blocking telemetry | Captures SLA breaches and concurrency limits as telemetry metrics |

---

## 🛠️ Tech Stack

| Layer | Tool | Purpose |
| :--- | :--- | :--- |
| **Test Execution** | Playwright (TypeScript) | Cross-browser UI automation (Chromium, Firefox, WebKit) |
| **API Testing** | Playwright API project | REST endpoint validation with strong TypeScript typing |
| **LLM Evaluation** | Promptfoo + Groq SDK | Programmatic accuracy and groundedness testing for AI models |
| **Accessibility** | Axe-core + Playwright Lighthouse | WCAG violation detection across mobile & desktop viewports |
| **Performance** | Apache JMeter + Taurus (bzt) | Load testing with concurrency matrix and non-blocking telemetry reporting |
| **Architecture** | Page Object Model (POM) | Strict separation of UI interactions from test orchestration |
| **CI/CD** | GitHub Actions + Docker | Containerized, environment-parity pipeline with job dependency management |
| **Reporting** | Playwright HTML Reports + GitHub Job Summary | Trace Viewer, screenshots, and stakeholder-readable dashboards |

---

## 🏗️ Architecture & Design Patterns

### Page Object Model (POM)

Strict POM enforcement ensures a clean separation of concerns: page objects own UI interactions, spec files own orchestration and assertions. This standard is audited programmatically by the Architecture Agent during the code generation phase.

```
tests/
├── pages/                    # Page objects only — no test logic
│   ├── base.page.ts          # Base page class with shared methods
│   ├── login.page.ts
│   ├── inventory.page.ts
│   └── checkout-*.page.ts    # Specialized per-step pages
├── common/
│   └── component/            # Reusable UI component objects
│       └── navigation-bar.ts
├── helpers/                  # Shared utilities & config
│   └── utils.ts
└── *.spec.ts                 # Test implementations — tests/ root only

specs/                        # Human-readable test specifications (Markdown)
├── login.md
├── checkout-flow.md
└── README.md
```

**Architecture Rules:**

| ✅ DO | ❌ DON'T |
| :--- | :--- |
| Page helpers in `tests/pages/*.ts` | Put test logic inside page objects |
| Tests in `tests/*.spec.ts` | Create spec files in subdirectories |
| Utilities in `tests/helpers/` | Duplicate helper logic across specs |

### Defense-in-Depth Selector Strategy

The framework enforces a resilient locator hierarchy to survive UI changes without manual intervention:

1. **Semantic Locators** — `getByRole`, `getByLabel`, `getByText` (accessibility-first, most resilient)
2. **Engineering Identifiers** — `data-testid` (stable, team-controlled fallback)
3. **Structural Fallbacks** — CSS classes as the final layer, continuously hardened by the Healer Agent

### Visual Regression Detection

A custom `compareVisuals` utility programmatically detects UI discrepancies — such as all product images rendering as the same source — by comparing image buffers and DOM `src` attributes. This catches visual bugs that assertion-only tests miss entirely.

### Architectural Decision Records (ADRs)

The following decisions were made deliberately rather than by default:

| Decision | Rationale | Acknowledged Trade-off |
| :--- | :--- | :--- |
| **Spec-first workflow** (Markdown specs before code) | Forces explicit coverage intent; specs are reviewable artifacts independent of implementation | Adds an authoring step; only valuable if specs are maintained alongside code |
| **POM with strict file-location enforcement** | Prevents test logic from leaking into page objects over time; enables AI agent auditing | More boilerplate per new page; requires contributor discipline |
| **Semantic locators as first-tier** | Aligns selectors with WCAG accessibility tree — locators that break on semantic changes signal real accessibility regressions | Requires the application under test to have meaningful ARIA roles; less applicable to legacy DOM-heavy apps |
| **Playwright for API testing** (not a dedicated API framework) | Single toolchain; typed TypeScript interfaces reused across UI and API layers; unified reporting | Less ergonomic than Supertest or RestAssured for contract-level validation; justified here by team toolchain consolidation |

---

## 🧪 Test Coverage

> [!NOTE]
> This project focuses on high-impact scenarios to demonstrate architectural patterns rather than 100% feature coverage of the demo targets. The patterns demonstrated here transfer directly to enterprise-scale systems.

### UI Tests — SauceDemo

| Scenario | What It Validates |
| :--- | :--- |
| **Multi-Persona Authentication** | Login state across `standard`, `problem`, `performance_glitch`, and `error` user personas |
| **Visual Regression Matrix** | Detects identical product images across personas using buffer comparison |
| **Stateful Checkout Funnel** | Cart state management, shipping data entry, financial total & tax verification |
| **SVG Icon & Error Validation** | Field-level error messages and icon presence for failed authentication flows |

| **Authentication State** | **Inventory Management** | **Stateful Checkout** |
| :---: | :---: | :---: |
| ![Login](screenshots/login.png) | ![Inventory](screenshots/inventory.png) | ![Cart](screenshots/cart.png) |
| Multi-persona login state matrix | Grid state & visual discrepancy detection | Financial total integrity through funnel |

### API Tests — ReqRes

Located in `tests/api/` — 50 API tests demonstrating robust backend validation patterns:

- **Full REST Coverage:** GET, POST, PUT, PATCH, DELETE operations with strong TypeScript interfaces
- **Security & Auth Injection:** Dynamic `x-api-key` injection via Playwright config and GitHub Secrets
- **Edge-Case Coverage:** Malformed payloads, non-existent resources, negative pagination values, extreme string lengths, unauthenticated endpoints

> [!NOTE]
> **Free-Tier Sandbox Constraints — Breadth vs. Depth Trade-off**
>
> The API suite intentionally favors **breadth of scenario coverage over deep assertion chains per test**. ReqRes is a shared, rate-limited public mock service running on a free tier — not a dedicated staging environment. Chaining multiple heavy assertions per request (schema validation, multi-step contract checks, idempotency probes, sequential consistency reads) multiplies the request count per CI run significantly, which reliably triggers `HTTP 429` rate limits on shared public endpoints, breaking the public pipeline for anyone reviewing this repository.
>
> Some tests are also explicitly scoped or skipped where the ReqRes mock returns behavior that is tightly coupled to the demo service's own implementation (e.g., exact empty JSON bodies, fixed field sets) — which would generate false failures on any real service. These are annotated in the test source.
>
> **What the suite demonstrates mechanically:**
> - REST verb coverage (GET, POST, PUT, PATCH, DELETE) with TypeScript-typed request/response interfaces
> - Secret injection via GitHub Actions and Playwright config — no credentials in source
> - Edge-case inputs: malformed payloads, non-existent resources, negative pagination, unauthenticated access
>
> **What enterprise API validation would additionally require with a dedicated environment:**
> - Full JSON schema validation against a versioned contract (e.g., OpenAPI / Pact consumer-driven contracts)
> - Authorization boundary testing: role-based access, token expiry, scope enforcement
> - Idempotency verification for POST/PUT/PATCH operations
> - Data-integrity and consistency checks across sequential reads after write
> - Response time SLA assertions under defined load conditions
> - A private staging environment with sufficient throughput headroom to run the full battery without rate-limit interference


### LLM Evaluation — Promptfoo + Groq

Located in `tests/api-llm/` — programmatic evaluation of Large Language Models using Promptfoo.

**Why this matters:** AI features fail silently. Accuracy regressions don't throw exceptions — they return plausible-sounding wrong answers. This suite demonstrates the mechanics of automated LLM evaluation using a structured, reproducible pipeline.

**How it works:**
- **Golden Dataset** (`tests/api-llm/golden-dataset.csv`): A curated set of prompts with expected answers used as the ground-truth rubric
- **LLM-as-Judge Grading:** The model grades its own outputs against the golden dataset, creating a fully automated quality gate
- **Custom Provider Adapter** (`tests/api-llm/groqProvider.js`): A hand-engineered JavaScript adapter that bridges Groq's SDK to Promptfoo's evaluation loop — proving the framework's extensibility for unsupported third-party APIs

**Provider Strategy:** Groq (`llama-3.1-8b-instant`) is the default due to its generous free-tier rate limits, which are critical for avoiding `429` errors during CI/CD bulk evaluations. Google Gemini remains in the codebase as a documented fallback.

```bash
# Run LLM evaluation
npm run test:llm:groq

# Setup: copy .env.example to .env and fill in your GROQ_API_KEY
cp .env.example .env
```

**Evaluation Output:**

![Promptfoo Golden Dataset Evaluation](screenshots/goldendataset-promptfoo.png)

> [!NOTE]
> **Scope of This Showcase: Mechanics vs. Production-Scale Coverage**
>
> The golden dataset intentionally contains a small number of prompts (arithmetic, translation, and security-sensitive refusal behavior). **This is a deliberate constraint of the free-tier API environment**, not a design limitation of the framework itself.
>
> Each Promptfoo evaluation run makes multiple LLM API calls — one for each prompt, plus one for the LLM-as-judge grader. At free-tier token-per-minute and request-per-day limits (Groq, Gemini), expanding the dataset to include paraphrase variation, adversarial inputs, hallucination cases, domain-specific facts, or multi-turn scenarios would reliably hit rate limits during every CI/CD run and break the public pipeline.
>
> **What this suite proves mechanically:**
> - Custom provider adapters bridge unsupported third-party LLM SDKs into Promptfoo's evaluation loop
> - LLM-as-judge grading operates programmatically against expected ground-truth outputs
> - The evaluation pipeline integrates cleanly into GitHub Actions CI/CD with secret injection
>
> **What production-scale LLM evaluation would additionally require:**
> - A significantly larger, domain-specific golden dataset with paraphrase and adversarial variation
> - Deterministic expected-output checks for factual/structural prompts (not solely LLM-judged)
> - Evaluator agreement analysis to measure consistency across judge runs and reduce correlated errors from LLM-as-judge drift
> - Hallucination detection cases and temperature sensitivity analysis
> - Historical defect-based pass/fail thresholds rather than a fixed 80% accuracy target
> - Dedicated API quota (paid tier or internally hosted model) to run the full evaluation battery reliably in CI

### Accessibility Audits — Axe-core + Lighthouse

Located in `tests/accessibility/` — a dynamic, multi-engine accessibility scanning module.

**Engineering decisions:**
- **URL-Driven Orchestration:** Takes a `URL_LIST` environment variable instead of hardcoded URLs, generating parallel test suites on the fly for any target
- **Non-Blocking Telemetry:** Violations surface as reports rather than blocking public CI jobs (`continue-on-error: true`), maintaining visibility into accessibility debt without failing public demo runs
- **Dual-Engine Coverage:** Axe-core catches strict WCAG violations; Lighthouse provides complete lab audits across Mobile and Desktop viewports

> [!NOTE]
> **Reporting vs. Release Gates:** In a production deployment pipeline, release policy separates reporting from blocking gates. The severity classification used in this portfolio's documented upgrade path:
>
> | Severity | Axe-core Impact Level | Gate Behavior |
> | :--- | :--- | :--- |
> | Critical | `critical` | Blocking — fails PR immediately |
> | Serious | `serious` | Blocking — fails PR immediately |
> | Moderate | `moderate` | Non-blocking — logged to issue tracker |
> | Minor | `minor` | Non-blocking — reported only |
>
> This portfolio currently runs all findings as non-blocking reports due to public sandbox constraints.

**CI Signal Design — Warning Annotations**

GitHub Actions has three final job states: **Success** (green), **Failure** (red), and **Cancelled** (grey). There is no native "warning" final state.

The accessibility job uses the best available pattern to communicate findings without false-failing the pipeline:

- The scan step runs with `continue-on-error: true` — the job **stays green** even when violations are detected
- A post-scan step checks the outcome and, if violations were found, emits a `::warning::` annotation — visible as a **yellow triangle** in the GitHub Actions run annotations panel
- The GitHub Actions **Job Summary** renders a `⚠️ Accessibility Scan — Completed with Warnings` table with the severity policy and a link to download the full Axe-core and Lighthouse artifact

This design is deliberate: blocking the entire pipeline on WCAG violations from a **third-party public demo URL** (`dequelabs.com`) would create noise, not signal. In a production pipeline, a separate **blocking PR job** would run against the application under test and fail only on new `Critical` or `Serious` violations introduced by the changeset — not pre-existing violations on external demo sites.

| **Axe-Core Custom Dashboard** | **Lighthouse Mobile/Desktop Audit** |
| :---: | :---: |
| ![Axe Core Dashboard](screenshots/axe-core.png) | ![Lighthouse Audit](screenshots/lighthouse.png) |

```bash
npm run test:a11y https://broken-workshop.dequelabs.com/
```

For detailed module documentation, see [Accessibility README](tests/accessibility/Accessibility-README.md).

### Performance & Load Testing — JMeter via Taurus

Located in `tests/all-perf/` — a JMeter load-testing suite orchestrated by Taurus operating in a **non-blocking telemetry mode**.

#### Telemetry & Baseline Monitoring Strategy

In this showcase environment, performance tests are configured with `continue-on-error: true` in CI/CD. This runs the load tests as non-blocking telemetry jobs to collect performance metrics (latency distributions, error rates, throughput limits) without breaking public demo workflows when running against shared third-party sandboxes.

During baseline 20-user stress tests, the suite captured two real environment limits:
1. The mock API rate-limited traffic at ~10 RPS, triggering a cascade of `429 Too Many Requests` responses
2. Latency spiked well above the 500ms SLA threshold

Both were logged, analyzed, and rendered in the job summary.

> [!NOTE]
> **Measurement vs. Release Policy:** Production CI/CD architectures separate non-blocking load testing from blocking release gates. Lightweight performance smoke tests (e.g., tight SLA latency gates under 5 users) enforce explicit build-failing thresholds, while high-concurrency stress suites run asynchronously for telemetry and capacity planning.

#### Stats Reference

The screenshot below is a real `stats.csv` output captured from a CI/CD run. Use it as a baseline when interpreting future results:

![Performance Stats](screenshots/stats.png)

#### CI/CD Reporting

When the pipeline runs, the framework automatically:
1. **Parses `stats.csv`** and renders a Markdown table of Users / Avg Latency / Error Rate directly on the GitHub Actions Job Summary — stakeholders see results without downloading anything
2. **Uploads raw artifacts** (`kpi.jtl`, HTML dashboards) as downloadable zip files at the bottom of each Actions run

---

## 🚀 CI/CD Pipeline

The pipeline achieves **~1-minute end-to-end execution** with multi-job orchestration and advanced pipeline controls.

- **Job Dependency Management:** `needs: [api-tests]` ensures API tests pass before UI tests begin — explicit serial execution with no wasted compute
- **Workflow Dispatch Toggles:** Engineers can selectively trigger `api`, `ui`, `llm`, or `all` suites from the GitHub UI, bypassing redundant runs
- **Containerized Execution:** Microsoft's `playwright:jammy` image provides pre-packaged OS dependencies and system runtime parity across CI jobs
- **Environment Parity:** Containerized execution ensures identical contexts across local dev, staging, and CI — no "works on my machine" failures
- **Concurrency Matrix:** Performance tests run against a `[20, 50]` user matrix in parallel, producing separate artifact sets per tier

![CI/CD Pipeline](screenshots/cicd.png)

> The CI badge at the top of this README is live — click it to see the latest pipeline run.

### 🔒 Security Practices

Security hygiene applied in this pipeline:

| Practice | Implementation |
| :--- | :--- |
| **Least-privilege permissions** | Workflow declares `permissions: contents: read` only — no write access granted to jobs that only read, test, and upload artifacts |
| **No credentials in source** | Hardcoded fallback API keys removed from `playwright.config.ts`; config fails explicitly if `REQRES_API_KEY` is not injected |
| **Secret injection via GitHub Secrets** | All API keys (`REQRES_API_KEY`, `GROQ_API_KEY`, `GEMINI_API_KEY`) injected at the job level from repository secrets — never in source or logs |
| **`.env.example` pattern** | Documents required keys with placeholder values and acquisition links; `.env` is gitignored |

### 🔀 Quality Gate Upgrade Path

The current pipeline uses non-blocking telemetry for accessibility and performance. The documented upgrade path to production-grade release gates:

| Gate | Current (Portfolio) | Production Upgrade |
| :--- | :--- | :--- |
| **Accessibility** | `continue-on-error: true`; all findings are reports | Separate blocking job: fails PR on any new `Critical` or `Serious` violation; `Minor` findings route to issue tracker |
| **Performance smoke** | Non-blocking concurrency matrix | Add a lightweight smoke job (5 users, <200ms p95 SLA) that runs blocking before merge; stress matrix runs non-blocking post-merge |
| **API contract** | Status/field-level assertions | Add Pact consumer contract job that fails if provider response schema breaks a registered consumer expectation |

---

## 🤖 AI-Augmented Engineering Workflow

This framework was built using a **multi-stage Agentic Workflow powered by Model Context Protocol (MCP)**. This is not AI-assisted copy-pasting — it is a structured engineering lifecycle where human oversight governs every stage.

### The Spec-to-Code Lifecycle

| Stage | Agent | What It Did |
| :--- | :--- | :--- |
| **1. Spec Discovery** | Planner MCP | Scanned SauceDemo and autonomously generated `specs/login.md` and `specs/checkout-flow.md` |
| **2. Scaffolding** | Generator MCP | Transformed markdown specs into initial TypeScript test implementations and Page Objects |
| **3. Human Review** | *(Senior SDET oversight)* | Manual correction of logic errors, edge cases, and spec gaps the generator missed |
| **4. POM Enforcement** | Architecture Agent | Audited and refactored the codebase to enforce strict Page Object Model separation |
| **5. Selector Hardening** | Healer MCP | Refactored selectors to the resilient locator hierarchy; established the re-engagement loop for future CI failures |

### Triggering the Agents

```text
@playwright-test-planner      "Create a test plan for the inventory sorting flow"
@playwright-test-generator    "Implement the tests defined in specs/inventory.md"
@playwright-test-architecture "Check if my new page object follows the POM rules"
```

### Self-Healing CI

The **Playwright Test Healer** is designed to close the failure → fix loop automatically:

1. A test fails in CI (e.g. a selector broke due to a UI change)
2. The Healer agent analyzes the failure logs and DOM snapshot
3. The agent generates a corrected selector, re-runs the test, and **auto-commits the fix**

> [!TIP]
> **To enable:** Uncomment the "Install Copilot CLI" and "Run Playwright Test Healer" steps in `.github/workflows/playwright.yml`. Requires a `COPILOT_PAT` repository secret.

---

## ⚡ Quick Start

### Prerequisites
- [Node.js](https://nodejs.org/) v20+
- npm
- Python 3 + `bzt` (Taurus) for performance tests only

### Installation

```bash
npm install
```

### Running Tests

```bash
# UI tests (all browsers)
npx playwright test

# UI tests (Chromium only)
npm run test:cli

# API tests
npm run test:api

# LLM evaluation
npm run test:llm:groq

# Accessibility scan (pass any URL)
npm run test:a11y https://your-target-url.com

# Performance test (default: 20 concurrent users)
npm run test:perf
```

### Environment Variables

Copy `.env.example` to `.env` and fill in your own values:

```bash
cp .env.example .env
```

All keys are injected as GitHub Actions Secrets in CI/CD — see `.env.example` for the full list and where to obtain each key.

---

*Created and maintained by **Rod Lester Dizon**.*
*Open to Senior SDET / Staff Engineer / QA Architect roles — [connect on LinkedIn](https://www.linkedin.com/in/rodlesterdizon/) or reach out via GitHub.*
