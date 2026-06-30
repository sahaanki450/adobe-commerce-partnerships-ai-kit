# Setting Up Claude Code

This kit is coding-agent agnostic. The skills and content live in root-level directories.
To use the kit with **Claude Code**, run the one-time setup below to wire those directories
into Claude Code's expected `.claude/` structure.

---

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- This repo cloned locally

---

## Setup

Run these commands from the root of this repo:

```bash
# 1. Create the .claude directory
mkdir -p .claude

# 2. Copy the skills into .claude
cp -r skills .claude/skills

# 3. Start Claude Code
claude
```

---

## Re-syncing after kit updates

If you pull updates to the `skills/` directory, re-copy them:

```bash
rm -rf .claude/skills && cp -r skills .claude/skills
```

---
