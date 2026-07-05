# Adobe Commerce Partnerships AI KIT — Instruction Manual

This manual teaches you how to use the Adobe Commerce Partnerships AI KIT to implement Adobe VIPMP Partners API features in the **adobe-commerce-partnerships-ref-app** or your own project using AI-assisted code generation.

---

## Agent Setup

Before running any skills, wire the kit to your AI coding agent:

| Agent | Setup guide |
|-------|-------------|
| Claude Code | [Setup for Claude Code](setup/claude-code.md) |

---

## Before You Start

Read **[Conceptual Guide](conceptual-guide.md)** first, regardless of your role. It explains the mental model behind the toolkit and the split between what Adobe provides and what you do. Every other guide assumes you have read it.

---

## Guide Map

### Quick Start

| Guide | Purpose |
|---|---|
| [Quick Start](quick-start.md) | End-to-end walkthrough using the kit with a real project<br>Covers multiple paths — visualize the feature, try it on the reference app, or run the full workflow on a real project |

---

### Prerequisites

**Service Cards** — a structured description of your codebase that the AI reads before generating any code, so the output matches your project's own patterns.

Run these once when onboarding a new project, before starting any Feature Workflow guide — and again after major code changes to the target repo.

| Guide | Purpose  |
|---|---|
| [Backend Service Card Generation](first-run/backend-service-card.md) | Scans your backend codebase and produces a structured description of how it is coded  |
| [UI Service Card Generation](first-run/ui-service-card.md) | Scans your frontend codebase and produces a structured description of how it is coded  |

---

### Feature Workflow Guides
Run these once per feature.

| Guide | Purpose | Source |
|---|---|---|
| [Receiving the Experience Card](feature-workflow/review-experience-card.md) & [API Spec](feature-workflow/review-api-spec.md) | AI reads both to produce the LLDs in later steps.<br>Review the user journey document so you can adapt it to your brand and UX guidelines if needed | Provided by Adobe |
| [Generating the Backend LLD](feature-workflow/generate-backend-lld.md) | AI produces a backend engineering plan from the API spec and your service cards | You run this |
| [Generating the UI LLD](feature-workflow/generate-ui-lld.md) | AI produces a UI engineering plan from the experience card and backend LLD | You run this |
| [Generating Code](feature-workflow/generate-code.md) | AI generates working code from the approved LLDs and service cards, then audits it against the LLDs and runs a guided browser checklist | You run this |

---

### Supporting Guides

| Guide | Purpose |
|---|---|
| [Decision Gate Guide](./decision-gates.md) | What to review at each approval checkpoint, and why it matters |
| [Reference](./reference.md) | Quick-lookup tables for commands, file paths, naming conventions, and common errors |

---

## Workflow at a Glance

```
┌─────────────────────────────────────────────────┐
│  ADOBE PROVIDES — ONE TIME (the toolkit)        │
│  Skills  /generate-backend-service-card         │
│          /generate-ui-service-card              │
│          /apply-api-spec                        │
│          /apply-experience-card                 │
│          /implement-feature                     │
│          /verify-feature                        │
└───────────────────┬─────────────────────────────┘
                    │
┌───────────────────▼─────────────────────────────┐
│  ADOBE PROVIDES — PER FEATURE                   │
│  Experience Card   written by Adobe PdM         │
│  API Spec          written by Adobe Engineer    │
└───────────────────┬─────────────────────────────┘
                    │  you receive, validate, then →
┌───────────────────▼─────────────────────────────┐
│  YOU DO — ONE TIME PER PROJECT                  │
│  Generate Backend Service Card                  │
│  Generate UI Service Card                       │
└───────────────────┬─────────────────────────────┘
                    │
                    ▼  (repeat for each feature)
┌─────────────────────────────────────────────────┐
│  YOU DO — PER FEATURE                           │
│  Review Experience Card & API Spec              │
│  Generate Backend LLD                           │
│        → Review & approve                       │
│  Generate UI LLD                                │
│        → Review & approve                       │
│  Generate Code                                  │
│  Verify Feature                                 │
└─────────────────────────────────────────────────┘
```

---

## Reference Example

Throughout this manual, we use the **Anytime Upgrade** feature as the running example. It demonstrates a partner upgrading a customer subscription mid-term with prorated pricing — a realistic, end-to-end scenario that touches all phases of the workflow.