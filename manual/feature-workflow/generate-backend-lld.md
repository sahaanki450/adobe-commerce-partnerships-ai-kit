# Generating the Backend LLD

**Frequency:** Once per feature  
**Prerequisites:**
- Backend service card is complete and reviewed
- API spec exists
- Feature Experience Card exists

---

## What This Step Does

This step uses the AI to generate a **Backend Low-Level Design (LLD)** — a precise engineering plan for all backend changes required to implement the feature.

The LLD is not generated code. It is a structured document that tells your engineers exactly what to build: which files to create or modify, what each function does, what data flows from where to where, and which existing code to reuse.

The AI produces this by reading three inputs together:
1. Your API spec — what the external API can do
2. Your backend service cards — how your backend is coded
3. Your experience card — what the feature must accomplish

**The LLD is your last design checkpoint before code is generated.** Treat LLD review with the same rigor as a design review. Approving the LLD means you own the design it describes.

---

## Running the Skill

Open your AI coding agent in this AI Kit's root directory and run:

```
/apply-api-spec <path-to-backend-repo> <path-to-feature-spec>
```

**Example:**

```
/apply-api-spec ../my-partner-backend feature-specs/anytime-upgrade
```

The skill reads the feature spec, locates and reads the matching API spec, and reads your service cards, then reasons about the implementation and writes the LLD. It will pause and show you the output before writing the file — review it before confirming.

---

## What Gets Produced

The skill produces a single file at:

```
<path-to-backend-repo>/docs/ai-kit/LLD/backend/<feature-spec-filename>-lld.md
```

The LLD contains 7 sections:

| Section | What it contains |
|---|---|
| **Summary** | One-paragraph description of the backend changes |
| **Data Flow** | Step-by-step call chain per operation, from trigger to response |
| **Change Summary** | Table of files to create or modify, with specific changes for each — cross-checked against your service cards to prefer reusing existing files over creating new ones |
| **DB Changes** | Schema or migration changes required, or an explicit note that the service is stateless |
| **Design Decisions** | Non-obvious choices and the reasoning behind them |
| **Acceptance Criteria Coverage** | How each acceptance criterion from the experience card is met |
| **Out of Scope** | What the backend explicitly will not handle in this implementation |

---

## Reviewing the LLD — Decision Gate DG-3

**Do not proceed to generating the UI LLD until you have completed the DG-3 review.**

Open the [Decision Gate Guide — DG-3](../decision-gates.md#dg-3--backend-lld-review) for the full review checklist.

The most critical things to verify:

**Data flow accuracy.** Read the data flow diagram step by step. Would you design it this way if writing from scratch? If any step looks wrong — wrong API call sequence, wrong data transformation, missing a step — fix it in the LLD before proceeding.

**File list accuracy.** Check the list of files to create or modify. Are the paths correct for your project? Is anything missing? Does it propose creating a new file for something that already exists?

**Reuse decisions.** The LLD will identify existing code to reuse. Verify each entry — does the referenced code actually exist, and does it do what the LLD says? If the LLD says "reuse the `fetchWithAuth` utility" and your project calls it `authenticatedFetch`, the code generation will fail.

**Design decisions.** Read each design decision and decide if you agree. If you disagree, change the LLD. The AI made these decisions based on your service cards and API spec — if either was inaccurate, the decisions may be wrong.

---

## Editing the LLD

Edit the LLD file directly to make corrections. The LLD is a plain markdown document — you can change anything in it.

Typical edits:
- Correct a file path that points to the wrong location
- Change a function name to match an existing one
- Remove a "create new utility" entry and replace it with "reuse existing X"
- Add a missing error handling step to the data flow
- Revise a design decision you disagree with

Once you have made edits, review the complete LLD again from the top before approving. Do not approve a LLD you have only partially read.

---

## What Happens If the LLD Is Fundamentally Wrong

If the LLD has structural problems (wrong data flow, wrong architecture, major gaps), do not try to fix it by editing. Instead:

1. Review your API spec and service cards for inaccuracies that may have caused the wrong output
2. Fix the inaccurate input
3. Re-run `/apply-api-spec` to regenerate the LLD

Re-running overwrites the existing LLD. If you have made partial edits you want to keep, copy them before re-running.

---

## After This Step

Proceed to **[Generating the UI LLD](generate-ui-lld.md)**.
