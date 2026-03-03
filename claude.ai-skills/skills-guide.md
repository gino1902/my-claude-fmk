# Skills Guide

> Claude Desktop | Windows 11 | WSL2
> Version 1.0

---

## Overview

Skills are reusable instruction sets that tell Claude how to behave for a specific type of task. Instead of re-explaining your standards every conversation, you install a skill once and Claude applies it automatically whenever it's relevant.

This guide covers:

- How skills work technically
- The persistence model — what survives and what doesn't
- How to install and manage skills
- How to commission new skills for your project using the skill-proposal interview
- Skill archetypes: BA, UI, Coder, QA

---

## Part 1 — How Skills Work

### Anatomy of a skill

A skill is a folder containing a `SKILL.md` file. That file has two parts:

```
---
name: technical-verification
description: When to trigger and what the skill does. Claude reads this
             to decide whether to consult the skill for a given task.
---

# Body
Instructions Claude follows when the skill is active.
```

The `description` is the trigger mechanism. Claude reads all installed skill descriptions at the start of a conversation and decides which ones are relevant to the current task. A well-written description makes the skill trigger reliably. A vague one means Claude ignores it.

### What skills can contain

```
skill-name/
├── SKILL.md              ← required, always loaded when skill triggers
└── references/           ← optional, loaded on demand
    ├── checklist.md
    └── examples.md
```

Keep `SKILL.md` focused. If you have large reference material (checklists, examples, templates), put them in a `references/` subfolder and point to them from the body.

### How Claude decides to use a skill

Claude matches your request against skill descriptions. Skills trigger reliably on **complex, multi-step, or specialised tasks** — not simple one-liners. A request like "review this requirements document for completeness and flag gaps" will trigger a `ba-requirements` skill. A request like "summarise this paragraph" won't trigger anything, and shouldn't.

This means: **write skill descriptions to match the real prompts you use**, not abstract definitions.

---

## Part 2 — The Persistence Model

Understanding what persists is essential. There are four layers:

```
Container (ephemeral)
│  Claude's Linux workspace. Resets after every session.
│  Files created here disappear unless explicitly downloaded.
│  This is where Claude writes code, docs, and .skill files.
│
↓ Download button (present_files tool)
│
Windows filesystem (durable)
│  Your machine. Downloads folder, repo, anywhere you save files.
│  Survives session resets. This is your ownership layer.
│
↓ Settings → Extensions → Install Extension (.skill file)
│
Claude Desktop Extensions (persistent)
│  Skills installed here load in every conversation automatically.
│  Stored on your Windows machine, managed by Claude Desktop.
│  Survives session resets. Does NOT survive Claude Desktop reinstall
│  unless you reinstall your .skill files.
│
↓ git commit + push
│
GitHub repo (most durable)
└─ Version-controlled. Survives reinstalls, new machines, team sharing.
   The source of truth for all your skills.
```

### Practical rule

**Anything you want to keep goes to GitHub.** The download button gets it to Windows. The Install Extension step activates it. The git commit makes it permanent.

### Recommended repo structure for skills

```
/skills
  technical-verification.skill
  ba-requirements.skill
  unit-test.skill
  ui-review.skill
```

Commit `.skill` files, not the raw folders. The `.skill` file is a packaged archive — it's what Claude Desktop installs.

---

## Part 3 — Installing and Managing Skills

### Installing a skill

1. Download the `.skill` file (via the download button in Claude)
2. In Claude Desktop: **Settings → Extensions → Advanced settings**
3. Under **Extension Developer**, click **Install Extension…**
4. Select the `.skill` file
5. Confirm — the skill is now active in all conversations

### Updating a skill

Skills cannot be edited in place. To update:

1. Build the revised skill (new `SKILL.md`, repackage)
2. Download the new `.skill` file
3. In Claude Desktop Extensions, remove the old version
4. Install the new `.skill` file

### Committing a skill to your repo

```bash
# In WSL2
mkdir -p ~/content-presentations/skills
cp /mnt/c/Users/<you>/Downloads/skill-name.skill ~/content-presentations/skills/
git add skills/skill-name.skill
git commit -m "add skill-name skill"
git push origin main
```

Do this after every new skill install. If you reinstall Claude Desktop, your `.skill` files are in the repo — reinstallation takes under a minute.

---

## Part 4 — The Skill-Proposal Interview

When starting a new project, Claude can propose a prioritised skill backlog — but only if it has the right inputs. Generic analysis of project files produces generic proposals. Targeted questions produce actionable ones.

### Prerequisites

Before running the interview, your project must have:

- `FRAMING.md` — including the **strategic deliverable** (what gets produced, for whom, for what purpose). Without this, Claude cannot assess what skills are relevant.
- `CONSTITUTION.md` — including the **operational deliverable spec** (format, theme, export, quality constraints).

> ⚠️ If FRAMING.md does not include a strategic deliverable statement, stop and add one before proceeding. The interview cannot produce useful output without it.

### How to trigger the interview

In your Claude Project, after syncing your repo, prompt:

```
Read FRAMING.md and CONSTITUTION.md. Then run the skill-proposal 
interview to help me identify which skills to build for this project.
```

### What Claude does

1. Reads FRAMING.md to understand strategic intent and deliverable type
2. Reads CONSTITUTION.md to understand operational constraints and quality bar
3. Asks you 3–4 targeted questions (see below)
4. Proposes a prioritised skill backlog with rationale

