# Project TEAP — Tender Evaluation & Audit Portal

> **Classification:** RESTRICTED — Government of India, Ministry of Home Affairs  
> **Directorate:** Central Armed Police Forces (CAPF)  
> **System Version:** TEAP v2.4.1  
> **Node:** NIC Data Centre, New Delhi  
> **Compliance:** ISO 27001 | MEITY Empanelled | GFR 2017 | DPIIT Order 2017

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Screenshots & Features](#2-screenshots--features)
3. [Architecture](#3-architecture)
4. [Module Reference](#4-module-reference)
   - [Module 1 — Secure Document Ingestion](#module-1--secure-document-ingestion)
   - [Module 2 — Multi-Agent Console](#module-2--multi-agent-console)
   - [Module 3 — FairCheck™ Fairness Scorecard](#module-3--faircheck-fairness-scorecard)
   - [Module 4 — Deterministic Fairness Receipt](#module-4--deterministic-fairness-receipt)
   - [Module 5 — DPIIT / Make in India Tracker](#module-5--dpiit--make-in-india-tracker)
5. [Technical Stack](#5-technical-stack)
6. [File Structure](#6-file-structure)
7. [Design System](#7-design-system)
8. [Regulatory & Compliance References](#8-regulatory--compliance-references)
9. [Bidder Dataset (Demo)](#9-bidder-dataset-demo)
10. [Deployment Guide](#10-deployment-guide)
11. [Running Locally](#11-running-locally)
12. [Browser Compatibility](#12-browser-compatibility)
13. [Known Limitations & Disclaimer](#13-known-limitations--disclaimer)
14. [Roadmap](#14-roadmap)
15. [Contributing](#15-contributing)
16. [License](#16-license)

---

## 1. Project Overview

**Project TEAP (Tender Evaluation & Audit Portal)** is a prototype single-page government web application designed for the **Central Armed Police Forces (CAPF) Directorate under the Ministry of Home Affairs, Government of India**. It demonstrates an AI-assisted, bias-aware, and fully auditable procurement evaluation framework for defence and security tenders.

### Problem Statement

Government tender evaluation processes are often:
- Opaque and prone to subjective scoring
- Susceptible to unconscious bias against MSMEs, women-led firms, and smaller vendors
- Lacking deterministic, auditable records of *why* a score was assigned
- Non-compliant with DPIIT's Make in India preferential purchase policy by default
- Dependent on manual cross-referencing of GFR clauses and RFP conditions

### What TEAP Solves

TEAP introduces a **five-module evaluation pipeline** that automates document ingestion, multi-agent scoring, fairness analysis, regulatory citation, and compliance tracking — all within a single, zero-dependency HTML file that can be deployed instantly on GitHub Pages or any static web server.

---

## 2. Screenshots & Features

### Header & Ticker Strip
- Live scrolling ticker with tender reference, session timer, and security classification
- National Emblem placeholder (golden circular badge with star-of-life icon)
- Government of India branding: bilingual (भारत सरकार / Government of India), Ministry and Directorate details
- Top navigation with tabs: Dashboard, Ingestion, Agents, Fairness, Receipts, DPIIT
- Logged-in officer display: `Sanjay Kumar, IPS`

### Status Bar (Live KPI Cards)
Five cards update dynamically as the portal is used:

| Card | Initial State | Live Update |
|---|---|---|
| Tender ID | CAPF/MHA/2026/T-0091 | Static |
| Bidders Received | 0 | Increments on file upload |
| PII Scanned | Awaiting Upload | Updates after scan |
| Evaluation Deadline | 15-MAY-2026 | Static |
| GFR Compliance | Pending | → Compliant after scan |

---

## 3. Architecture

```
index.html (Single File)
│
├── HEAD
│   ├── Tailwind CSS (CDN)
│   ├── Google Fonts: EB Garamond + Source Sans 3
│   ├── Font Awesome 6.5 (CDN)
│   └── Chart.js (CDN)
│
├── BODY
│   ├── Ticker Strip
│   ├── Government Header
│   │   ├── National Emblem
│   │   ├── Ministry / Directorate Branding
│   │   └── Sub-Navigation Bar
│   ├── Status KPI Bar (5 cards)
│   ├── Main Grid (2 columns)
│   │   ├── LEFT COLUMN
│   │   │   ├── Module 1: Secure Ingestion
│   │   │   ├── Module 3: Fairness Scorecard + Chart
│   │   │   └── Module 4: Receipt Generator
│   │   └── RIGHT SIDEBAR
│   │       ├── Module 2: Multi-Agent Console
│   │       └── Module 5: DPIIT Tracker
│   ├── Footer (NIC / ISO / MEITY)
│   └── Receipt Modal (hidden, shown on demand)
│
└── SCRIPT (Vanilla JS)
    ├── Tab switching
    ├── Drag-and-drop + file processing
    ├── PII scan animation engine
    ├── File progress bar engine
    ├── Agent log writer + sequencer
    ├── Chart.js fairness chart
    ├── Bias mitigation toggle
    ├── Receipt data store
    └── Receipt modal generator
```

---

## 4. Module Reference

---

### Module 1 — Secure Document Ingestion

**Location:** Left column, top card  
**Purpose:** Simulates a classified, AES-256-encrypted document upload channel for receiving bidder submissions.

#### Features
- **Drag-and-drop zone** — Accepts files by drag-and-drop or click-to-browse
- **Accepted formats:** PDF, DOCX, XLSX (enforced via `accept` attribute)
- **Max file size label:** 50 MB per file
- **Per-file progress bars** — Each uploaded file displays an animated progress bar that fills from 0% → 100%, then shows a `✓` checkmark
- **PII Scan Animation** — After upload begins, a gradient scan bar animates across the screen (CSS keyframe `scan-anim`) with a live percentage counter (0% → 100%)
- **KPI updates** — On completion: "PII Scanned" card updates to `5 Docs Scanned — 0 PII Detected`; "GFR Compliance" card updates to `Compliant`
- **Agent log integration** — Upload triggers three automatic log entries in the Agent Console (Technical, Financial, Compliance agents)

#### How It Works (Code)
```javascript
function processFiles(files) { ... }
// 1. Renders file items with animated progress bars
// 2. Starts PII scan bar animation via setInterval
// 3. Updates KPI cards on completion
// 4. Fires 3 agent log entries with staggered setTimeout delays
```

---

### Module 2 — Multi-Agent Console

**Location:** Right sidebar, top card  
**Purpose:** Simulates a real-time multi-agent AI orchestration system processing the uploaded tender documents.

#### Agents

| Agent | Colour Code | Responsibilities |
|---|---|---|
| **TECH** (Technical Agent) | Blue `#3b82f6` | Parses BOM tables, validates CEMILAC certs, NSIC certs, computes technical weighted scores |
| **FIN** (Financial Agent) | Green `#10b981` | Extracts L1 prices, applies currency normalisation (USD/INR), applies DPIIT 20% loading, identifies L1 bidder |
| **COMP** (Compliance Agent) | Amber `#f59e0b` | GFR Rule 160 check, EMD/BG verification, DPIIT affidavit validation, CVC blacklist registry check |

#### Status Indicators
- Each agent has a **pulsing dot** (`agent-dot.active` CSS animation) that activates while running
- Status text transitions: `Idle` → `Running` → `Done`

#### Log Console
- Dark terminal UI (background `#0d1117`, text `#c9d1d9`) — styled after GitHub's dark theme
- Each log entry has a **colour-coded left border** (blue/green/amber/grey) and a timestamp
- Auto-scrolls to the latest entry
- 6 scripted log lines per agent, fired with `700ms` intervals

#### Button
- **Run All Agents** — Triggers all three agent scripts in parallel with staggered starts. Prevents re-entry while running (`agentRunning` flag).

---

### Module 3 — FairCheck™ Fairness Scorecard

**Location:** Left column, middle card  
**Purpose:** Analyses technical scores for disparate impact across vendor categories and provides a one-click bias mitigation action.

#### Disparate Impact Ratio (DIR) Table

| Bidder | Category | Original DIR | Status |
|---|---|---|---|
| Bharat Systems Ltd. | Large / Public | 0.92 | ✅ PASS |
| Innovate MSME Co. | MSME / Startup | 0.71 | ⚠️ REVIEW |
| DefenceTech Pvt. | Private / Mid | 0.85 | ✅ PASS |
| StarShield Infra | Foreign JV | 0.58 | ❌ FAIL |
| NovaSec Dynamics | MSME / Women-led | 0.78 | ⚠️ REVIEW |

**DIR Threshold Key:**
- `≥ 0.80` → PASS (green badge)
- `0.70 – 0.79` → REVIEW (amber badge)
- `< 0.70` → FAIL (red badge)

#### Bar Chart (Chart.js)
- Displays Technical Score Distribution for all 5 bidders
- Original scores: `[87.4, 81.2, 83.9, 62.1, 79.8]`
- Colour-coded bars: Navy for passing bidders, amber for review, red for failing

#### Mitigate Bias Button
Clicking **Mitigate Bias** does the following in one action:
1. Animates the Chart.js bars to post-mitigation scores: `[87.4, 84.0, 83.9, 80.5, 84.2]`
2. Changes all bar colours to `#1a7a4a` (green)
3. Updates Innovate MSME (0.71 → 0.84), StarShield (0.58 → 0.82), NovaSec (0.78 → 0.83) in the table
4. Flips their badges to PASS
5. Displays a regulatory note: *"MSME preference (Rule 4, MSME Order 2012) and DPIIT loading applied"*
6. Fires a Compliance Agent log entry
7. Button label changes to **Undo Mitigation** — fully reversible

---

### Module 4 — Deterministic Fairness Receipt

**Location:** Left column, bottom card  
**Purpose:** Generates a tamper-evident, citation-backed audit receipt for any bidder's evaluation score — the core audit trail feature of TEAP.

#### Controls
- **Bidder dropdown** — Choose from all 5 demo bidders
- **Evaluation Stage dropdown** — Technical Evaluation / Financial Evaluation / Compliance Check
- **Generate Fairness Receipt button** — Opens the receipt modal

#### Receipt Contents

Each receipt includes:

| Field | Description |
|---|---|
| Receipt Hash | Unique `TEAP-XXXXXXXX-XXXXXXXX` identifier (cryptographic-style, JS-generated) |
| Timestamp | Live IST date and time at generation |
| Tender Reference | CAPF/MHA/2026/T-0091 |
| Bidder Name & Rank | From the demo dataset |
| Composite Score | Weighted aggregate |
| Sub-scores | Technical / Financial / Compliance |
| DIR Value | Disparate Impact Ratio |
| Status Badge | QUALIFIED / CONDITIONAL / DISQUALIFIED |
| Regulatory Citations | Exact Document, Page number, Paragraph number, and quoted text |
| Evaluator Notes | Bidder-specific findings and flags |
| Signature Block | Three-column: Technical Member / Financial Advisor / Committee Chair |
| Tamper Notice | "Any alteration renders it void" with hash footer |

#### Example Regulatory Citation (Bharat Systems Ltd.)
> **RFP Document — Page 12, Para 4**  
> *"Technical evaluation shall be carried out against Mandatory Technical Specifications (MTS) listed in Annex-III…"*

#### Receipt Data Store (JavaScript)
All receipt data is stored in the `RECEIPT_DATA` object keyed by bidder name, containing scores, citations, and notes. This is the "deterministic" engine — the same inputs always produce the same receipt.

#### Print / Export
The **Print / Export PDF** button triggers `window.print()` — use the browser's "Save as PDF" option.

---

### Module 5 — DPIIT / Make in India Tracker

**Location:** Right sidebar, bottom card  
**Authority:** DPIIT Order No. P-45021/2/2017-PP(BE-II)  
**Purpose:** Tracks Make in India eligibility and local content compliance for all bidders.

#### Status Matrix

| Bidder | Supplier Class | Local Content | Status |
|---|---|---|---|
| Bharat Systems Ltd. | Class I | 67% | ✅ Eligible |
| Innovate MSME Co. | Class II | 54% | ✅ Eligible |
| DefenceTech Pvt. | Class II | 51% | ⚠️ Verify |
| StarShield Infra | Foreign JV | 22% | ❌ Ineligible |
| NovaSec Dynamics | Class I | 71% | ✅ Eligible |

#### Important Notice
A yellow warning banner flags: *"StarShield Infra's bid will be subject to 20% loading as per GFR Rule 149."*

This is cross-linked with the Financial Agent's log and the receipt generator for StarShield.

---

## 5. Technical Stack

| Technology | Version | Source | Purpose |
|---|---|---|---|
| HTML5 | — | Native | Structure |
| CSS3 | — | Native | Custom styles, animations |
| Vanilla JavaScript | ES2020 | Native | All interactivity and logic |
| **Tailwind CSS** | Latest | CDN (`cdn.tailwindcss.com`) | Utility-first layout & spacing |
| **Font Awesome** | 6.5.0 | cdnjs.cloudflare.com | Icons throughout the UI |
| **Chart.js** | Latest | jsdelivr.net | Fairness bar chart |
| **EB Garamond** | 400/500/600 | Google Fonts | Display / heading typography |
| **Source Sans 3** | 300–700 | Google Fonts | Body / UI typography |

> **Zero build tools required.** No Node.js, no npm, no webpack. The file runs entirely in the browser.

---

## 6. File Structure

```
project-teap/
│
├── index.html          ← Entire application (single file)
└── README.md           ← This document
```

That's it. The entire portal is contained within `index.html`. All CSS (custom + Tailwind), JavaScript, and HTML are in one file. External dependencies are loaded via CDN.

---

## 7. Design System

### Colour Palette

| Token | Hex | Usage |
|---|---|---|
| `--navy` | `#002147` | Primary — headers, buttons, borders |
| `--navy-light` | `#003366` | Hover states |
| `--navy-faint` | `#e8edf5` | Card headers, table headers, backgrounds |
| `--gold` | `#b8860b` | Accent — header border, emblem |
| `--gold-light` | `#d4a820` | Emblem gradient, highlights |
| `--green` | `#1a7a4a` | Success states, mitigated chart |
| `--red` | `#b91c1c` | Error/fail states |
| `--grey-border` | `#d1d5db` | Card and table borders |
| `--grey-bg` | `#f5f6f8` | Page background |

### Typography

| Role | Font | Weight |
|---|---|---|
| Government heading, receipt titles | EB Garamond | 400–600 |
| UI labels, body text, table content | Source Sans 3 | 300–700 |
| Agent console logs | Courier New (monospace) | 400 |

### Component Patterns

- **Cards** — White background, `1px` grey border, `4px` border-radius, subtle box-shadow
- **Card Headers** — Navy-faint background, navy text, uppercase, `0.08em` letter-spacing
- **Primary Buttons** — Navy fill, white text, no radius
- **Secondary Buttons** — White fill, navy border/text
- **Badges** — Pill-shaped, semantic colour (green/amber/red)
- **Status Pills** — Rounded, used in DPIIT tracker
- **Drop Zone** — Dashed border, lightens on hover/drag

### Animations

| Name | Element | Effect |
|---|---|---|
| `scan-anim` | PII scan bar | Gradient scrolls left → right, infinite loop |
| `pulse-dot` | Agent status dots | Opacity pulse 1 → 0.4 → 1, 1.2s |
| `ticker` | Header ticker strip | Horizontal scroll, 30s loop |
| Chart.js transition | Fairness bars | 800ms animated height change on bias mitigation |
| File progress bar | Per-file bars | CSS `transition: width 1.2s ease` |

---

## 8. Regulatory & Compliance References

| Reference | Description | Used In |
|---|---|---|
| **GFR 2017, Rule 149** | DPIIT purchase preference and price loading for local vs non-local suppliers | Module 5, Receipt Generator, Financial Agent |
| **GFR 2017, Rule 160** | Evaluation committee procedures and score documentation | Compliance Agent |
| **DPIIT Order P-45021/2/2017-PP(BE-II)** | Make in India preferential market access policy — Class I / Class II thresholds | Module 5 |
| **MSME Procurement Policy Order 2012, Rule 4** | Price preference and procurement target for Micro & Small Enterprises | Bias mitigation note, Receipt Generator |
| **RFP Document, Page 12, Para 4** | Mandatory Technical Specifications (MTS) evaluation methodology | Receipt citations |
| **GFR 2017, Rule 74, Para 2** | Score assignment methodology for evaluation committees | Receipt citations |
| **CEMILAC** | Centre for Military Airworthiness & Certification — referenced in Technical Agent | Module 2 |
| **NSIC** | National Small Industries Corporation — MSME certification | Module 2 |
| **CVC Blacklist Registry** | Central Vigilance Commission vendor blacklist | Compliance Agent |
| **MoD Blacklist Registry** | Ministry of Defence vendor debarment list | Compliance Agent |

---

## 9. Bidder Dataset (Demo)

Five fictitious bidders are pre-loaded for demonstration:

### Bharat Systems Ltd.
- **Category:** Large / Public Sector  
- **Technical Score:** 87.4 | **Financial Score:** 86.5 | **Compliance Score:** 88.3  
- **Composite Score:** 87.4 | **Rank:** 1 of 5  
- **DIR:** 0.92 (PASS) | **DPIIT:** Class I, 67% local content — Eligible  

### Innovate MSME Co.
- **Category:** MSME / Startup  
- **Technical Score:** 80.5 | **Financial Score:** 82.1 | **Compliance Score:** 81.0  
- **Composite Score:** 81.2 | **Rank:** 3 of 5  
- **DIR:** 0.71 (REVIEW) → 0.84 after mitigation | **DPIIT:** Class II, 54% local content — Eligible  
- **L1 Bidder:** ₹14.82 Cr  

### DefenceTech Pvt.
- **Category:** Private / Mid-size  
- **Technical Score:** 84.2 | **Financial Score:** 83.5 | **Compliance Score:** 84.1  
- **Composite Score:** 83.9 | **Rank:** 2 of 5  
- **DIR:** 0.85 (PASS) | **DPIIT:** Class II, 51% local content — Verify (pending updated affidavit)  

### StarShield Infra
- **Category:** Foreign Joint Venture  
- **Technical Score:** 62.0 | **Financial Score:** 69.3 | **Compliance Score:** 55.1  
- **Composite Score:** 62.1 | **Rank:** 5 of 5  
- **DIR:** 0.58 (FAIL) → 0.82 after mitigation | **DPIIT:** Foreign JV, 22% local content — **Ineligible**  
- **Note:** 20% price loading applied per GFR Rule 149. Missing NSIC certificate.

### NovaSec Dynamics
- **Category:** MSME / Women-led  
- **Technical Score:** 79.5 | **Financial Score:** 80.1 | **Compliance Score:** 79.9  
- **Composite Score:** 79.8 | **Rank:** 4 of 5  
- **DIR:** 0.78 (REVIEW) → 0.83 after mitigation | **DPIIT:** Class I, 71% local content — Eligible  

---

## 10. Deployment Guide

### GitHub Pages (Recommended — Zero Config)

1. Create a new GitHub repository (e.g., `project-teap`)
2. Upload `index.html` and `README.md` to the root of the `main` branch
3. Go to **Settings → Pages → Source** → Select `main` branch, `/ (root)` folder
4. Click **Save**
5. Your portal will be live at: `https://<your-username>.github.io/project-teap/`

> No build step. No configuration. No server. The page is fully static.

### Netlify (Drag & Drop)
1. Go to [netlify.com](https://netlify.com) → Sites → Drag & Drop
2. Drop the folder containing `index.html`
3. Site is live immediately at a `*.netlify.app` URL

### Vercel
```bash
npm i -g vercel
vercel --prod
```
Select the folder with `index.html`. Done.

### NIC / Government Server (Apache / Nginx)
1. Copy `index.html` to the web root (e.g., `/var/www/html/teap/`)
2. Ensure HTTPS is configured (mandatory for government deployments)
3. No additional configuration needed

### Intranet / Offline Deployment
If the deployment environment has no internet access, download the CDN dependencies and host them locally:

```html
<!-- Replace CDN links with local paths -->
<script src="./assets/tailwind.min.js"></script>
<link rel="stylesheet" href="./assets/fontawesome.min.css"/>
<script src="./assets/chart.min.js"></script>
<!-- Self-host fonts as WOFF2 files -->
```

---

## 11. Running Locally

No setup required. Simply open the file in any modern browser:

```bash
# Option 1: Double-click index.html in your file manager

# Option 2: Open via terminal (macOS)
open index.html

# Option 3: Open via terminal (Linux)
xdg-open index.html

# Option 4: Use a local dev server (optional, for CDN bypass testing)
npx serve .
# Then visit http://localhost:3000
```

---

## 12. Browser Compatibility

| Browser | Version | Status |
|---|---|---|
| Google Chrome | 90+ | ✅ Fully supported |
| Mozilla Firefox | 88+ | ✅ Fully supported |
| Microsoft Edge | 90+ | ✅ Fully supported |
| Safari | 14+ | ✅ Fully supported |
| Opera | 76+ | ✅ Fully supported |
| Internet Explorer | Any | ❌ Not supported |
| Chrome for Android | 90+ | ✅ Responsive layout |
| Safari for iOS | 14+ | ✅ Responsive layout |

> **Minimum requirement:** A browser that supports CSS Grid, CSS Custom Properties, ES2020 (`const`, `let`, arrow functions, template literals), and the Fetch API (for CDN loading).

---

## 13. Known Limitations & Disclaimer

### Prototype Disclaimer
> This application is a **functional prototype for demonstration and research purposes only**. It does **not** connect to any real government database, procurement system, NIC infrastructure, or AI backend. All bidder data, scores, citations, and agent logs are **simulated**.

### Current Limitations

1. **No backend / persistence** — All state is held in browser memory. Refreshing the page resets everything.
2. **No real AI agents** — The Multi-Agent Console plays back pre-scripted log lines. It does not perform real document analysis.
3. **No real file processing** — Uploaded files are not read, parsed, or stored. The PII scan is a visual animation only.
4. **No authentication** — The officer name ("Sanjay Kumar, IPS") is hardcoded. There is no real login system.
5. **No cryptographic hashing** — The receipt hash is a pseudo-random string, not a real cryptographic hash of the receipt contents.
6. **Single-language** — The interface is in English only (with one bilingual element in the header). No full Hindi translation.
7. **Static bidder data** — All five bidders and their scores are hardcoded. There is no mechanism to add real bidders.
8. **Print layout** — The `window.print()` receipt export has not been optimised with a dedicated `@media print` stylesheet.
9. **Responsive layout** — The two-column layout collapses on screens below 900px, but the application is optimised primarily for desktop use (1280px+).
10. **CDN dependency** — The application requires internet access to load Tailwind, Font Awesome, Chart.js, and Google Fonts from CDNs.

---

## 14. Roadmap

These features would be required to make TEAP production-ready:

### Phase 1 — Backend Integration
- [ ] NIC-hosted REST API for document storage and retrieval
- [ ] PostgreSQL / Oracle database for persistent tender records
- [ ] Role-based access control (RBAC) — Committee Chair, Technical Member, Financial Advisor, Observer
- [ ] MFA / CAC-card authentication integration

### Phase 2 — Real AI Pipeline
- [ ] Document parsing engine (PDF, DOCX, XLSX → structured JSON)
- [ ] Real PII detection using regex + NLP (Aadhaar, PAN, bank account patterns)
- [ ] LLM-powered Technical Agent for spec-vs-BOM comparison
- [ ] Automated financial L1/L2 computation engine
- [ ] Compliance document verification against MCA21, NSIC, CVC APIs

### Phase 3 — Security & Audit
- [ ] SHA-256 cryptographic hashing of all receipts
- [ ] Immutable audit log stored to blockchain or append-only database
- [ ] Digital signature integration (DSC) for committee members
- [ ] VAPT (Vulnerability Assessment & Penetration Testing) clearance

### Phase 4 — Advanced Fairness
- [ ] Real statistical Disparate Impact Ratio computation
- [ ] Historical bias trend analysis across multiple tenders
- [ ] FairCheck™ explainability module with SHAP values
- [ ] GEM (Government e-Marketplace) integration for vendor categorisation

### Phase 5 — Localisation & Accessibility
- [ ] Full Hindi (हिंदी) translation
- [ ] WCAG 2.1 AA accessibility compliance
- [ ] Screen reader optimisation
- [ ] Low-bandwidth mode (offline-capable PWA)

---

## 15. Contributing

This is a prototype developed for research and policy demonstration. If you wish to contribute:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Make changes to `index.html` (keeping everything in one file unless explicitly expanding)
4. Test across Chrome, Firefox, and Safari
5. Submit a pull request with a clear description of changes

### Coding Conventions
- All JavaScript stays in the single `<script>` tag at the bottom of `index.html`
- CSS custom properties (variables) are defined in `:root` — use them for all colour references
- New modules should follow the existing card pattern with `.card` + `.card-header`
- Agent log entries use `addAgentLog(type, label, message)` — do not manipulate `#agentLog` directly
- All regulatory citations must reference real Indian government documents (GFR 2017, DPIIT Orders, etc.)

---

## 16. License

```
Copyright © 2026 Government of India, Ministry of Home Affairs.
Central Armed Police Forces Directorate.

This software prototype is developed for internal research and 
policy evaluation purposes. Redistribution, commercial use, or 
deployment in production government systems requires explicit 
written authorisation from the Ministry of Home Affairs, 
Government of India.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.
```

---

## Acknowledgements

- **National Informatics Centre (NIC)** — Infrastructure and security standards reference
- **DPIIT, Ministry of Commerce** — Make in India procurement policy framework
- **Controller General of Accounts (CGA)** — GFR 2017 procedural reference
- **Central Vigilance Commission (CVC)** — Integrity and transparency guidelines
- **Tailwind Labs** — Tailwind CSS framework
- **Chart.js Contributors** — Open-source charting library
- **Font Awesome** — Icon library

---

*For technical queries regarding this prototype, contact the TEAP Project Coordinator, CAPF IT Division, Ministry of Home Affairs, North Block, New Delhi — 110001.*

*Document version: 1.0 | Last updated: June 2026*
