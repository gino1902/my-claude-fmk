# Claude Tool Map — Which Tool for Which Job

> Windows 11 | Claude Desktop + Claude Cowork + Claude Code
> Version 1.0

---

## The Three Tools

Anthropic provides three separate desktop tools. They are complementary — not interchangeable.

---

### Claude Desktop

A GUI chat interface that acts as an **MCP host**. Its power comes from connecting external services (APIs, databases, SaaS tools) directly into the conversation via MCP servers.

**Best for:**

- Orchestrating external tools (Gamma, GitHub, Notion, Slack, etc.)
- Generating content that requires API integrations
- Any workflow where Claude needs to call external services

---

### Claude Cowork

A desktop automation tool focused on **local file and task automation**. It does not support MCP but has native access to your file system and OS-level workflows.

**Best for:**

- Automating repetitive file operations (batch rename, convert, organize)
- Processing local documents without coding
- Desktop workflow automation for non-developers

---

### Claude Code

A terminal-based **agentic coding tool**. It runs in PowerShell and has native access to your file system, shell, and git. It operates in a long-horizon autonomous loop.

**Best for:**

- Writing, editing, and refactoring code in a project
- Running tests, installing packages, executing shell commands
- End-to-end software development tasks from the terminal

---

## Tool Comparison

| Capability                 | Claude Desktop | Claude Cowork | Claude Code         |
|:-------------------------- |:--------------:|:-------------:|:-------------------:|
| Interface                  | GUI chat       | GUI desktop   | Terminal            |
| MCP support                | ✅              | ❌             | ✅ (separate config) |
| File access                | Via MCP only   | ✅ Native      | ✅ Native            |
| Shell access               | Via MCP only   | ✅ Native      | ✅ Native            |
| External API integrations  | ✅              | ❌             | ✅                   |
| Local automation (no code) | ❌              | ✅             | ❌                   |
| Agentic coding             | ❌              | ❌             | ✅                   |
| Requires Node.js           | ✅              | ❌             | ✅                   |

---

## Decision Guide

```
Need to call an external API or SaaS tool?
  → Claude Desktop + MCP

Need to automate local files or OS tasks without coding?
  → Claude Cowork

Need to write, edit, or run code in a project?
  → Claude Code

Need all three?
  → Install all three — they coexist independently
```

---

## Installation Scope of This Documentation

| Tool                 | Covered                                |
|:-------------------- |:--------------------------------------:|
| Claude Desktop + MCP | ✅ Doc 2                                |
| Claude Code          | ✅ Doc 2                                |
| Claude Cowork        | ℹ️ Install only — no MCP config needed |

**Claude Cowork install:** Download from [claude.ai/download](https://claude.ai/download), run the installer, sign in. No additional configuration required.

---

*Doc 0 of 2 — see 01-claude-stack-explainer.md and 02-claude-stack-setup-manual.md*
