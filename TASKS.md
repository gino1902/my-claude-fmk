# TASKS.md — my-claude-fmk Task Tracker

> Managed by Claude (proposed) + approved by you.
> Triggers: `show tasks` | `next task` | `start task TASK-XXX` | `done task TASK-XXX` | `add task`
> Convention: TASKS.md is written immediately after proposing — no confirmation required.

---

## 🔴 Backlog

- [x] TASK-008 | Restructure skills docs into reference and playbook (Option A) | `claude-desktop/skills/` | Current state: `skills-setup-guide.md` (reference manual, v1.5, verified) and `skills-guide.md` (operational guide, v1.0, partially stale). Action: (1) rename `skills-guide.md` → `skills-usage-guide.md`; (2) fix stale content — Quick Reference table still references old Extensions path, persistence model predates ZIP/Customize flow; (3) remove duplicated SKILL.md anatomy — cross-reference setup guide instead; (4) add cross-reference headers to both files; (5) add version block to usage guide (currently missing). Boundary: setup guide = reference/install/templates; usage guide = proposal interview, archetypes, build loop, description discipline, update/commit workflow.
- [x] TASK-007 | Update playbook with all docs and tasks completed across repos | `claude-desktop/playbook/claude-project-playbook.md` | Cross-reference completed work in slide-gen and my-claude-fmk into the playbook — lessons learned, patterns confirmed, tasks closed — so the playbook reflects the current state of the framework not just its initial design | done: 2026-03-13
- [x] TASK-006 | System prompt routing template — author and insert into playbook | `claude-desktop/playbook/` | Write a reusable system prompt template implementing the agreed routing architecture (default repo, override rules, skills injection, behavioural rules); insert as a worked example in `claude-project-playbook.md` alongside existing examples | done: 2026-03-13
- [x] TASK-005 | Tech verify all documentation in `my-claude-fmk/` | `claude-desktop/` | Audit all docs against official Anthropic sources — verify claims about Projects, system prompts, knowledge files, skills, context window limits, and MCP behaviour; flag any outdated or unverified statements | done: 2026-03-13
- [x] TASK-004 | Write system prompt template for multi-repo routing | `claude-desktop/playbook/` | Superseded by TASK-006 | done: 2026-03-13
- [x] TASK-003 | Update `claude-project-playbook.md` for multi-project architecture | `claude-desktop/playbook/claude-project-playbook.md` | Sections 1 (System Prompt) and 3 (Skills) were written for single-project model — revise to reflect routing layer, per-repo CLAUDE.md scope, and MCP dependency caveat | done: 2026-03-13
- [x] TASK-002 | Add CLAUDE.md to `my-claude-fmk/` | `my-claude-fmk/CLAUDE.md` | Repo has no CLAUDE.md yet — define output defaults for framework documentation (format, tone, version block, flagging rules) consistent with the configuration-layer-guide layer model | done: 2026-03-13

---

## 🟡 In Progress

---

## ✅ Done

- [x] TASK-001 | Multi-project routing architecture — clarify, chart, document | `claude-desktop/context/` | Clarified architecture (multiple projects, shared skills, shared repos); produced agreed mmd chart; rationalized `configuration-layer-guide.md` (v2.0) and `prompt-maintenance.md`; tech-verified routing concept against official Anthropic docs | done: 2026-03-13

---

*Last updated: 2026-03-13 — all tasks closed*

> ⚠️ Post-TASK-008: `skills-setup-guide.md` and `skills-usage-guide.md` are stub redirects — delete them with `git rm` and commit.
