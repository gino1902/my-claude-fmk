# Claude Skills — Setup Guide
> How to create and install custom Skills across Claude.ai, Claude Desktop, and Claude Code
> Version 1.4 — Source-verified against official Anthropic documentation

---

## What are Skills?

Skills are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks. Skills teach Claude how to complete specific tasks in a repeatable way.

Skills work through **progressive disclosure** — Claude scans available skill metadata to determine relevance, then loads the full content only when needed. This keeps the context window lean.

A skill is always a directory containing at minimum a `SKILL.md` file. The file has two parts:

- **YAML frontmatter** — `name` and `description` fields (required). Claude uses the description to decide when to invoke the skill.
- **Markdown body** — the instructions Claude follows when the skill is active.

```
my-skill/
├── SKILL.md          ← required
├── references/       ← optional reference docs
├── scripts/          ← optional executable code
└── assets/           ← optional templates and files
```

**Composition note:** Skills cannot explicitly reference other skills. Claude can however use multiple skills simultaneously and automatically — composability is built into the invocation mechanism, not the skill files themselves.

**When two skills trigger on the same prompt:** Claude loads both and applies their instructions together. This is by design and works well when skills have non-overlapping scope. If two skills give conflicting instructions on the same aspect of a task, behaviour is undefined — the official docs do not specify a resolution priority. Avoid overlapping scope between skills. If two skills consistently co-trigger in an unintended way, tighten the trigger phrases in their descriptions to make them mutually exclusive.

⚠️ Only install skills from trusted sources. Primary risks are prompt injection and data exfiltration from malicious skill content.

---

## Skill.md Structure

Every skill file follows this structure:

```markdown
---
name: your-skill-name
description: >
  Clear description of what this skill does and when to use it.
  Claude uses this to decide when to invoke the skill.
---

# Skill Name

## Instructions

Step-by-step guidance for Claude.

## Examples

Concrete examples of expected inputs and outputs.
```

**Field rules (verified against official docs):**

| Field | Required | Limit | Notes |
| :--- | :---: | :--- | :--- |
| `name` | ✅ | 64 characters | ⚠️ Lowercase/hyphens-only confirmed for Claude Code only — unverified for claude.ai and Claude Desktop |
| `description` | ✅ | ⚠️ See note below | Critical — Claude uses this to determine invocation |
| `dependencies` | ❌ | — | Software packages required by the skill |

⚠️ **Description character limit conflict between official sources:**

- `support.claude.com` (claude.ai / Claude Desktop): 200 characters maximum
- `code.claude.com/docs` (Claude Code): 1024 characters maximum

Use 200 characters as the safe limit across all configurations until Anthropic resolves this discrepancy.

---

## Configuration 1 — Claude.ai and Claude Desktop

Anthropic does not document a separate skills installation flow for Claude Desktop. Both claude.ai and Claude Desktop use the same **Customize > Skills** UI. The steps below apply to both.

⚠️ **Plan availability conflict between official sources:**

- `support.claude.com/what-are-skills`: Free, Pro, Max, Team, Enterprise
- `support.claude.com/how-to-create-custom-skills`: Pro, Max, Team, Enterprise only (free not listed)

Confirm your plan supports custom skills before uploading. If on a free plan, custom skill upload may not be available.

### Prerequisites

- **Settings > Capabilities** → Code execution and file creation must be enabled
- Team/Enterprise: Owner must also enable Skills in **Organization settings > Skills**

### Install a custom skill

1. Create your skill folder locally with `SKILL.md` inside
2. Zip the folder — the ZIP must contain the skill folder as its root:

   ```
   my-skill.zip
   └── my-skill/
       └── SKILL.md
   ```

   ⚠️ Do not place files directly in the ZIP root — this will fail.

3. Navigate to **Customize > Skills**
4. Click the **"+"** button → **"Upload a skill"**
5. Upload the ZIP file
6. Toggle the skill on

### Claude Desktop specific notes

- The **Customize** section groups Skills, Plugins, and Connectors in one place
- MCP filesystem connectors give Claude file access but are separate from the skills system — skills must still be uploaded via **Customize > Skills**

### Manage skills

- Enable/disable: toggle in **Customize > Skills**
- Delete: click the skill → **"..."** → **Delete**
- Skills are private to your account. To share across a Team or Enterprise org, use organization provisioning (Owner access required).
- No data persists between sessions — skills run in Claude's secure sandboxed environment.

