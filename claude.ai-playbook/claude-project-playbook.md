# Claude Project Configuration Playbook

> Personal reference — how to configure and use a Claude Project effectively
> Based on claude.ai Projects (Sonnet 4.6)

---

## 1. System Prompt

**What it is:** Persistent instruction block prepended to every conversation in the project. Claude reads it first, every time.

**When to use:** Anything you'd type more than twice belongs here.

**How to set it:** Project → Custom Instructions field.

**What belongs here:**

| Category | Example |
| :--- | :--- |
| Role / framing | `"You are reviewing as a senior network architect"` |
| Skills definitions | `## SKILL: risk-review ...` |
| Workflows | `## WORKFLOW: arch-review ...` |
| Hard rules | `"Never recommend deleting production data"` |
| Output format defaults | `"Always respond in markdown with headers"` |
| Tone / verbosity | `"Be concise. No filler. Flag uncertainty explicitly."` |

**What does NOT belong here:**

- Large reference documents → use Knowledge Files
- One-off task instructions → put in the conversation
- Data that changes often → too expensive to maintain

**Length guideline:** Keep it proportionate. The system prompt loads every turn and reduces space for conversation and knowledge retrieval. No official hard limit — but bloat has a real cost. Trim anything you no longer use.

**Structuring tip:** Use XML tags to separate sections — Claude parses them unambiguously when instructions mix context, rules, and examples:

```xml
<role>Senior network architect reviewer</role>
<context>Azure-based infrastructure, production environment</context>
<rules>Never recommend irreversible changes without confirmation</rules>
<skills>...</skills>
```

---

## 2. Knowledge Files

**What it is:** Documents attached to the project that Claude can read and reference. Persistent across all conversations.

**When to use:**

- Reference material you cite often (architecture specs, data dictionaries, company context)
- Skills or workflows too long to inline in the system prompt
- Templates Claude should reuse

**How to add:** Project → Add Content → Upload files or paste text.

**Options:**

| Approach | When |
| :--- | :--- |
| Inline in system prompt | Short skill/rule, <20 lines |
| Separate knowledge file | Long skill, template, reference doc |
| One file per skill | When you have 5+ skills and want clean separation |

**Key distinction:**

| System Prompt | Knowledge File |
| :--- | :--- |
| Always fully loaded, every turn | Retrieval-based — only relevant parts pulled per reply |
| Counts against context window every turn | Lower context pressure per turn |
| For instructions and behavior | For reference material and content |

**File specs:** Individual files up to 30MB. Accepted formats: PDF, DOCX, CSV, TXT, HTML, ODT, RTF, EPUB. Unlimited files per project — but Claude retrieves only what fits within the active context window per answer.

---

## 3. Skills

**What it is:** A named, reusable prompt module that defines *how Claude thinks* for a specific task. Anthropic officially describes Skills as "professional knowledge packs" — folders of instructions, scripts, and resources Claude can discover and load on demand. In claude.ai Projects, you implement them as named blocks in your system prompt or knowledge files.

**When to use:** Any task you repeat across conversations.

**How to define (in system prompt or knowledge file):**

```
## SKILL: risk-review
Trigger: when asked to review risks
Steps:
1. Identify top 3 risks by severity
2. For each: impact, likelihood, mitigation
3. Flag any blockers explicitly
Output: markdown table with columns Risk | Severity | Mitigation
Avoid: generic risks, vague mitigations
```

**Invocation options:**

| Style | Example |
| :--- | :--- |
| Explicit | `"run risk-review on this"` |
| Implicit (baked in system prompt) | Claude triggers automatically on matching input |
| Parameterized | `"run risk-review for prod environment"` |

**Storage options:**

| Where | When |
| :--- | :--- |
| Inline in system prompt | ≤3 skills, each <20 lines |
| Single knowledge file `skills.md` | 4–8 skills |
| One file per skill | Complex skills with examples |

---

## 4. Workflows

**What it is:** A named chain of skills executed in sequence. Defined once, invoked by keyword.

**When to use:** Multi-step tasks you run repeatedly.

**How to define:**

```
## WORKFLOW: arch-review
Trigger: user says "run arch-review"
Steps:
1. sizing-estimate skill on input
2. risk-review skill on result of step 1
3. summary skill on combined output
Final output format: [executive summary + risk table + sizing estimate]
```

**Parameterized variant:**

```
"run arch-review on [diagram] for [environment]"
```

Claude threads the variables through each step automatically.

**Chaining patterns:**

| Pattern | Description |
| :--- | :--- |
| Linear | A → B → C, each step feeds the next |
| Parallel + merge | Run A and B on same input, combine outputs |
| Conditional | `"if risk score > high, also run compliance-check"` |
| Recursive | Feed output back as input (e.g. critique → improve → critique) |

**Limit:** Long chains with verbose skills will eat your context window. Beyond 4–5 steps → move to API orchestration.

---

## 5. Guardrails

**What it is:** Rules that constrain Claude's behavior in this project. Defined in the system prompt.

**Hard rules (never violated):**

```
- Never recommend irreversible infrastructure changes without explicit confirmation
- Never output credentials or secrets, even as examples
- Always flag when a question is outside your defined scope
```

**Soft preferences (default behavior, overridable):**

```
- Default to concise responses unless asked for detail
- Assume production context unless stated otherwise
- Prefer options over single recommendations
```

**Failure modes to anticipate and guard against:**

| Failure | Guardrail |
| :--- | :--- |
| Sycophancy | `"Disagree with me when I'm wrong. Flag it explicitly."` |
| Hallucinating specifics | `"If uncertain, say so. Don't invent version numbers or names."` |
| Scope creep | `"Stay within [domain]. Flag out-of-scope requests."` |
| Verbose filler | `"No preamble. No summary of what you're about to do. Just do it."` |

