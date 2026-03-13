# CLAUDE.md — my-claude-fmk Defaults

> Output defaults for the `my-claude-fmk` framework documentation repo.
> Applies to all use-cases in this repo unless overridden by a `CONSTITUTION.md`.

---

## Repo Purpose

This repo is the personal framework for configuring and operating Claude Desktop.
It contains architecture docs, playbooks, setup guides, skill definitions, and verification records.
It does NOT contain project deliverables — those live in their own repos.

---

## Format

- Default output format: **Markdown (.md)**
- Use plain prose with tables and code blocks where appropriate
- No decorative formatting: no emoji, no horizontal rules as decoration
- Use headers sparingly — one level of nesting is usually enough
- Prefer tables over bullet lists for structured comparisons
- All code examples use fenced blocks with language tag (` ```xml `, ` ```bash `, ` ```markdown `)

---

## Tone

- Direct, technical, no filler
- Assume the reader is the repo author: business architect with strong technical background
- No preamble summarising what you're about to do — just do it
- Flag uncertainty explicitly: `> ⚠️ Unverified — check against official docs`
- Flag outdated content explicitly: `> ⚠️ Stale — verify before use`

---

## Version Block

Every output file must include a version block at the bottom:

| Field        | Value                  |
|--------------|------------------------|
| Version      | 1.x                    |
| Last Updated | YYYY-MM-DD             |
| Status       | Draft / Review / Final |

Version numbers must increment on each material revision.
Format: `1.0` for initial release, `1.1`, `1.2` etc. for incremental updates, `2.0` for structural rewrites.

---

## Flagging Rules

- Content only partially verified: flag inline with `> ✅ Verified: [source] [date]` or `> ⚠️ Unverified`
- Decisions made in a session: flag with `> [DECISION] [date]: [what was decided and why]`
- Assumptions not traceable to a source: mark explicitly as `[ASSUMPTION]`
- Out-of-scope deliverables: label at the top of the file

---

## Layer Boundaries

This repo uses a layered configuration model. When producing or editing any document, respect these boundaries:

| Layer | File | What belongs here |
| :--- | :--- | :--- |
| Repo defaults | `CLAUDE.md` (this file) | Output format, tone, version blocks, flagging rules |
| Use-case rules | `CONSTITUTION.md` | Content rules specific to one use-case |
| User intent | `FRAMING.md` | Why the use-case exists — author-owned, never modified by Claude |
| Architecture | `context/configuration-layer-guide.md` | How the layers relate — reference only |

**Do NOT** put routing rules or behavioural instructions in this file — those belong in the Claude Desktop system prompt.
**Do NOT** modify `FRAMING.md` files under any circumstances.

---

## Verification Standard

When making claims about Anthropic product behaviour (features, limits, UI paths, plan availability):
- Verify against `support.claude.com` or `docs.claude.com` before writing
- Mark verified claims: `> ✅ Verified: [source] [date]`
- Mark unverified claims: `> ⚠️ Unverified — check before publishing`
- Do not rely on training knowledge for product UI paths — these change frequently

---

## Overrides

Per use-case overrides are defined in the relevant `CONSTITUTION.md`.
`CONSTITUTION.md` takes precedence over all defaults in this file.

---

*Last updated: 2026-03-13 — v1.0 initial release*
