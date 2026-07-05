# Reference

Quick-lookup tables for recurring use. No prose — just scannable facts.

---

## Skills Quick Reference

| Skill | Command | Inputs required | Output location |
|---|---|---|---|
| Generate Backend Service Card | `/generate-backend-service-card <repo-path>` | Backend repo path | `<repo-path>/docs/ai-kit/service-cards/backend/` |
| Generate UI Service Card | `/generate-ui-service-card <repo-path>` | Frontend repo path | `<repo-path>/docs/ai-kit/service-cards/ui/` |
| Generate Backend LLD | `/apply-api-spec <backend-repo-path> <feature-spec-dir>` | Feature spec directory (experience card + API spec) + backend service cards | `<backend-repo-path>/docs/ai-kit/LLD/backend/` |
| Generate UI LLD | `/apply-experience-card <experience-card-path> <frontend-repo-path> <backend-lld-path>` | Experience card + backend LLD (or `none`) + UI service cards | `<frontend-repo-path>/docs/ai-kit/LLD/ui/` |
| Generate Code | `/implement-feature <featureName> <targetRepoPath> [--mode] [--use-reference-files]` | Both LLDs + both service card sets | Your target repo |
| Verify Feature | `/verify-feature <featureName> <targetRepoPath> [--mode] [--use-reference-files]` | Both LLDs + experience card + generated code | Read-only — produces a pass/partial/fail report |

---

## Artifact Directory Map

**In this AI Kit repo** — Adobe-provided inputs, plus optional pre-approved reference files for trying the kit against `adobe-commerce-partnerships-ref-app`:

```
feature-specs/
└── <featureName>/
    ├── experience-card/
    │   └── <feature-name>.md
    ├── APISpec/
    │   └── <feature-name>-apispec.md
    └── reference-files/              (only present for reference features)
        ├── service-cards/
        │   ├── backend/
        │   │   ├── SERVICE_CARD.md
        │   │   ├── MODULE_INDEX.md
        │   │   ├── CONTRACTS.md
        │   │   ├── CONNECTORS.md
        │   │   ├── BUILD_CONFIG.md
        │   │   ├── CODE_PATTERNS.md
        │   │   ├── PLATFORM.md
        │   │   ├── DB_SCHEMA.md
        │   │   └── CHANGELOG.md
        │   └── ui/
        │       ├── UI_SERVICE_CARD.md
        │       ├── UI_MODULE_INDEX.md
        │       ├── ROUTES.md
        │       ├── DATA_LAYER.md
        │       ├── STATE_MANAGEMENT.md
        │       ├── UI_CODE_PATTERNS.md
        │       ├── UI_PLATFORM.md
        │       └── CHANGELOG.md
        └── LLD/
            ├── backend/
            │   └── <feature-name>-lld.md
            └── ui/
                └── <feature-name>-lld.md
```

**In your target repo** — where generated service cards and LLDs actually live for your own project:

```
<targetRepo>/docs/ai-kit/
├── service-cards/
│   ├── backend/   (same 9 files as above)
│   └── ui/        (same 8 files as above)
└── LLD/
    ├── backend/
    │   └── <featureName>-lld.md
    └── ui/
        └── <featureName>-lld.md
```

---

## File Naming Conventions

| Artifact | Naming pattern | Example |
|---|---|---|
| Feature name (directory) | camelCase | `anytimeUpgrade` |
| Experience card | kebab-case + `.md` | `anytime-upgrade.md` |
| API spec | kebab-case + `-apispec.md` | `anytime-upgrade-apispec.md` |
| Backend LLD | kebab-case + `-lld.md` | `anytime-upgrade-lld.md` |
| UI LLD | kebab-case + `-lld.md` (same name as backend, distinguished by the `backend/`/`ui/` directory) | `anytime-upgrade-lld.md` |
| Service card files | SCREAMING_SNAKE_CASE + `.md` | `SERVICE_CARD.md`, `CODE_PATTERNS.md` |

---

## Finding a Figma Node ID

1. Open the Figma file
2. Click the specific frame or component you want to reference
3. Right-click → **Copy link**
4. The copied URL ends with `?node-id=XX%3AYY` — that is the node ID

Example URL with node ID:
```
https://www.figma.com/file/AbCdEfGh/feature-name?node-id=12%3A345
```

Paste this full URL into the `## Figma Design` section of the experience card.

If you link to the file root (no `?node-id=...`), the AI will fetch the entire file and may not find the correct frame.

---

## Re-Run vs. Edit Decision Table

| Situation | Decision | Reason |
|---|---|---|
| Single field name wrong in a service card | Edit the card | Faster; re-run overwrites all edits |
| One missing route in `ROUTES.md` | Edit the card | Targeted fix |
| Auth mechanism described incorrectly in `CONNECTORS.md` | Edit the card | Well-scoped change |
| New external API was added to the backend | Edit `CONNECTORS.md` | Add the new connector entry |
| Major framework upgrade changed the entire code structure | Re-run the service card generator | Too many changes to edit manually |
| LLD has a wrong file path | Edit the LLD | Small, targeted fix |
| LLD has a wrong data flow (steps out of order, missing step) | Edit the LLD | Do not re-run; you would lose other edits |
| LLD architecture is fundamentally wrong (wrong pattern, wrong structure) | Fix the source input, then re-run | Root cause is in the API spec or service cards |
| Generated code has 1-2 minor logic errors | Fix manually | Not worth a full re-run |
| Generated code has pervasive wiring or acceptance-criteria gaps across many files | Fix the LLD, re-run `/implement-feature` | Systemic issue traces back to LLD |
| PM requests a UI design change after code generation | Update experience card → update UI LLD → re-run `/implement-feature` | Change must be tracked from the source |

---

## Common Errors and What to Do

### During service card generation

| Error | Likely cause | Fix |
|---|---|---|
| Skill cannot find route definitions | Non-standard routes directory | Point the skill to the correct directory in the prompt |
| Generated `CODE_PATTERNS.md` has empty sections | Source code uses non-standard patterns the scanner did not recognize | Fill in those sections manually after generation |
| `CONNECTORS.md` missing a known external dependency | Connector uses an unconventional import or wrapper | Add the connector entry manually |

### During LLD generation

| Error | Likely cause | Fix |
|---|---|---|
| LLD references a file path that does not exist | Service card has wrong directory structure | Fix `CODE_PATTERNS.md` in the service card, re-run |
| LLD proposes creating a utility that already exists | Service card `MODULE_INDEX.md` is missing that capability | Add the capability to `MODULE_INDEX.md`, re-run |
| LLD has a data flow that contradicts the API spec | API spec is ambiguous or incorrect | Fix the API spec, re-run |
| LLD is missing error handling for some API error codes | API spec does not list those error codes | Add the error codes to the API spec, re-run |

### During code generation (wiring check)

| Error | Likely cause | Fix |
|---|---|---|
| Data fetch unit endpoint URL doesn't match the server handler path | Backend LLD and UI LLD are inconsistent | Align the two LLDs, re-run |
| A query or dispatch param the server requires isn't sent from the UI | UI LLD's fetch hook spec is missing that param | Fix the UI LLD, re-run |
| Response field accessed in the UI doesn't exist in the backend response | Request/response shape mismatch between LLDs | Align the two LLDs, re-run |
| Acceptance criterion not satisfied by any component, hook, or route | Criterion wasn't addressed in either LLD's Change Summary | Fix the LLD, re-run |
| Generated file at wrong path | Service card `CODE_PATTERNS.md` / `UI_CODE_PATTERNS.md` had wrong directory path | Fix the service card, re-run |