### The interview questions

Claude will ask variants of these, tailored to your project context:

**1. Repeated tasks**
"What tasks do you find yourself doing manually on every generation or every document? Examples: reformatting content, checking slide counts, verifying terminology."

**2. Quality failures**
"Where do your deliverables most often come back with feedback or corrections? What gets flagged?"

**3. Alignment risks**
"What's the most common way a generated output misses the mark — tone, structure, audience, claims?"

**4. Technical checks**
"Are there any technical standards, naming conventions, or compliance rules that outputs must meet?"

### What good proposals look like

A good skill proposal includes:

- **Skill name** — short, specific
- **Trigger context** — what request or task activates it
- **What it prevents** — the failure mode it addresses
- **Priority** — based on frequency and cost of the failure

Example output:

| Priority | Skill                 | Trigger                                  | Prevents                                    |
|:--------:|:--------------------- |:---------------------------------------- |:------------------------------------------- |
| 1        | `framing-alignment`   | Before any generation                    | Content that contradicts FRAMING objectives |
| 2        | `slide-density-qa`    | After generation                         | Slides with too much text for the audience  |
| 3        | `ba-requirements`     | When writing requirements sections       | Incomplete or untestable requirements       |
| 4        | `financial-narrative` | Slides containing numbers or projections | Inconsistent figures, unsupported claims    |

---

## Part 5 — Skill Archetypes

These are the most common skill types across projects. Treat these as starting points — each should be built from your specific workflow gaps, not copied generically.

### BA — Business Analyst

**Purpose:** Ensures requirements, objectives, and problem statements are complete, unambiguous, and testable.

**Typical triggers:**

- "Review this requirements section"
- "Check if these objectives are measurable"
- "Flag any assumptions in this document"

**What it prevents:** Requirements that are too vague to act on, missing acceptance criteria, unstated assumptions that become misalignments later.

**Build it when:** Deliverables regularly come back with "this isn't specific enough" or "how will we measure this."

---

### UI Reviewer

**Purpose:** Evaluates UI descriptions, mockup notes, or slide layouts against a defined visual and UX standard.

**Typical triggers:**

- "Review this slide layout"
- "Check if this UI description meets our standards"
- "Flag anything that violates our design principles"

**What it prevents:** Layout inconsistencies, accessibility gaps, visual hierarchy problems, off-brand choices.

**Build it when:** Generated slides or documents have visual quality issues that repeat across generations.

---

### Coder

**Purpose:** Applies your coding standards to generated or reviewed code — naming conventions, error handling, structure, documentation.

**Typical triggers:**

- "Review this script against our standards"
- "Generate a bash script that follows our conventions"
- "Check this for error handling gaps"

**What it prevents:** Inconsistent style, missing edge case handling, undocumented functions, non-portable shell syntax.

**Build it when:** Code you generate with Claude regularly needs manual cleanup before it's usable.

**Note:** `technical-verification` (already built) is a specialised coder-adjacent skill focused on factual accuracy of technical documentation. It is not a substitute for a coding standards skill.

---

### QA — Quality Assurance

QA skills come in several focused variants. Build the one that addresses your most frequent failure, not all of them at once.

**`technical-verification`** *(already built)*
Verifies commands, package names, API parameters, and UI paths against official docs before they appear in any output.

**`unit-test`**
Generates or reviews unit tests for coverage completeness, edge cases, and meaningful assertions. Triggered when writing or reviewing test files.

**`framing-alignment`**
Reads FRAMING.md and checks every section of a deliverable against the stated objectives, audience, and strategic deliverable. Flags contradictions and gaps before generation is finalised.

**`slide-density-qa`**
Checks generated slides for text density, claim support, and audience appropriateness. Triggered after Gamma generation before sharing.

**`financial-narrative`**
Checks slides or documents containing numbers, projections, or financial claims for internal consistency, sourcing, and appropriate caveats.

---

## Part 6 — Building a Skill

Skills are built using the skill creator loop. The short version:

1. Identify the workflow gap (from the interview or from a real failure)
2. Write `SKILL.md` — name, description, body instructions
3. Test it on 3–5 real prompts from your actual workflow
4. Review the outputs — does it change behaviour in the right way?
5. Refine the description until triggering is reliable
6. Package: `python -m scripts.package_skill ./skill-name ./output/`
7. Download the `.skill` file
8. Install via Claude Desktop → Settings → Extensions
9. Commit to your repo

For the full loop including evaluation and description optimisation, ask Claude to run the skill creator workflow.

---

## Quick Reference

| Action                         | Where                                                              |
|:------------------------------ |:------------------------------------------------------------------ |
| Install a skill                | Claude Desktop → Settings → Extensions → Install Extension         |
| Remove a skill                 | Claude Desktop → Settings → Extensions → remove                    |
| Make a skill permanent         | Commit `.skill` file to GitHub repo                                |
| Trigger the proposal interview | Prompt Claude in your Project after syncing FRAMING + CONSTITUTION |
| Build a new skill              | Ask Claude to run the skill creator workflow                       |
| Update a skill                 | Remove old → install new `.skill` file                             |

---

*See 03-content-as-code-workflow.md for FRAMING.md and CONSTITUTION.md structure*
*See 02-claude-stack-setup-manual.md for Claude Desktop Extensions setup*
