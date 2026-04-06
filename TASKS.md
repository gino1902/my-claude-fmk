# TASKS.md — my-claude-fmk

> Managed by Claude (proposed) + approved by you.
> Triggers: `show tasks` | `next task` | `start task TASK-XXX` | `done task TASK-XXX` | `add task`
> Convention: `managing-tasks: writes pre-approved`

---

## 🔴 Backlog

- TASK-011: Run token audit on my-claude-fmk system prompt | target: Custom Instructions (my-claude-fmk project) | scope: project-bootstrapping skill added to workspace — prompt-maintenance rule requires audit when new skill is added; follow prompt-maintenance.md four-step pattern | blocker: sequence after TASK-012 — playbook changes may surface system prompt adjustments before audit runs
- TASK-012: Document project-bootstrapping in claude-project-playbook.md | target: claude-desktop/playbook/claude-project-playbook.md | scope: add section or example entry covering the bootstrapping skill — trigger, 3-phase flow, MCP dependency, compose rules; consistent with existing examples table format | note: playbook restructured to v3.0 (2026-03-26) but bootstrapping entry not yet added — this task remains open

---

## 🟡 In Progress

---

## ✅ Done

- TASK-001: Multi-project routing architecture — clarify, chart, document | target: claude-desktop/context/ | scope: clarified architecture; produced mmd chart; rationalized context-layers-guide.md v2.0 and prompt-maintenance.md; tech-verified routing concept | done: 2026-03-13
- TASK-002: Add CLAUDE.md to my-claude-fmk/ | target: my-claude-fmk/CLAUDE.md | scope: repo defaults — format, tone, version block, flagging rules, layer boundaries | done: 2026-03-13
- TASK-003: Update claude-project-playbook.md for multi-project architecture | target: claude-desktop/playbook/claude-project-playbook.md | scope: Sections 1 and 3 revised for routing layer, per-repo CLAUDE.md scope, MCP dependency caveat | done: 2026-03-13
- TASK-004: Write system prompt template for multi-repo routing | target: claude-desktop/playbook/ | scope: superseded by TASK-005 | done: 2026-03-13
- TASK-005: System prompt routing template — author and insert into playbook | target: claude-desktop/playbook/ | scope: routing-system-prompt-template.md authored; inserted as worked example in playbook with field guide, MCP checklist, verification markers | done: 2026-03-13
- TASK-006: Tech verify all documentation in my-claude-fmk/ | target: claude-desktop/ | scope: audit against official Anthropic sources; flagged stale claims; verified install paths, plan gating, MCP behaviour | done: 2026-03-13
- TASK-007: Update playbook with completed work across repos | target: claude-desktop/playbook/claude-project-playbook.md | scope: lessons learned from slide-gen and my-claude-fmk tasks cross-referenced; Section 11 (layer model) and Section 12 (operational hygiene) added | done: 2026-03-13
- TASK-008: Restructure skills docs into reference and playbook | target: claude-desktop/skills/ | scope: skills-setup-guide.md → skills-reference.md; skills-guide.md → skills-playbook.md; stale content fixed; SKILL.md anatomy removed and cross-referenced; version blocks added | done: 2026-03-13
- TASK-009: Fix stale and contradictory content across my-claude-fmk docs | target: claude-desktop/ | scope: claude-stack-explainer.md v2.3 (UI path corrected); context-layers-guide.md v2.2 (precedence diagram split into two axes); claude-project-playbook.md v3.0 (Knowledge Files verification scoped per-claim; Skills section MCP-first); skills-reference.md v1.1 (MCP pattern repositioned as primary) | done: 2026-03-26

---

*Last updated: 2026-03-26 — TASK-009 closed (doc fixes from challenge pass); TASK-011 blocker note added; TASK-012 note added clarifying playbook restructure does not satisfy the task*
