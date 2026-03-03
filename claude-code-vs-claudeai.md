# Claude Code vs Claude.ai — Comparison Reference

> Doc-verified against Anthropic official docs (docs.claude.com, code.claude.com) — March 2026.
> Ordered by project creation steps: from initial setup to agentic runtime.
> Corrections from conversation review are marked ✱

---

## 1. Project Setup & Configuration

| Concept                        | Claude.ai                            | Claude Code                                                               |
|:------------------------------ |:------------------------------------ |:------------------------------------------------------------------------- |
| Where does the project live?   | Anthropic's cloud (UI-managed)       | Your codebase (filesystem)                                                |
| Versioned with git?            | No                                   | Yes — commit `CLAUDE.md` and `.claude/`                                   |
| Shared with team?              | No — per user                        | Yes — committed to repo                                                   |
| Project-level config file      | Project Instructions (UI text field) | `CLAUDE.md` at project root                                               |
| User-level config file         | User preferences (UI)                | `~/.claude/CLAUDE.md`                                                     |
| Subdirectory-level config      | No                                   | `CLAUDE.md` in any subdirectory — loaded when Claude works in that folder |
| Conditional rules by file type | No                                   | `.claude/rules/*.md` — activated by glob pattern (e.g. `*.test.ts`)       |
| Settings file                  | Not applicable                       | `~/.claude/settings.json` (user) / `.claude/settings.json` (project)      |
| Settings precedence            | N/A                                  | Managed → Project → Local → User (higher wins)                            |
| Personal config (gitignored)   | N/A                                  | `CLAUDE.local.md` and `.claude/settings.local.json`                       |

---

## 2. System Prompt

| Concept                         | Claude.ai                                                        | Claude Code                                                                         |
|:------------------------------- |:---------------------------------------------------------------- |:----------------------------------------------------------------------------------- |
| System prompt visible?          | Yes — editable in Project Instructions                           | No — internal, not published                                                        |
| Can you replace it?             | Yes — fully editable                                             | No — append only                                                                    |
| How to customize it             | Edit Project Instructions directly                               | `--append-system-prompt` flag                                                       |
| What it configures              | Who Claude is: tone, persona, response style, context about you  | Who Claude is as an agent: tools available, coding style, safety rules, file access |
| Configures project knowledge?   | Yes — Project Instructions carries both role AND project context | No ✱ — project knowledge lives in `CLAUDE.md`, not the system prompt                |
| Internal system prompt contents | N/A                                                              | Tool usage instructions, code style guidelines, response tone, security rules       |
| Analogy                         | Employment contract + project brief combined                     | Employment contract only — project brief is separate (`CLAUDE.md`)                  |

---

## 3. Memory & Persistent Context

| Concept                     | Claude.ai                                   | Claude Code                                                                             |
|:--------------------------- |:------------------------------------------- |:--------------------------------------------------------------------------------------- |
| Mechanism name              | Memory system                               | Two systems: `CLAUDE.md` + Auto memory                                                  |
| Who writes it?              | Anthropic auto-generates from conversations | `CLAUDE.md`: you write it. `MEMORY.md`: Claude writes it automatically                  |
| Stored where?               | Anthropic's cloud (per user)                | `CLAUDE.md` on disk (git-tracked) + `~/.claude/projects/<project>/memory/` (local only) |
| Loaded at session start?    | Yes — injected at runtime                   | Yes — both `CLAUDE.md` and `MEMORY.md` index loaded at start                            |
| Line limit at startup       | N/A                                         | 200 lines max for `MEMORY.md` — content beyond is truncated                             |
| User can edit it?           | Via memory edits tool                       | Directly — edit the markdown file, or `/memory` command                                 |
| Gitignore recommended?      | N/A                                         | `MEMORY.md` and `settings.local.json` — yes. `CLAUDE.md` — no, commit it                |
| Auto-learned from sessions? | Yes                                         | Yes — Auto memory (`MEMORY.md`): build commands, debugging insights, preferences        |
| Key distinction             | Memory = who you are to Claude              | `CLAUDE.md` = rules you impose. `MEMORY.md` = what Claude has learned                   |

