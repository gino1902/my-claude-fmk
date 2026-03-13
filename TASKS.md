# TASKS.md — my-claude-fmk Task Tracker

> Managed by Claude (proposed) + approved by you.
> Triggers: `show tasks` | `next task` | `start task TASK-XXX` | `done task TASK-XXX` | `add task`
> Convention: TASKS.md is written immediately after proposing — no confirmation required.

---

## 🔴 Backlog

- [ ] TASK-004 | Write system prompt template for multi-repo routing | `claude-desktop/playbook/` | Create a reusable system prompt template implementing the agreed routing architecture: default repo declaration, override rules, skills injection paths, and behavioural rules — generic enough to work for any project
- [ ] TASK-003 | Update `claude-project-playbook.md` for multi-project architecture | `claude-desktop/playbook/claude-project-playbook.md` | Sections 1 (System Prompt) and 3 (Skills) were written for single-project model — revise to reflect routing layer, per-repo CLAUDE.md scope, and MCP dependency caveat
- [ ] TASK-002 | Add CLAUDE.md to `my-claude-fmk/` | `my-claude-fmk/CLAUDE.md` | Repo has no CLAUDE.md yet — define output defaults for framework documentation (format, tone, version block, flagging rules) consistent with the configuration-layer-guide layer model

---

## 🟡 In Progress

---

## ✅ Done

- [x] TASK-001 | Multi-project routing architecture — clarify, chart, document | `claude-desktop/context/` | Clarified architecture (multiple projects, shared skills, shared repos); produced agreed mmd chart; rationalized `configuration-layer-guide.md` (v2.0) and `prompt-maintenance.md`; tech-verified routing concept against official Anthropic docs | done: 2026-03-13

---

*Last updated: 2026-03-13 — TASK-001 closed; session close*
