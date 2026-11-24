# AGENTS.md

**Status:** Linux Foundation specification, stewarded by the Agentic AI Foundation. Backed by OpenAI, Google, Cursor, Amp, Aider. This is the most institutionally-weighted standard in the agent-readiness space — genuine cross-vendor alignment rather than a single-company proposal.

## What it is

A Markdown file at the root of a code repository that tells agents how to work in this codebase. Analogous to README.md for humans, but scoped specifically to agent context. Agents pull it into their initial context when they start working in the repo.

## What goes in it

The spec doesn't prescribe rigid sections — the file's job is to be read by a model, not parsed by a machine — but useful content typically includes:

- **Project overview** — what this repo is, what language, what framework
- **Build/run/test commands** — `npm test`, `cargo build`, `make dev`, etc. Agents will guess if you don't tell them; telling them saves round-trips.
- **Conventions** — code style, naming, commit message format
- **Architecture pointers** — where the entry point lives, how major systems relate, which directories are critical vs. generated
- **Things NOT to do** — files not to edit, patterns to avoid, known footguns
- **Testing expectations** — what counts as "done" before a PR

## How it differs from README.md and CLAUDE.md

| File | Audience | Scope |
|---|---|---|
| `README.md` | Humans finding the repo on GitHub | Marketing, setup, contribution |
| `AGENTS.md` | Any agent (Claude Code, Cursor, Amp, Aider, Codex, etc.) | Cross-vendor operational guidance |
| `CLAUDE.md` | Claude Code specifically | Claude-specific hints; supersedes AGENTS.md when present |
| `.cursorrules` | Cursor specifically | Cursor-specific rules |

**Precedence (de facto):** agents typically read their tool-specific file first (e.g., `CLAUDE.md` for Claude Code), falling back to `AGENTS.md`. The Linux Foundation spec encourages tools to respect `AGENTS.md` as the universal baseline.

The practical approach: write `AGENTS.md` as the canonical guidance. If you need Claude-specific overrides, put them in `CLAUDE.md` and keep them minimal.

## Template

```markdown
# AGENTS.md

This file tells AI coding agents how to work effectively in this repository.

## Project

<One paragraph: what this is, what language/framework, what it does.>

## Setup

\`\`\`bash
<install dependencies>
<any one-time setup>
\`\`\`

## Development

- **Run dev server:** \`<command>\`
- **Run tests:** \`<command>\`
- **Lint:** \`<command>\`
- **Type check:** \`<command>\`

Before claiming a task is done, run tests and the type checker. If they fail, fix them or explain why.

## Architecture

- Entry point: \`<path>\`
- Main modules: \`<paths>\`
- Generated/do-not-edit: \`<paths>\`

<Any important mental model: "the X layer calls the Y layer via Z," etc.>

## Conventions

- <code style / naming / commit message format>
- <anything non-obvious a new contributor would miss>

## Don't

- <files, patterns, or approaches to avoid, with brief reasoning>

## Testing

<What "done" means before merging. Which tests must pass. What coverage is expected.>
```

## How agents actually use it

When an agent opens a repo, it looks for this file at the root and loads it into initial context. Well-written AGENTS.md eliminates a large chunk of the "let me explore and figure this out" phase — the agent starts with the shortcuts you'd tell a new hire on day one.

Keep it tight. A 500-line AGENTS.md burns context that could be spent on actual code. Think "smart new hire briefing," not "complete documentation."

## Adoption signal

The Linux Foundation / Agentic AI Foundation backing makes AGENTS.md distinct from most standards in this space. The alignment across OpenAI, Google, Cursor, Amp, and Aider means agents built on any of those platforms will respect it. Publishing AGENTS.md in a repo is the single highest-confidence agent-readiness move for code repositories today.

## Sources

- [Agentic AI Foundation (Linux Foundation)](https://www.linuxfoundation.org/press/linux-foundation-announces-agentic-ai-foundation)
- [AGENTS.md specification](https://agents.md/) (community-maintained)
- [OpenAI Codex — AGENTS.md support](https://help.openai.com/en/articles/codex-agents-md)
- [Cursor — custom instructions](https://docs.cursor.com/context/rules)