### How invocation works

Claude automatically scans available skills and loads relevant ones based on your request. You do not need to explicitly invoke skills — Claude determines when each skill is needed based on its `description` field.

---

## Configuration 2 — Claude Code

### Prerequisites

- Claude Code version 1.0 or later
- Skills are file-based — no UI upload required

### Skill locations

Claude Code scans three locations, in priority order:

| Scope | Location | Use for |
| :--- | :--- | :--- |
| Personal | `~/.claude/skills/your-skill/` | Individual workflows, available across all projects |
| Project | `.claude/skills/your-skill/` | Team workflows, committed to git, shared automatically |
| Plugin | Bundled with plugin | Installed via plugin marketplace |

When the same skill name exists at multiple levels, the priority is: **enterprise > personal > project**.

### Create a personal skill

```bash
mkdir -p ~/.claude/skills/your-skill-name
# create SKILL.md inside the directory
```

### Create a project skill

```bash
mkdir -p .claude/skills/your-skill-name
# create SKILL.md inside the directory
# commit to git — team members get it automatically
```

### How invocation works

Skills are **model-invoked** — Claude autonomously decides when to use them based on the `description` field. This is different from slash commands, which are user-invoked (`/command`).

To invoke a skill explicitly, use `/your-skill-name` in the Claude Code prompt.

### Monorepo support

Claude Code automatically discovers skills from nested `.claude/skills/` directories. If you are editing a file in `packages/frontend/`, Claude Code also looks for skills in `packages/frontend/.claude/skills/`.

### Live change detection

Skills defined in directories added via `--add-dir` are picked up by live change detection — you can edit them during a session without restarting.

---

## Comparison Table

| | Claude.ai | Claude Desktop | Claude Code |
| :--- | :---: | :---: | :---: |
| Install method | ZIP upload via UI | ZIP upload via UI | Filesystem (directory) |
| UI location | Customize > Skills | Customize > Skills | No UI — file-based |
| Personal scope | ✅ | ✅ | `~/.claude/skills/` |
| Project/team scope | Via org provisioning | Via org provisioning | `.claude/skills/` + git |
| Invocation | Automatic | Automatic | Automatic or `/skill-name` |
| Code execution required | ✅ | ✅ | ❌ |
| context: fork / agent field | ❌ | ❌ | ✅ |
| Requires plan | ⚠️ Free+ or Pro+ (conflicting docs) | ⚠️ Free+ or Pro+ (conflicting docs) | Claude Code subscription |

---

## Templates

### A note on co-triggering

A prompt can trigger more than one skill. Claude loads all matching skills and composes their instructions. This works well when skills have distinct, non-overlapping scope — as in the three templates below. If you adapt these templates, ensure trigger phrases remain distinct. For example, `output-format` and `doc-formatter` could both trigger on "format this document" — in that case they compose harmlessly, but if their instructions ever conflict, results will be unpredictable.

---

### Simple — Reference Content

**Use when:** You want Claude to apply a set of rules, conventions, or guidelines inline. No scripts, no supporting files.

**Compatible with:** Claude.ai, Claude Desktop, Claude Code

**Folder structure:**

```
output-format/
└── SKILL.md
```

**SKILL.md:**

```markdown
---
name: output-format
description: >
  Apply standard output formatting rules. Use when the user says
  'format this', 'apply output rules', 'add a version block', or
  asks to standardise the structure of any document or report.
---

# Output Format

## Rules

- Always begin with a one-line summary
- Use Markdown headers for sections (##, ###)
- Use tables for structured data — never bullet lists for comparisons
- End every document with a version block
- Flag assumptions explicitly with ⚠️

## Version Block Template

| Field        | Value      |
| :----------- | :--------- |
| Version      | 1.0        |
| Last Updated | YYYY-MM-DD |
| Status       | Draft      |
```

---

### Medium — Structured Instructions + Supporting File

**Use when:** Instructions are multi-step and reference a template or supplemental reference that Claude should load only when needed.

**Compatible with:** Claude.ai, Claude Desktop, Claude Code

**Folder structure:**

```
doc-formatter/
├── SKILL.md
└── template.md
```

**SKILL.md:**