---

## 4. Skills

| Concept                           | Claude.ai                                               | Claude Code                                               |
|:--------------------------------- |:------------------------------------------------------- |:--------------------------------------------------------- |
| Feature exists?                   | Yes (since Oct 2025)                                    | Yes                                                       |
| Pre-built skills available?       | Yes — `docx`, `xlsx`, `pptx`, `pdf` (Anthropic-managed) | No pre-built via filesystem; bundled skills ship with CLI |
| Custom skills?                    | Yes — upload zip via Settings → Features                | Yes — filesystem at `.claude/skills/*/SKILL.md`           |
| Same SKILL.md format?             | Yes — open Agent Skills standard                        | Yes                                                       |
| Auto-invoked by Claude?           | Yes — based on description match (~100 tokens metadata) | Yes — based on description match                          |
| Manually invoked?                 | No slash commands in UI                                 | Yes — `/skill-name` in CLI                                |
| Versioned in git?                 | No — per user, not org-wide                             | Yes                                                       |
| Org-wide distribution?            | Not currently supported                                 | Yes — via plugins                                         |
| Require code execution enabled?   | Yes                                                     | No — filesystem-based, no sandbox needed                  |
| Max skills per API request        | Up to 8 (via `container` parameter)                     | N/A — filesystem, no limit                                |
| How Claude accesses skill content | VM filesystem — reads SKILL.md via bash when triggered  | Same — reads SKILL.md via bash when triggered             |

---

## 5. Skill Invocation Control (Claude Code only)

| Frontmatter field                | Effect                                                      | Use case                                                |
|:-------------------------------- |:----------------------------------------------------------- |:------------------------------------------------------- |
| *(none — default)*               | Both user and Claude can invoke                             | General-purpose skills                                  |
| `disable-model-invocation: true` | Only user can invoke via `/skill-name`                      | Side-effect workflows: `/deploy`, `/send-slack-message` |
| `user-invocable: false`          | Only Claude can invoke — no `/` command registered          | Background knowledge, auto-applied context              |
| `context: fork`                  | Runs in isolated subagent, results returned to main session | Long autonomous tasks with isolated context             |
| `agent: Explore`                 | Uses read-only Explore subagent                             | Codebase research without risk of writes                |

---

## 6. Slash Commands vs Skills (Claude Code)

| Aspect                     | Slash Commands (legacy)                  | Skills (current — v2.1.3+)                             |
|:-------------------------- |:---------------------------------------- |:------------------------------------------------------ |
| Location                   | `.claude/commands/*.md`                  | `.claude/skills/*/SKILL.md`                            |
| Invocation                 | Manual only — you type `/command-name`   | Auto (Claude decides) + manual `/skill-name`           |
| Supports bundled files?    | No — single markdown file                | Yes — `scripts/`, `references/`, `assets/` directories |
| Still works?               | Yes — fully backwards compatible         | Yes — merged into same system                          |
| Recommended going forward? | Legacy — keep existing, migrate new work | Yes                                                    |

---

## 7. Agents & Agentic Loop

| Concept                           | Claude.ai           | Claude Code                                                       |
|:--------------------------------- |:------------------- |:----------------------------------------------------------------- |
| Agentic by default?               | No — conversational | Yes — autonomous developer agent                                  |
| Has an agent loop?                | No — single turn    | Yes — perceive → act → observe → repeat                           |
| Decides next action autonomously? | No                  | Yes                                                               |
| Number of API calls per task      | 1                   | N — unknown upfront, goal-directed                                |
| Stops when?                       | After one response  | When Claude decides the goal is achieved (`end_turn`)             |
| Tool results fed back into loop?  | No                  | Yes — tool results re-enter conversation history each turn        |
| Context management across turns?  | N/A                 | Automatic compaction — clears old tool calls before context limit |
| Built-in subagent types           | None                | `Explore` (read-only), `Plan`, general-purpose                    |

---

## 8. SDK & Programmatic Access

