# Job Search & Unemployment Compliance Companion

> **Vanguard Compliance OS**: An enterprise-grade, cyber-chic executive dashboard engineered to streamline job hunting, guarantee unemployment insurance (UI) work-search compliance, audit & tailor resumes with AI against Applicant Tracking Systems (ATS), formulate STAR interview answers, and screen third-party recruiters.

---

## 📑 Table of Contents

1. [Overview & Architecture](#-overview--architecture)
2. [Core Modules & Features](#-core-modules--features)
   - [1. Activity Log & UI Compliance Engine](#1-activity-log--ui-compliance-engine)
   - [2. AI Resume Studio & ATS Auditor](#2-ai-resume-studio--ats-auditor)
   - [3. STAR Interview Answer Builder](#3-star-interview-answer-builder)
   - [4. Recruiter Legitimacy Screener](#4-recruiter-legitimacy-screener)
3. [API Architecture (`server.ts`)](#-api-architecture-serverts)
4. [Client Architecture & Data Flow (`index.html`)](#-client-architecture--data-flow-indexhtml)
5. [Storage & Persistence Model](#-storage--persistence-model)
6. [Responsive Design & Navigation](#-responsive-design--navigation)
7. [Environment Variables & Setup](#-environment-variables--setup)
8. [Available Scripts](#-available-scripts)
9. [Verification & Code Quality](#-verification--code-quality)

---

## 🏛 Overview & Architecture

The application is architected as a **full-stack Node.js / Express + Vite SPA** adhering strictly to enterprise security standards:

```
┌────────────────────────────────────────────────────────┐
│               Client-Side Browser App                  │
│       Tailwind CSS + Lucide Icons + Responsive UI       │
│  - Sunday-to-Saturday Work-Search Cycle Engine         │
│  - Master Resume Vault & Tailored Version History      │
│  - STAR Behavioral Matrix Interactive Previews        │
│  - Agency Recruiter Scoring Checklist                 │
└───────────────────────────┬────────────────────────────┘
                            │ /api/* (Proxied REST calls)
                            ▼
┌────────────────────────────────────────────────────────┐
│            Express Server (`server.ts`)               │
│  - Secure Server-Side Gemini API Proxy                │
│  - Multi-Model Automatic Fallback Resilience           │
│  - Heuristic Offline Engines (Graceful Degradation)    │
│  - Vite Middleware Development Integration             │
└───────────────────────────┬────────────────────────────┘
                            │ GEMINI_API_KEY (Server-only)
                            ▼
┌────────────────────────────────────────────────────────┐
│             Google Gemini API Models                  │
│       `gemini-3.8-flash` / `gemini-flash-latest`       │
└────────────────────────────────────────────────────────┘
```

- **Zero Client-Side Secret Leakage**: The `GEMINI_API_KEY` is exclusively read and utilized on the backend within `server.ts`. No secret keys are exposed to the browser.
- **Resilient Fallbacks**: If the Gemini API experiences network limits or missing credentials, the backend automatically switches to deterministic heuristic scoring and synthesis models so the application never breaks.
- **Offline First**: All user-authored logs, custom criteria, behavioral interview records, master resumes, and tailored outputs persist instantly to client `localStorage`.

---

## 🚀 Core Modules & Features

### 1. Activity Log & UI Compliance Engine
- **Sunday-to-Saturday Strict Tracking**: Automatically partitions activities into the standard unemployment benefit week (Sunday 12:00 AM to Saturday 11:59 PM).
- **Interactive Compliance Circular Gauge**: Displays real-time progress toward the weekly work search requirement (default: 5 activities, configurable in Settings).
- **Audit-Ready Exporting**: 
  - **Print / PDF Formal Audit Dossier**: Formats a clean, high-contrast, black-and-white state unemployment agency submission document complete with signature/certification lines.
  - **CSV Spreadsheet Export**: Generates standardized comma-separated files for Excel, Google Sheets, or state upload portals.
  - **JSON Data Backup**: Complete portability for backing up and restoring data.
- **Search & Multi-Filter**: Filter by search terms, status (`Applied`, `Interviewing`, `Offer`, `Archived`), or work search compliance eligibility.

### 2. AI Resume Studio & ATS Auditor
- **ATS Match & Parseability Scoring**:
  - Calculates a 0–100 ATS Compatibility Score based on hard skills, keywords, and typography layout.
  - Calculates an Executive Recruiter 6-Second Glance Score.
  - Detects formatting parseability risks (multi-column tables, misplaced contact headers).
- **Targeted Keyword Gap Analysis**: Highlights matched vs. missing high-priority technical skills, methodologies, and domain keywords found in the Job Description.
- **Action Bullet Auditing (Google XYZ Formula)**: Identifies weak, passive bullet points and transforms them into quantified achievements (`Accomplished [X] as measured by [Y], by doing [Z]`).
- **Tailored Resume Generator**:
  - Integrates user-selected job context or manual job descriptions against the Master Resume.
  - Generates tailored professional summaries, core competency grids, optimized experience bullets, and ready-to-use LinkedIn InMail outreach notes.
  - **Tailored Resume Vault**: Saves and stores multiple tailored versions for quick copy-to-clipboard or plain text export.

### 3. STAR Interview Answer Builder
- **Behavioral Framework**: Structures responses into Situation, Task, Action, and Result.
- **Category Matrix**: Pre-configured behavioral prompt bank covering Leadership, Conflict Resolution, Technical Architecture, Failure & Resilience, and Ambiguity.
- **Live Preview & Word Counter**: Tracks delivery timing estimates (e.g., target 2–3 minute speaking response length).

### 4. Recruiter Legitimacy Screener
- **Agency Vetting Scorecard**: Evaluates inbound recruiter inquiries against standard staffing red flags:
  - Exclusive representation rights without a named client company.
  - Refusal to provide verified salary/rate transparency.
  - Generic mass outreach or off-domain communication channels (WhatsApp/Telegram).
- **Decision Engine**: Generates a composite Risk Score with actionable next steps (Proceed, Request Written Verification, or Reject/Block).

---

## 🔌 API Architecture (`server.ts`)

| Route | Method | Payload / Params | Purpose |
| :--- | :--- | :--- | :--- |
| `/api/health` | `GET` | None | Returns server health status and verifies whether `GEMINI_API_KEY` is present. |
| `/api/resume/evaluate` | `POST` | `{ resumeText, jobDescription, jobTitle?, company? }` | Runs structured ATS audit using Gemini (`gemini-3.8-flash`) with structured JSON schema output and multi-model fallback. |
| `/api/resume/tailor` | `POST` | `{ masterResume, jobDescription, jobTitle?, company?, targetEmphasis? }` | Synthesizes a role-tailored resume package, ATS keyword matrix, and LinkedIn InMail pitch. |

### Multi-Model Fallback Engine
Calls to Gemini are encapsulated in `callGeminiWithFallback()`:
1. First attempts `gemini-3.8-flash` for highest quality and speed.
2. If transient capacity errors occur (503 / 429), automatically retries with `gemini-flash-latest` and `gemini-3.1-flash-lite`.
3. If no key is set or all network requests fail, seamlessly serves an algorithmic fallback response so the user interface remains fully functional.

---

## 💻 Client Architecture & Data Flow (`index.html`)

The frontend is structured in `index.html` with vanilla JavaScript for zero-latency, reactive execution:

```
index.html
├── <head>
│   ├── Cyber-obsidian design token styles & animations
│   ├── Responsive @media queries for primary navigation visibility
│   └── Lucide icon library & Tailwind CSS CDN
├── <body>
│   ├── <header> Top Cyber-Bar (Logo, Quick Log, Export, Settings)
│   ├── <nav id="primaryNav"> Sticky Desktop & Tablet Navigation (>= 640px)
│   ├── <main>
│   │   ├── #viewActivities: Hero strip, circular gauge, activity list & modal
│   │   ├── #viewResume: ATS Audit & Targeted Tailor Studio, Vault
│   │   ├── #viewInterview: STAR answer questions and editor
│   │   └── #viewScreener: Recruiter checklist and scoring gauge
│   ├── Modals (Log Activity, Export Audit, Goal Settings)
│   ├── <nav id="mobileBottomNav"> Fixed Mobile Dock (< 640px)
│   └── <script>
│       ├── State Management (STORAGE_KEYS, SAMPLE_ACTIVITIES)
│       ├── Sunday-to-Saturday Week Calculation Helpers
│       ├── Rendering Engines (Dashboard, Activities, Resume, STAR, Screener)
│       └── Print / CSV / JSON Export Pipelines
```

---

## 💾 Storage & Persistence Model

All user state is stored using standard `localStorage` with distinct namespaces:

| Storage Key | Type | Description |
| :--- | :--- | :--- |
| `vanguard_compliance_activities_v2` | `Activity[]` | Work search activity history including dates, company, position, method, and compliance status. |
| `vanguard_compliance_goal_v2` | `number` | Required weekly activity count for state compliance (default: `5`). |
| `vanguard_behavioral_answers_v2` | `Answer[]` | Saved STAR behavioral interview stories mapped by question category. |
| `vanguard_recruiter_screeners_v2` | `ScreenerRecord[]` | History of agency recruiter assessments and legitimacy scores. |
| `vanguard_master_resume_v2` | `string` | Master resume content used as the source for all AI evaluations and tailoring. |
| `vanguard_tailored_vault_v2` | `TailoredResume[]` | Saved role-tailored resume variations with metadata and date stamps. |

---

## 📱 Responsive Design & Navigation

- **Computer & Tablet (Viewport >= 640px)**:
  - Prominently anchored top navigation bar (`#primaryNav`) positioned immediately below the header.
  - Four dedicated tab buttons with live compliance progress indicators (`0/5`), AI badges, and hover glow transitions.
- **Mobile Handheld (Viewport < 640px)**:
  - Sticky bottom navigation dock (`#mobileBottomNav`) with touch-friendly 44px+ targets and quick-action floating action buttons.
  - Top header compacts gracefully to maximize vertical working area.

---

## ⚙️ Environment Variables & Setup

Create a `.env` file in the root directory (based on `.env.example`):

```env
# Google Gemini API key for ATS evaluation & resume tailoring
GEMINI_API_KEY=your_gemini_api_key_here
```

> **Note**: If `GEMINI_API_KEY` is not provided, the application continues to run without error using the embedded heuristic resume auditing and tailoring engines.

---

## 🛠 Available Scripts

```bash
# Install dependencies
npm install

# Start development server on port 3000 (Express + Vite middleware)
npm run dev

# Lint codebase for TypeScript errors
npm run lint

# Build for production (Vite client build + esbuild server bundle)
npm run build

# Start production server
npm run start
```

---

## ✅ Verification & Code Quality

- **Type Safety**: Strictly checked with `tsc --noEmit`.
- **Bundling**: Server is bundled using `esbuild` to `dist/server.cjs` for fast startup on Cloud Run containers.
- **Accessibility**: High-contrast slate/cyan palette with WCAG AA compliance and visible focus indicators.