```markdown
---
name: doc-formatter
description: >
  Format documents following team standards. Use when the user says
  'format this document', 'turn this into a spec', 'write this up as
  meeting notes', 'make this a decision record', or 'write a project brief'.
---

# Document Formatter

## Instructions

1. Read the user's content and identify the document type
2. Load [template.md](template.md) to get the correct structure for that type
3. Rewrite the content using the template structure
4. Apply output-format rules: headers, tables, version block
5. Flag any section where source content was ambiguous or missing

## Document Types Supported

- Technical specification
- Meeting notes
- Decision record
- Project brief

For each type, the correct template section is defined in template.md.
```

**template.md:**

```markdown
# Document Templates

## Technical Specification

### Context
### Problem
### Proposed Solution
### Constraints
### Open Questions

## Meeting Notes

### Date / Attendees
### Decisions
### Actions (owner, due date)
### Parking Lot

## Decision Record

### Decision
### Context
### Alternatives Considered
### Consequences

## Project Brief

### Objective
### Scope
### Success Criteria
### Timeline
```

---

### Complex — Script + Forked Agent

> ⚠️ **Claude Code only.** The `context: fork`, `agent`, and `allowed-tools` frontmatter fields are not available in Claude.ai or Claude Desktop.

**Use when:** The task requires isolated execution, tool-restricted analysis, and script output — without polluting the main conversation context. Results are summarized and returned to the main conversation when the subagent completes.

**Compatible with:** Claude Code only

**Folder structure:**

```
codebase-report/
├── SKILL.md
├── REFERENCE.md
└── scripts/
    └── collect_stats.py
```

**SKILL.md:**

```markdown
---
name: codebase-report
description: >
  Analyse the codebase and generate a structured report covering file
  count, language breakdown, and largest files. Use when the user says
  'analyse the codebase', 'codebase report', 'health check', or
  'give me an overview of the repo'.
context: fork
agent: Explore
allowed-tools: Bash, Read, Glob, Grep
---

# Codebase Report

## Instructions

1. Run the stats script to collect raw data:

   ```bash
   python scripts/collect_stats.py .
   ```

2. Read [REFERENCE.md](REFERENCE.md) for the report structure and thresholds
3. Analyse the script output against the thresholds defined in REFERENCE.md
4. Produce a structured report following the format in REFERENCE.md
5. Flag any files or directories that exceed defined thresholds
6. Return the completed report to the main conversation

## Notes

- Do not modify any files — this skill is read-only
- If the script fails, report the error and the working directory path
- Limit analysis to the directory passed as argument
```

**REFERENCE.md:**

```markdown
# Codebase Report — Reference

## Report Structure

### Summary
- Total files
- Total lines of code
- Language breakdown (table)

### Largest Files
Top 10 files by line count (table: path, language, lines)

### Flags
Any file exceeding 500 lines or any directory exceeding 50 files

## Thresholds

| Metric         | Warning     |
| :------------- | :---------- |
| File size      | > 500 lines |
| Directory size | > 50 files  |
| Total files    | > 1000      |
```

**scripts/collect_stats.py:**

```python
import os
import sys
from pathlib import Path

def collect(root):
    stats = []
    for path in Path(root).rglob("*"):
        if path.is_file() and not any(p.startswith(".") for p in path.parts):
            try:
                lines = len(path.read_text(errors="ignore").splitlines())
                stats.append({"path": str(path), "lines": lines,
                               "ext": path.suffix})
            except Exception:
                pass
    stats.sort(key=lambda x: x["lines"], reverse=True)
    print(f"Total files: {len(stats)}")
    print(f"Total lines: {sum(s['lines'] for s in stats)}")
    print("\nTop 10 files:")
    for s in stats[:10]:
        print(f"  {s['lines']:>6}  {s['path']}")
    from collections import Counter
    exts = Counter(s["ext"] for s in stats)
    print("\nLanguage breakdown:")
    for ext, count in exts.most_common():
        print(f"  {ext or '(none)':>10}  {count} files")

if __name__ == "__main__":
    collect(sys.argv[1] if len(sys.argv) > 1 else ".")
```

**How it works at runtime:**

1. Claude detects the request matches the skill description
2. `context: fork` spawns an isolated Explore subagent — main conversation context is not carried over
3. `allowed-tools` restricts the subagent to read-only tools (Bash, Read, Glob, Grep) — no file writes possible
4. The subagent runs `collect_stats.py` via Bash — only the script output enters context, not the script code
5. Claude reads `REFERENCE.md` progressively — only when step 2 of the instructions requires it
6. The completed report is returned to the main conversation