| Concept                           | Claude.ai     | Claude Code / Agent SDK                                                                    |
|:--------------------------------- |:------------- |:------------------------------------------------------------------------------------------ |
| SDK package                       | N/A (UI only) | `@anthropic-ai/claude-agent-sdk` (renamed from `claude-code-sdk`)                          |
| Use Claude Code system prompt?    | No            | Yes — `systemPrompt: { preset: "claude_code" }`                                            |
| Append to system prompt?          | No            | Yes — `appendSystemPrompt: "..."`                                                          |
| Load `CLAUDE.md` from filesystem? | No            | Yes — requires `settingSources: ["project"]` ✱ (not automatic with preset alone)           |
| Load skills from filesystem?      | No            | Yes — requires `settingSources: ["project", "user"]` + `allowedTools: ["Skill"]`           |
| Agent loop managed by SDK?        | No            | Yes — async generator, no manual `while(true)` needed                                      |
| Use pre-built API skills?         | N/A           | Via Messages API — `container: { skills: [{ skill_id: "pptx" }] }` (different API surface) |
| Context auto-managed?             | No            | Yes — automatic compaction built into SDK                                                  |

---

## 9. Subagents (Claude Code only)

| Concept                    | Detail                                                                                                                     |
|:-------------------------- |:-------------------------------------------------------------------------------------------------------------------------- |
| Location                   | `.claude/agents/*/` — markdown files with frontmatter                                                                      |
| Purpose                    | Independent Claude instance with its own tool scope and system prompt                                                      |
| Difference from skills     | Skills = portable expertise injected into current context. Subagents = separate Claude instances with isolated tool access |
| Can use skills?            | Yes — subagents can load skills as reference material                                                                      |
| Isolated worktree support? | Yes — `isolation: worktree` in agent definition for git-isolated runs                                                      |
| Defined programmatically?  | Yes (unlike skills, which must be filesystem artifacts)                                                                    |

---

## 10. Permissions & Security

| Concept                           | Claude.ai                          | Claude Code                                                             |
|:--------------------------------- |:---------------------------------- |:----------------------------------------------------------------------- |
| File access control               | Controlled by Anthropic            | `permissions.deny` in `settings.json` — e.g. `"Read(./.env)"`           |
| Org-level policy enforcement      | Via Team/Enterprise plan           | Managed settings — `/etc/claude-code/managed-settings.json` (Linux/WSL) |
| Managed settings override user?   | N/A                                | Yes — Managed > Project > Local > User                                  |
| Skill execution security          | Upload reviewed by user before use | Skills execute arbitrary code via bash — only use trusted sources       |
| Network access in skills          | Controlled by Anthropic (VM)       | Full — same as any program on your machine                              |
| Sensitive file protection pattern | N/A                                | `permissions.deny: ["Read(./.env)", "Read(./.env.*)"]`                  |

---

## Quick Mental Model

| Question                                    | Claude.ai                                               | Claude Code                                                                 |
|:------------------------------------------- |:------------------------------------------------------- |:--------------------------------------------------------------------------- |
| Who is Claude?                              | Configured by Project Instructions                      | Configured by Anthropic's internal system prompt + `--append-system-prompt` |
| What should Claude know about this project? | Configured by Project Instructions (combined with role) | Configured by `CLAUDE.md`  (separate from system prompt)                    |
| What has Claude learned about this project? | Configured by Memory system                             | Configured by `MEMORY.md` (Auto memory — Claude writes it)                  |
| What can Claude do?                         | Configured by plan settings                             | Configured by `settings.json` permissions                                   |
| What does Claude know how to do?            | Configured by Skills (uploaded zip)                     | Configured by Skills (filesystem `.claude/skills/`)                         |
| Who drives the conversation?                | You                                                     | Claude — goal-directed agent loop                                           |
| Where does work live?                       | Anthropic's cloud                                       | Your repo                                                                   |

---

*Verified against docs.claude.com and code.claude.com — Claude Code CLI v2.1.x, March 2026.*
*✱ marks corrections from prior version of this document.*