---

## 6. Framing Patterns

**What it is:** How you set up the task context. Alternatives to the overused `"You are a..."` persona.

**Options:**

| Pattern | Example | Best for |
| :--- | :--- | :--- |
| Goal-first | `"The goal is X. Here's context..."` | Outcome-focused tasks |
| Constraint-first | `"Never do Y. Given that, help with..."` | Guardrail-heavy tasks |
| Audience framing | `"This is for a non-technical CFO."` | Tone/complexity shaping |
| Context dump | `"50-person SaaS, Series A, migrating monolith..."` | Rich situational grounding |
| Collaborative | `"Push back where you disagree."` | Reducing sycophancy |
| Task-only | `"Review this and flag risks."` | Simple, direct tasks |
| Format-as-frame | Provide a template, Claude fills it | Output structure control |

**Rule of thumb:** Persona (`"you are..."`) is rarely the best choice. Goal-first + context dump is stronger for most professional work.

---

## 7. Memory

**What it is:** Claude auto-generates persistent memories from conversations in a project. Visible and editable.

**Where to find it:** Project → Memory (or conversation header).

**When to push something to memory manually:**

- Your role, team context, recurring constraints
- Decisions already made ("we chose Postgres, stop suggesting alternatives")
- Preferences that always apply ("always output JSON, never YAML")

**What to avoid storing:**

- Sensitive data, credentials, PII
- Instructions that could override safety behavior
- Volatile data that changes often

**Practical use:** After a long onboarding conversation, explicitly say: `"Summarize the key decisions and context from this conversation as memory items."` Then clean up the result.

---

## 8. Invocation Patterns

**What it is:** How you trigger skills and workflows efficiently.

**Patterns:**

| Pattern | Example | When |
| :--- | :--- | :--- |
| Keyword trigger | `"run arch-review"` | Named workflows |
| Parameterized | `"run arch-review on [X] for [Y]"` | Variable inputs |
| Implicit | Input matches, Claude auto-triggers | Simple, well-defined tasks |
| Stacked | `"run skill-A then skill-B"` | Ad-hoc chaining |
| Override | `"run risk-review but skip the table format"` | One-off adjustment |

**Tip:** Define your trigger keywords clearly in the system prompt. Ambiguous triggers cause Claude to guess.

---

## 9. Context Window Management

**What it is:** The total tokens Claude can see in one turn. System prompt + retrieved knowledge file content + conversation history + your message.

**Plan limits:**

| Plan | Context Window |
| :--- | :--- |
| Pro | 200K tokens (~500 pages) |
| Team | 200K tokens |
| Enterprise | 500K tokens (specific models) |

**What eats it:**

| Source | Impact |
| :--- | :--- |
| System prompt | Every turn, always fully loaded |
| Long conversation history | Grows unbounded |
| Knowledge file retrieval | Relevant chunks per turn |
| Verbose skill definitions | Multiplied across turns |

**How to stay lean:**

- Keep system prompt proportionate — trim unused skills and rules
- Start a new conversation when the thread gets long
- Summarize old context: `"Summarize this conversation in 200 words, I'll use it as context in a new thread"`
- Put large reference docs in knowledge files, not the system prompt
- Remove skills you no longer use

**Signal that you're hitting limits:** Claude starts forgetting earlier instructions or ignoring parts of the system prompt.

---

## 10. When to Leave claude.ai

**Stay in claude.ai when:** Tasks are interactive, exploratory, or one-off.

**Move to API when:**

| Need | Solution |
| :--- | :--- |
| Automate a chain of calls | Python/Node script, one call per skill |
| Process batches of inputs | Batch API |
| Trigger on events | n8n, Make, or custom webhook |
| Log and audit outputs | API with structured responses |
| Chain 5+ skills reliably | API orchestration, each step explicit |
| Build a tool for others | API-powered app or artifact |

**Move to Claude Code when:**

- Tasks involve your local codebase
- You need file system access
- You want to run and test code in a loop

**Quick decision:**

```
Interactive + exploratory → claude.ai Project
Repeatable + automated   → API
Codebase-connected       → Claude Code
```

---

## Quick-Start Template: System Prompt Structure

Use XML tags to separate sections — Claude parses them unambiguously:

```xml
<role>
[One sentence: what this project assistant does]
</role>

<context>
[2-3 sentences: who you are, your stack, your constraints]
</context>

<rules>
- [Hard rule 1]
- [Hard rule 2]
</rules>

<defaults>
- [Output format]
- [Tone/verbosity]
- [Assumptions to make]
</defaults>

<skills>
## SKILL: [name]
Trigger: [when]
Steps: [numbered]
Output: [format]
</skills>

<workflows>
## WORKFLOW: [name]
Trigger: [keyword]
Steps: [skill chain]
Output: [final format]
</workflows>
```

---

## Examples

Full worked examples are stored separately. Each example includes a system prompt file and a `CONSTITUTION.md` knowledge file, ready to drop into a project.

| Example | Files | Description |
| :--- | :--- | :--- |
| Data Engineering — Headless | `examples/data-eng-headless-system-prompt.md` `examples/data-eng-headless-CONSTITUTION.md` | Headless pipeline assistant (dbt, Spark, Delta Lake, Azure Databricks). JSON-only outputs, medallion architecture, CONSTITUTION.md pattern for rules. |
| Headless Web App Builder | `examples/headless-webapp-system-prompt.md` `examples/headless-webapp-CONSTITUTION.md` | Build assistant for a headless app backed by Unity Catalog. FastAPI intermediary, Azure Event Hub (bidirectional), MS Teams Adaptive Card notifications (workflow + monitoring), schema-sync tooling. Explicit output modes: developer, ci, tool. |

---

*Last updated: March 2026*
