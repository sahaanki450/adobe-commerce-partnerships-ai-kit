# FR-1 — Backend Service Card Generation

**Who runs this:** Engineer  
**Frequency:** Once per project onboarding, then again after significant codebase restructuring  
**Prerequisite:** None — this is the first step in onboarding a project

---

## What This Step Does

This step scans your backend codebase and produces a set of **service card files** — structured documents that describe how your backend is coded. These cards are not documentation for humans to read casually; they are precision inputs that the AI reads before generating any code for your project.

The cards capture things like:
- How your project names controllers, models, and route handlers
- How auth tokens are injected into outbound API calls
- What external dependencies your service uses, and how they behave on failure
- Your database schema and entity relationships
- Your error handling and validation patterns

Every piece of backend code the toolkit generates will follow the patterns in these cards. If the cards are accurate, generated code will look like it was written by a member of your team. If the cards are inaccurate, generated code will have subtle pattern mismatches that compound across the codebase.

---

## Before You Run

- You must have access to the backend repository path on your machine.
- Your AI coding agent (e.g. Claude Code) must be installed, configured, and running. See [Agent Setup](../README.md#agent-setup).
- The backend repo should be in a stable state — avoid running this during an active large refactor, as the scan will capture in-progress patterns.

---

## Running the Skill

Open your AI coding agent in this AI Kit's root directory and run:

```
/generate-backend-service-card <path-to-backend-repo>
```

**Example:**

```
/generate-backend-service-card ../my-partner-backend
```

The skill will scan your backend repo across four phases:
1. **Locate & Scaffold** — identifies the project structure and creates output files
2. **Code Scan** — reads controllers, models, routes, connectors, and validation patterns
3. **Config Scan** — reads build config, environment variables, secrets, and deployment config
4. **Assembly** — writes all findings into the structured card files

This will take a few minutes depending on the size of your repo. Do not interrupt it.

---

## What Gets Produced

The skill produces **9 files** (8 cards plus a changelog) at:

```
<path-to-backend-repo>/docs/ai-kit/service-cards/backend/
```

### Card Files

| File | What it captures |
|---|---|
| `SERVICE_CARD.md` | Identity: what the service owns, who runs it, its external surface at a glance, fragile areas, recent changes. The file an engineer reads first. |
| `MODULE_INDEX.md` | Capability → implementation mapping: which classes, connectors, events, and flags implement each business capability, with Key Decisions per capability |
| `CONTRACTS.md` | The service's full API surface: HTTP operations it exposes and async events it consumes and publishes, with request/response shapes and auth |
| `CONNECTORS.md` | Every outbound dependency the service calls: auth, transport config, resilience settings, error-handling posture |
| `BUILD_CONFIG.md` | Technology choices and major dependency categories — exact coordinates, property keys, and secret paths |
| `CODE_PATTERNS.md` | Team coding conventions: naming, package layout, DTO patterns, error handling, validation, test approach |
| `PLATFORM.md` | Deployment topology, monitoring, feature flags, caching, database connection, active migrations, runbooks |
| `DB_SCHEMA.md` | Entity-to-table mapping, columns, relationships, migrations. Omitted if the service is stateless. |
| `CHANGELOG.md` | Append-only log of each scan run — source repo, commit hash, scans completed. Do not edit this manually. |

---

## Reviewing the Cards — Decision Gate DG-1

**Do not proceed to any feature workflow step until you have reviewed the cards.**

Open the [Decision Gate Guide — DG-1](../decision-gates.md#dg-1--backend-service-card-review) and complete the review checklist. Pay particular attention to:

- `CODE_PATTERNS.md` — if this is wrong, every generated file will have pattern mismatches
- `CONNECTORS.md` — if auth or retry behavior is wrong, generated API calls will be incorrect
- `MODULE_INDEX.md` — if capabilities are missing or wrong, the AI may re-implement things that already exist

Edit the card files directly to correct any inaccuracies. Do not re-run the generator to fix small errors — edit the files and commit the changes.

---

## When to Re-Run vs. When to Edit

The cards are meant to be maintained over time, not regenerated from scratch each time something changes. See the [Reference guide's Re-Run vs. Edit table](../reference.md#re-run-vs-edit-decision-table) for backend-card scenarios.

When you re-run, the generator overwrites the existing card files. Back up any manual edits first, then apply them on top of the re-generated output.

---

## After This Step

Proceed to **[UI Service Card Generation](ui-service-card.md)** to produce the equivalent cards for your frontend codebase.

Once both the Service cards are complete and reviewed, your project is fully onboarded. You can then run the feature workflow for any feature.