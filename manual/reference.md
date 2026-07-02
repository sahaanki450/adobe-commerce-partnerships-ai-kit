# Reference

Quick-lookup tables for recurring use. No prose — just scannable facts.

---

## Skills Quick Reference

| Skill | Command | Inputs required | Output location |
|---|---|---|---|
| Generate Backend Service Card | `/generate-backend-service-card <targetRepoPath>` | Backend repo path | `<targetRepo>/docs/ai-kit/service-cards/backend/` |
| Generate UI Service Card | `/generate-ui-service-card <targetRepoPath>` | Frontend repo path | `<targetRepo>/docs/ai-kit/service-cards/ui/` |
| Generate Backend LLD | `/apply-api-spec <targetRepoPath> <featureSpecPath>` | Feature spec + API spec + backend service cards | `<targetRepo>/docs/ai-kit/LLD/backend/<feature-spec-filename>-lld.md` |
| Generate UI LLD | `/apply-experience-card <featureSpecPath> <targetRepoPath> <backendLLDPath>` | Experience card + backend LLD (or `none`) + UI service cards | `<targetRepo>/docs/ai-kit/LLD/ui/<experience-card-filename>-lld.md` |
| Generate Code | `/implement-feature <featureName> <targetRepoPath> [--mode=both\|backend\|ui] [--use-reference-files]` | Both LLDs + both service card sets | `<targetRepo>` (backend + UI files) |
| Verify Feature | `/verify-feature <featureName> <targetRepoPath> [--mode=both\|backend\|ui] [--use-reference-files]` | Generated code + both LLDs + experience card | Read-only — pass/partial/fail report |

---

## Artifact Directory Map

**In this kit repo** — feature inputs, plus the pre-approved reference files used when a skill is run with `--use-reference-files`:

```
feature-specs/
└── <featureName>/
    ├── experience-card/
    │   └── <feature-name>.md
    ├── APISpec/
    │   └── <feature-name>-apispec.md
    └── reference-files/
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
        │   │   ├── CHANGELOG.md
        │   │   ├── CONNECTOR_STANDARDS.md
        │   │   ├── EVENT_STANDARDS.md
        │   │   ├── LOGGING_STANDARDS.md
        │   │   └── API_DOCS_STANDARDS.md
        │   └── ui/
        │       ├── UI_SERVICE_CARD.md
        │       ├── UI_MODULE_INDEX.md
        │       ├── ROUTES.md
        │       ├── DATA_LAYER.md
        │       ├── STATE_MANAGEMENT.md
        │       ├── UI_CODE_PATTERNS.md
        │       └── UI_PLATFORM.md
        └── LLD/
            ├── backend/
            │   └── <feature-name>-lld.md
            └── ui/
                └── <feature-name>-lld.md
```

**In your target repo** — where generated service cards and LLDs land when you run the skills against your own codebase (i.e. without `--use-reference-files`):

```
<targetRepo>/
└── docs/
    └── ai-kit/
        ├── service-cards/
        │   ├── backend/    (same file set as above)
        │   └── ui/         (same file set as above)
        └── LLD/
            ├── backend/
            │   └── <feature-name>-lld.md
            └── ui/
                └── <feature-name>-lld.md
```

---

## File Naming Conventions

| Artifact | Naming pattern | Example |
|---|---|---|
| Feature name (directory) | camelCase | `anytimeUpgrade` |
| Experience card | kebab-case + `.md` | `anytime-upgrade.md` |
| API spec | kebab-case + `-apispec.md` | `anytime-upgrade-apispec.md` |
| Backend LLD | kebab-case + `-lld.md` | `anytime-upgrade-lld.md` |
| UI LLD | kebab-case + `-ui-lld.md` | `anytime-upgrade-ui-lld.md` |
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
| Generated code has 1-2 lint errors | Fix manually | Not worth a full re-run |
| Generated code has pervasive type errors across many files | Fix the LLD, re-run `/implement-feature` | Systemic issue traces back to LLD |
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

### During code generation

| Error | Likely cause | Fix |
|---|---|---|
| TypeScript: property does not exist on type | Response type in LLD does not match actual API response shape | Fix field name in LLD, re-run |
| TypeScript: argument not assignable | Request type mismatch between backend LLD and UI LLD | Align the two LLDs, re-run |
| Lint: import order | Generated imports not sorted per your lint config | Fix manually; add lint rule note to `CODE_PATTERNS.md` |
| Playwright: component throws on render | Unhandled null/undefined in a generated component | Fix in generated file; check if LLD was missing a null state |
| Generated file at wrong path | Service card `CODE_PATTERNS.md` had wrong directory path | Fix the service card, re-run |

---

## Workflow Prerequisites Checklist

Use this before starting each phase. Spec-quality checks are owned by the [Decision Gate Guide](./decision-gates.md) — this list only covers what that guide doesn't: environment and git-state prerequisites.

### Before running Backend and UI Service Card Generation skills
- [ ] Claude Code is installed and running
- [ ] You have access to the target repo path
- [ ] The target repo is in a stable, non-mid-refactor state

### Before validating the Experience Card and API Spec
- [ ] DG-1 and DG-2 complete (see Decision Gate Guide)
- [ ] You have read the Adobe VIPMP Partners API docs for this feature
- [ ] A Figma design exists (or you have explicitly noted it does not)

### Before running Generation of Backend LLD
- [ ] Feature Experience card and API spec are validated (see above)
- [ ] Backend service cards exist and are up to date

### Before running Generation of UI LLD
- [ ] DG-3 complete (see Decision Gate Guide)
- [ ] Feature Experience card is finalized (no open questions)
- [ ] UI service cards exist and are up to date

### Before running Code Generation Skill
- [ ] DG-4 complete (see Decision Gate Guide)
- [ ] Feature branch created in backend repo
- [ ] Feature branch created in frontend repo
- [ ] Both repos have no uncommitted changes

### Before running Verify Feature
- [ ] Code generation has completed and reported done
- [ ] Dev server can be started in the target repo(s)