# PM Agent — All-in-One Product Management Workspace

> **Gated Autopilot** — AI-powered product management with mandatory human oversight checkpoints at every stage of the spec-to-code workflow.

---

## What This Is

PM Agent is a single-file, browser-based product management tool that covers the entire workflow from writing a PRD through to tracking CI/CD pipeline health — with a **Human Checkpoint System** that enforces review gates before any AI-generated output can proceed to the next stage.

Built as a response to a real product management challenge: *how do you design an AI-assisted spec-to-code workflow that is fast enough to be useful but safe enough to maintain developer trust?*

The answer implemented here is **Gated Autopilot** — automation runs ahead, humans decide whether to follow.

## Workflow
<img width="1895" height="514" alt="Project Development Workflow" src="https://github.com/user-attachments/assets/4de0a537-8dd7-4115-9e07-06cde203e011" />


---

## Features

### 1. PRD Builder
- Chat with the PRD Agent to describe any feature
- Generate a full PRD across 4 tabs: Overview, Requirements, User Stories, Success Metrics
- Quick-action buttons for user stories, KPIs, risks, and timeline
- **Gate 1** pre-approved with full audit trail (4 checks: acceptance criteria, API surface, security requirements, edge cases)

### 2. Implementation Plan
- AI generates a 3-sprint breakdown with tasks, owners, story points, and dependencies
- All architectural decisions flagged with `[ARCH DECISION REQUIRED]` — none auto-resolved
- **Gate 2** — 5-item interactive checklist that must be fully completed before the Approve button enables:
  - Spec validated against approved PRD
  - Architecture reviewed by engineer
  - Dependencies confirmed (no hallucinated libraries)
  - Security constraints applied
  - No autonomous architectural decisions made
- **Request Changes** modal with priority levels (blocking / important / minor)
- **Reject & Halt** modal — full pipeline stop with reason logged

### 3. Sprint Board
- Kanban board with To Do / In Progress / Done columns
- Tasks show BLOCKED or GATED status when Gate 2 is incomplete
- Team velocity tracker (Jake Kim, Rosa Lin, Alex Morgan)
- One-click auto-remind for overdue task owners
- **Gate 3** — Draft MR promotion gate with 4 checks:
  - Gate 2 prerequisite confirmed
  - Engineer has reviewed the full diff
  - Unit tests passing in CI
  - Security scan clear (zero findings)
- Promote modal shows MR summary (lines, files, tests) and reviewer assignment

### 4. DORA Metrics & CI/CD
- **Locked** until Gate 2 + Gate 3 are both approved
- Shows all 4 DORA metrics with Elite / Medium / needs-work ratings:
  - Deployment frequency: 4.2/day (Elite)
  - Lead time for changes: 1.8h (Elite)
  - Change failure rate: 4.2% (Medium — needs improvement)
  - Time to restore: 23m (Elite)
- CI/CD pipeline stage view (Build → Test → SAST → Staging → Deploy) with pass/fail per run
- Full gate audit trail displayed alongside pipeline data

<img width="1260" height="727" alt="Screenshot 2026-04-22 at 10 27 31 PM" src="https://github.com/user-attachments/assets/fdbbe0ba-d4ce-401a-82f8-05978c66fed4" />





### 5. Audit Log
- Persistent log of every gate decision, agent action, and timestamp
- Records: approver name, checks completed, notes, and downstream effects
- Exportable

### 6. PM Assistant
- Context-aware agent that knows gate status, sprint progress, and DORA metrics simultaneously
- Gate-aware suggested prompts:
  - Gate 2 review guide
  - Detecting gate fatigue / rubber-stamping
  - Writing effective change requests
  - Gate 3 diff review tips
  - Diagnosing high change failure rate
- Live workspace state panel showing current gate status at a glance

---

## The Human Checkpoint System

The core design principle: **no AI-generated output reaches the next stage without a human explicitly deciding to send it there.**

```
PRD → [Gate 1] → Plan → [Gate 2] → Code → [Gate 3] → Review → Merge
       Approved    ↑     Pending    ↑      Locked     ↑
                PM sign           Eng sign           Eng promotes
```

### Gate states
| State | Meaning | Visual |
|---|---|---|
| `APPROVED` | All checks passed, human confirmed | Green border + ✅ |
| `PENDING` | Checklist incomplete or awaiting decision | Amber border + ⚠️ |
| `LOCKED` | Prerequisite gate not yet approved | Grey border + 🔒 |
| `ACTIVE` | Gate is open for review right now | Terracotta border + ⚠️ |

### What is never automated
- Architectural decisions (agent flags, human resolves)
- Promoting draft MRs into the review pipeline
- Merging or deploying code
- Any decision that affects system design

