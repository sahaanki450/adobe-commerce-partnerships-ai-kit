# Generating Code

**Frequency:** Once per feature (re-runnable if needed)  
**Prerequisites:**
- Backend and Frontend Service cards are complete and reviewed
- Backend LLD is approved
- UI LLD is approved

---

## What This Step Does

This step uses the AI to generate working implementation code for both the backend and frontend of your target repo, following the approved LLDs and the patterns documented in your service cards.

Unlike the LLD steps, this step writes real code directly into your project repo. The AI generates all files listed in both LLDs, then runs a wiring check to confirm the UI and backend actually connect correctly.

You do not need to direct the AI through the generation — it works through the LLDs systematically. Your job at this step is to:
1. Run the command
2. Monitor for gaps flagged in the wiring check
3. Complete a standard code review of the output

---


## Running the Skill

Open your AI coding agent in this AI Kit's root directory and run:

```
/implement-feature <featureName> <targetRepoPath> [--mode=both|backend|ui] [--use-reference-files]
```

**Example:**

```
/implement-feature anytime-upgrade ../my-partner-app
```

| Flag | Default | Description |
|---|---|---|
| `--mode` | `both` | `--mode=backend` or `--mode=ui` to generate only one side |
| `--use-reference-files` | off | Loads LLDs and service cards from this kit's pre-approved reference location instead of the target repo. Only use this when the target repo is **adobe-commerce-partnerships-ref-app**; for your own project's real features, leave it off. |

The skill runs in 5 phases:

| Phase | What happens |
|---|---|
| 1 — Load reference material | Reads the experience card, both LLDs, and the relevant service cards |
| 2 — Generate backend code | Creates models, controllers, API routes, constants — skipped for `--mode=ui` |
| 3 — Generate UI code | Creates fetch hooks, page components, sub-components, state/style files — skipped for `--mode=backend` |
| 4 — Wiring check | Cross-checks contracts between backend and UI (URLs, params, request/response shapes, auth), then verifies every acceptance criterion in the experience card is satisfied — skipped for `--mode=backend` or `--mode=ui` |
| 5 — Report | Prints created/modified files, reuse applied, wiring verification, and experience card coverage |

This will take several minutes. Do not interrupt it between phases.

---

## Verify the Feature

Once the skill completes without unresolved gaps, run `/verify-feature` before opening a PR — it audits every generated file against the LLDs bullet-by-bullet and walks through a browser checklist derived from the experience card.

---

## What Gets Generated

All files are written into your target repo, at the paths documented in your service cards' `CODE_PATTERNS.md` / `UI_CODE_PATTERNS.md`.

### Backend files:

| Type | Location (follows your service card paths) |
|---|---|
| Data models | `src/models/<feature>/` |
| Controllers | `src/controllers/<feature>/` |
| Route definitions | `src/routes/<feature>/` |
| Constants | `src/constants/<feature>/` |

### UI files:

| Type | Location (follows your service card paths) |
|---|---|
| Fetch hooks / data fetch units | `src/hooks/<feature>/` |
| Page components | `src/pages/<feature>/` |
| Sub-components | `src/components/<feature>/` |
| Style files | Co-located with components |
| State store updates | In the relevant store file (modified, not replaced) |

Exact paths will match the directory structure documented in your service cards. If the service cards are accurate, the generated files will land in the right places.

---

## Handling Wiring and Coverage Gaps

After generating code, the skill runs a wiring check (Phase 4): it cross-checks every data flow between the UI and backend LLDs — endpoint URLs, query params (including dispatch/routing params), request/response field names, and auth forwarding — and separately verifies that every acceptance criterion and user-facing surface in the experience card is satisfied by the generated implementation. It fixes every mismatch it finds before writing the final report.

If the Phase 5 report still shows unresolved gaps, here is how to handle the most common cases:

**Wiring mismatch (URL, param, or shape):**

Usually caused by an inconsistency between the backend LLD and UI LLD — e.g. the UI fetch hook was specified against a field name the backend LLD doesn't produce. Fix the inaccuracy in whichever LLD is wrong and re-run.

**Acceptance criterion not covered:**

Means no generated component, hook, or route satisfies that criterion. Check whether the criterion was addressed in the relevant LLD's Change Summary — if it's missing from the LLD, that's the root cause; fix the LLD and re-run.

**If gaps are pervasive (more than 3-4 files affected):**

Stop, do not try to fix them individually. The root cause is almost always inaccurate service cards or an inconsistency between the backend LLD and UI LLD. Identify the inaccuracy, fix it in the source (service card or LLD), and re-run.

---

## Re-Running the Skill

`/implement-feature` is safe to re-run. It overwrites generated files with fresh output. If you have made manual edits to generated files that you want to keep, save them before re-running. Use `--mode=backend` or `--mode=ui` to re-run just one side if only one LLD changed.

See the [Reference guide's Re-Run vs. Edit table](../reference.md#re-run-vs-edit-decision-table) for when a re-run is warranted vs. a manual fix.

---

## Code Review

After the skill completes successfully, create a pull request on your target repo and run it through your team's standard code review process. The LLD approval already covered design — this review is about correctness. If the design turns out to be wrong, update the experience card and UI LLD and regenerate, rather than redesigning in review.

---

## After Code Review

Once the PR is merged, the feature workflow is complete.