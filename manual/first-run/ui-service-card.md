# FR-2 — UI Service Card Generation

**Who runs this:** Engineer  
**Frequency:** Once per project onboarding, then again after significant frontend restructuring  
**Prerequisite:** Backend service cards exist and have been reviewed

---

## What This Step Does

This step scans your frontend codebase and produces a set of **UI service card files** — structured documents that describe how your frontend is coded. These cards are the frontend equivalent of the backend service cards.

The cards capture things like:
- Your route structure, including which routes require auth and which stores they need
- How fetch hooks are structured, including how auth tokens are injected
- Which state management stores exist, what they own, and their initialization order
- Your component naming conventions, directory structure, and design system usage
- Your runtime stack, build scripts, and environment variables

Every piece of frontend code the toolkit generates will follow the patterns in these cards. The consistency between the generated code and your existing frontend depends entirely on the accuracy of these cards.

---

## Before You Run

- You must have access to the frontend repository path on your machine.
- Your AI coding agent (e.g. Claude Code) must be installed, configured, and running. See [Agent Setup](../README.md#agent-setup).
- The frontend repo should be in a stable state.

---

## Running the Skill

Open your AI coding agent in this AI Kit's root directory and run:

```
/generate-ui-service-card <path-to-frontend-repo>
```

**Example:**

```
/generate-ui-service-card ../my-partner-frontend
```

The skill scans your frontend repo across four phases:
1. **Locate & Scaffold** — detects the framework, router, state library, and data-fetching library, then builds a codebase map
2. **Code Scan** — reads routes, auth guards, fetch units, state stores, components, forms, and design system usage (plus WebSocket, i18n, or analytics patterns if present)
3. **Config Scan** — reads the package manifest, framework/build config, env vars, and README
4. **Assembly** — writes all findings into the structured card files

---

## What Gets Produced

The skill produces **8 files** (7 cards plus a changelog) at:

```
<path-to-frontend-repo>/docs/ai-kit/service-cards/ui/
```

| File | What it captures |
|---|---|
| `UI_SERVICE_CARD.md` | Identity, boundaries, data contracts summary, companion file index. The file an engineer reads first. |
| `UI_MODULE_INDEX.md` | Capability catalogue — every feature mapped to its page/component/hook/store, with Key Decisions and call graphs |
| `ROUTES.md` | Full route registry, auth guard mechanism, navigation patterns, route constants |
| `DATA_LAYER.md` | Every fetch unit the app calls: endpoint, cache key, auth injection, dependency guard, return shape, mutation pattern, shared HTTP client |
| `STATE_MANAGEMENT.md` | Store shapes, actions, init order, browser storage keys, server data cache strategy |
| `UI_CODE_PATTERNS.md` | Naming conventions, directory structure, real code excerpts per pattern category, design system map, test skeletons |
| `UI_PLATFORM.md` | Runtime stack, build scripts, key dependencies, env vars, feature flags, accessibility, monitoring |
| `CHANGELOG.md` | Append-only log of each scan run — source repo, commit hash, scans completed. Do not edit this manually. |

---

## Reviewing the Cards — Decision Gate DG-2

**Do not proceed to any feature workflow step until you have reviewed the UI cards.**

Open the [Decision Gate Guide — DG-2](../decision-gates.md#dg-2--ui-service-card-review) and complete the review checklist.


---

## When to Re-Run vs. When to Edit

The cards are meant to be maintained over time, not regenerated from scratch each time something changes. See the [Reference guide's Re-Run vs. Edit table](../reference.md#re-run-vs-edit-decision-table) for UI-card scenarios.

When re-running, the generator overwrites existing card files. Back up manual edits first, then reapply them.

---

## After This Step

Your project is now fully onboarded. Both backend and UI service cards exist, have been reviewed, and are committed to the repo.

Proceed to **[Reviewing the Experience Card](../feature-workflow/review-experience-card.md)** to begin the first feature workflow.