---

## Packaging Checklist (claude.ai and Claude Desktop)

Before uploading:

- [ ] Folder name matches skill name
- [ ] `SKILL.md` is at the root of the folder
- [ ] YAML frontmatter is valid (name and description present, wrapped in `---`)
- [ ] ZIP contains the skill folder as its root — not loose files
- [ ] Description is 200 characters or fewer
- [ ] No hardcoded API keys or passwords
- [ ] All referenced scripts or resource files exist inside the folder

---

## Troubleshooting

| Symptom | Likely cause | Fix |
| :--- | :--- | :--- |
| Skills section not visible | Code execution disabled | Settings > Capabilities → enable Code execution |
| Skills greyed out (Team/Enterprise) | Org-level code execution disabled | Contact org Owner |
| Skill not invoked automatically | Description too vague | Rewrite description to be specific about when the skill applies |
| YAML parse error on upload | Frontmatter formatting issue | Check `---` delimiters, indentation, no tabs |
| Claude Code skill not found | Wrong directory path | Verify skill is in `~/.claude/skills/` or `.claude/skills/` |
| Subagent not spawning | context: fork used outside Claude Code | context: fork is Claude Code only — remove for other configs |
| Two skills triggering together unexpectedly | Overlapping description scope | Rewrite descriptions with more specific, distinct trigger phrases |

---

## References

- [What are Skills?](https://support.claude.com/en/articles/12512176-what-are-skills) — support.claude.com
- [Use Skills in Claude](https://support.claude.com/en/articles/12512180-use-skills-in-claude) — support.claude.com
- [How to create custom Skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills) — support.claude.com
- [Agent Skills — Claude Code Docs](https://code.claude.com/docs/en/skills) — code.claude.com
- [Agent Skills Overview — Claude Docs](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) — docs.claude.com
- [Anthropic Skills GitHub repository](https://github.com/anthropics/skills) — example skills and templates

---

## Verified Claims Log

Tracks claim-level verification status. Re-check any row marked Conflicted or approaching 12 months since last verification.

| Claim | Source URL | Date Verified | Status |
| :--- | :--- | :--- | :--- |
| Skills available on Free, Pro, Max, Team, Enterprise | support.claude.com/what-are-skills | 2026-03-05 | ⚠️ Conflicted |
| Skills available on Pro, Max, Team, Enterprise only | support.claude.com/how-to-create-custom-skills | 2026-03-05 | ⚠️ Conflicted |
| Description character limit: 200 chars (claude.ai / Claude Desktop) | support.claude.com/how-to-create-custom-skills | 2026-03-05 | ✅ Confirmed |
| Description character limit: 1024 chars (Claude Code) | code.claude.com/docs | 2026-03-05 | ✅ Confirmed |
| name field lowercase/hyphens-only scoped to Claude Code only | code.claude.com/docs | 2026-03-05 | ✅ Confirmed |
| context: fork available in Claude Code only | code.claude.com/docs | 2026-03-05 | ✅ Confirmed |
| ZIP must contain skill folder as root — not loose files | support.claude.com/how-to-create-custom-skills | 2026-03-05 | ✅ Confirmed |
| Code execution must be enabled for Skills (claude.ai / Desktop) | support.claude.com/use-skills-in-claude | 2026-03-05 | ✅ Confirmed |

---

**Document Version**

| Field | Value |
| :--- | :--- |
| Version | 1.4 |
| Last Updated | 2026-03-05 |
| Status | Final |
| Verified against | support.claude.com, code.claude.com/docs, docs.claude.com (March 2026) |

*v1.0 — Initial guide*
*v1.1 — Tech verification pass: flagged plan availability conflict, description char limit conflict, name format scope, merged Claude Desktop into Claude.ai section, added skills composition note*
*v1.2 — Added Templates section: simple, medium, complex (script + forked agent, Claude Code only). Added context:fork row to comparison table. Added subagent troubleshooting row.*
*v1.3 — Rewrote all three template descriptions to include explicit trigger phrases.*
*v1.4 — Added co-triggering note to What are Skills, co-triggering note to Templates section, troubleshooting row for unexpected combined skill behaviour.*