### What automation safely runs ahead on
- Scoring spec quality
- Generating implementation plans
- Identifying dependencies and flagging hallucination risks
- Writing boilerplate code and unit test scaffolds
- Creating branches and draft MRs
- Logging all actions for audit

---

## Getting Started

### Prerequisites
- A modern browser (Chrome, Firefox, Safari, Edge)
- An [Anthropic API key](https://console.anthropic.com) to enable live AI features

### Running the tool
1. Download `pm_agent_v3.html`
2. Open it directly in your browser — no server, no install, no dependencies
3. The tool works immediately for the gate workflow, sprint board, and DORA view
4. To enable live AI generation (PRD drafting, implementation plans, chat agents), add your Anthropic API key

### Adding your API key
The tool calls `https://api.anthropic.com/v1/messages` directly from the browser. To enable this:

1. Open `pm_agent_v3.html` in a text editor
2. Find the `callAI` function near the bottom of the `<script>` block
3. Add your key to the fetch headers:
```javascript
headers: {
  'Content-Type': 'application/json',
  'x-api-key': 'sk-ant-YOUR-KEY-HERE',
  'anthropic-version': '2023-06-01',
  'anthropic-dangerous-direct-browser-access': 'true'
}
```

> **Security note:** Do not commit your API key to a public repository. For team use, route API calls through a backend proxy that holds the key server-side.

---

## Project Structure

```
pm_agent_v3.html        — Complete single-file application (HTML + CSS + JS)
pm_agent_all_in_one.html — Previous version (v2, no checkpoint system)
README.md               — This file
```

All styling, logic, and AI integration live in a single HTML file. No build step, no framework, no external dependencies beyond:
- [Google Fonts](https://fonts.google.com) — Fraunces, DM Sans, DM Mono (loaded from CDN)
- [Chart.js 4.4.1](https://cdnjs.cloudflare.com) — for DORA trend charts (loaded from CDN)
- [Anthropic API](https://api.anthropic.com) — for AI generation (requires your key)

---

## AI Model

All AI features use `claude-sonnet-4-20250514`. Each agent has a focused system prompt:

| Agent | System prompt focus |
|---|---|
| PRD Agent | Senior PM · clean HTML output · concise and structured |
| Implementation Agent | Senior eng lead · flag arch decisions · no auto-resolution |
| PM Assistant | Full gate context · DORA metrics · under 180 words · actionable |

---

## Case Study Context

This tool was built to demonstrate the **Gated Autopilot** approach from a product management case study on spec-to-code automation:

> *"An MVP that ships fast but erodes developer trust is worse than not shipping at all."*

The four leadership risks addressed:

| Risk | Mitigation in the tool |
|---|---|
| Poor spec quality leading agent astray | Gate 1 spec validation with rubric before any agent action |
| Hallucinated implementations that look plausible | Gate 2 plan review surfaces hallucinations before code is written |
| Architectural decisions without oversight | Agent flags all arch decisions — none auto-resolved |
| Insufficient human checkpoints before code enters review | Three explicit gates · draft MR held until human promotes it |

---

## Validation Metrics

If this tool is working well in a real deployment, you would observe:

**Leading (Week 1–4)**
- Spec quality score > 70/100 by week 3
- ≥ 80% of implementation plans approved without major amendment
- Gate drop-off rate < 20%
- End-to-end time spec → draft MR < 4 hours (including human gate time)

**Lagging (Week 4–8)**
- AI MR rework rate ≤ human baseline
- Review cycle time ≤ equivalent human MR
- Zero security/compliance incidents in first 60 days
- ≥ 2 Staff+ engineers voluntarily promote an AI draft MR

**Stop conditions**
- Any security incident linked to AI code → immediate halt and audit
- Rework rate > 15% above baseline → redesign gate checkpoints
- Engineers rubber-stamping gates → gate fatigue, redesign UX

---

## Roadmap

- [ ] GitLab API integration for live DORA metrics and real pipeline data
- [ ] YouTrack webhook trigger to start the Gate 1 → Gate 2 workflow automatically
- [ ] Persistent storage via backend so gate state survives page refresh
- [ ] Multi-user mode — PM approves Gate 1, Engineer approves Gate 2
- [ ] Slack / email notifications when a gate is pending review
- [ ] Spec quality scorer with rubric-based feedback before workflow starts
- [ ] Export PRD and implementation plan as PDF or Word document

---

## License

Built for demonstration and case study purposes. Adapt freely for your team's workflow.

---

*PM Agent v3 · Built with Claude Sonnet · Gated Autopilot architecture*
