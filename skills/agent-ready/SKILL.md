---
name: agent-ready
description: Build, review, test and ship WebMCP tools (in-page tools for browser agents), and make a website, docs site, or repo ready for AI agents (Agentic Engine Optimization / AEO). Use this skill whenever the user asks to audit, author, or understand agent-readiness — including robots.txt for AI bots, llms.txt, AGENTS.md, MCP Server Cards, AI Catalog / ARD files, promoting or listing an MCP server (connector links for Claude and ChatGPT, MCP Registry), WebMCP tools (building, reviewing, testing or shipping in-page tools for browser agents, including WordPress plugins), Markdown content negotiation, Web Bot Auth, Cloudflare Agent Readiness Score, or "how do I make my site work with Claude Code / Cursor / GPT agents". Trigger even when the user uses adjacent phrases like "optimize docs for AI", "LLM-friendly site", "agent SEO", "AEO", or asks about specific files like skill.md, api-catalog, or content signals. Not for human-SEO tasks — use a general SEO skill for those.
---

# agent-ready

Helps users make a site, docs portal, or repo consumable by AI coding agents (Claude Code, Cursor, Cline, ChatGPT agents, etc.). The goal is standards-grounded, actionable output — cite primary sources and note adoption status so recommendations are grounded.

## Core framing

The field has two overlapping names:
- **AEO** — Agentic Engine Optimization (Addy Osmani, Apr 2026)
- **Agent Readiness** — Cloudflare's taxonomy, backed by Radar telemetry

This skill uses **Cloudflare's 4-category taxonomy** as the spine because it maps to actual RFCs, and layers Addy's token-budget guidance on top because it's the most useful concrete guidance for docs sites.

The four categories:

| Category | What agents need | Key standards |
|---|---|---|
| Discoverability | Find the content | robots.txt (RFC 9309), sitemap.xml, Link Headers (RFC 8288) |
| Content Accessibility | Read it efficiently | Markdown content negotiation (`Accept: text/markdown`), llms.txt, token budgets |
| Bot Access Control | Know what's allowed | Content Signals, Web Bot Auth (IETF draft), robots.txt AI rules |
| Protocol Discovery | Call APIs / invoke skills | Agent Skills, API Catalog (RFC 9727), MCP Server Card, AI Catalog / ARD, OAuth (RFC 8414/9728), WebMCP |

Plus one cross-cutting concern:
- **AGENTS.md** — Linux Foundation spec (Agentic AI Foundation), backed by OpenAI, Google, Cursor, Amp, Aider. This has the strongest institutional backing in the space.

## Adoption snapshot (Apr 2026, Cloudflare Radar, top 200k sites)

Calibrate recommendations with current adoption — but frame these as *opportunities*, not reasons to skip a standard. Most of these are low-effort, high-upside.

- **78%** have robots.txt (most not yet tuned for AI agents)
- **4%** have declared AI usage preferences (Content Signals)
- **3.9%** support Markdown content negotiation (**highest-ROI change**)
- **<15 sites** in the entire 200k have MCP Server Cards + API Catalogs — genuine first-mover territory

The growth curve is steep. Publishing llms.txt, adding Content Signals, and supporting Markdown negotiation are easy wins today that position the site well as providers formalize support.

## Modes

This skill has four modes. Pick based on what the user is asking. WebMCP comes first because it is what this skill is mainly for.

### 0. WEBMCP — "build / review / test / ship WebMCP tools"

When the user wants in-page tools for browser agents on a site: plan them, build them (WordPress plugin, theme code or a JavaScript bundle), review an existing build, test it, measure tool use or ship a release. Signals: "WebMCP", "modelContext", "registerTool", "tools for the browser agent", "origin trial token", "agent can fill the form".

Approach:
1. **Read `references/webmcp-build-guide.md` first**, every time. It holds the design rules, the WordPress traps, the security pass, the testing ladder and the release checklist.
2. Plan from visitor journeys (find a location, product details, start a request), read-only tools first. A tool never submits a form for the visitor.
3. Build, then run the testing ladder in the guide before anything goes on a live site. Never ship the polyfill on a live site.
4. Say plainly what WebMCP does not do: it does not change search rankings, and it works only in Chromium-based browsers and the clients on the spec's implementation status page.
5. Check the spec's implementation status page before quoting any browser version or date; the API is still changing.


### 1. AUDIT — "check my site"

When the user wants to evaluate an existing site or repo. Signals: "audit", "check", "is my site ready", passes a URL, points at a local repo.

Approach:
1. If a URL is given, prefer the Cloudflare tool's MCP endpoint: `https://isitagentready.com/.well-known/mcp.json` (tool: `scan_site`). Authoritative implementation exposed via standard MCP.
2. If that's not available, use `npx agentic-seo --url <url>` (Addy's tool). Readable, no API key, covers ~80% of the same checks.
3. For a local repo, `cd` into it and run `npx agentic-seo`. Auto-detects Next.js, Docusaurus, Astro, Hugo, Jekyll.
4. If no tools available (e.g. read-only/sandboxed environment), fall back to native checks via `WebFetch`: pull `/robots.txt`, `/llms.txt`, `/sitemap.xml`, `/.well-known/api-catalog`, `/.well-known/agent-skills/index.json`, `/.well-known/ai-catalog.json`, `/.well-known/ard.json` (the two site-wide lists that point to MCP Server Cards; older sites may still use `/.well-known/mcp/server-card.json`), `/AGENTS.md`, and the homepage. Check for Markdown negotiation by noting absence of `.md` URLs in the source.
5. Read the output and **prioritize by leverage** — surface what actually moves the needle:
   - **Critical:** robots.txt accidentally blocks AI crawlers, no Markdown negotiation, token-heavy pages
   - **High-value easy wins:** llms.txt, AGENTS.md, Content Signals in robots.txt
   - **Strategic if applicable:** MCP Server Card, Agent Skills index, API Catalog (recommend when the site has APIs or workflows agents should invoke)

Output a prioritized fix list with concrete file contents, not just a grade.

### 2. AUTHOR — "make my site ready"

When the user wants to generate the actual files. Signals: "add llms.txt", "write robots.txt for AI", "create AGENTS.md", "scaffold agent-ready files".

Ask first:
- Is this a **docs site**, a **marketing site**, a **SaaS app**, an **e-commerce site**, or a **code repo**? The right files differ.
- Do they have a policy on AI training vs. AI search vs. user-triggered agents? (Default to *allow all with attribution via Content Signals* unless told otherwise — opt-in to the agent economy is usually the right move.)
- Do they have APIs or workflows agents should invoke? (If yes, include protocol-discovery files. If no, skip them.)

Then generate from templates in `assets/templates/`. **Default recommendation is full adoption** — all the files that apply to the site type. Each is low-cost to publish and compounds with the others.

| File | When to include | Template |
|---|---|---|
| `robots.txt` | Always | `assets/templates/robots.txt` |
| `AGENTS.md` (repo root) | Code repos, and strongly recommended for any site with docs | `assets/templates/AGENTS.md` |
| `llms.txt` (site root) | Any content site | `assets/templates/llms.txt` |
| `/.well-known/api-catalog` | Site has public APIs | `assets/templates/api-catalog.json` |
| `/.well-known/agent-skills/index.json` | Site offers agent-invocable workflows | `assets/templates/agent-skills-index.json` |
| MCP Server Card (at `<server address>/server-card`) and the list that points to it (`/.well-known/ai-catalog.json`, same file at `/.well-known/ard.json`) | Site runs a remote MCP server | `assets/templates/mcp-server-card.json`, `assets/templates/ai-catalog.json` |
| Landing page, connect links and registry listing for an MCP server | Site runs a remote MCP server that people should add to their assistant | No template: follow "Promoting an MCP server" in `references/protocol-discovery.md` |
| WebMCP tools (in-page JavaScript) | Site has a visitor journey an agent could serve: find a location, get a price range, search products, start a request | No template: follow `references/webmcp-build-guide.md` |
| Markdown content negotiation rule | Any site with textual content | See `references/content-accessibility.md` |
| Content Signals in robots.txt | Any content site | See `references/bot-access-control.md` |
| "Copy for AI" buttons | Docs and marketing pages | See `references/content-accessibility.md` |

Generate to a staging directory first (`./agent-ready-draft/` or similar). Never overwrite an existing file without explicit confirmation — existing files may contain user customizations.

### 3. REFERENCE — "what is X"

When the user asks a question about the landscape. Signals: "what's llms.txt", "does Google use X", "is AGENTS.md a real standard", "should I care about MCP Server Cards".

Answer from `references/` files. Habits:
- State adoption status (proposal / draft RFC / Linux Foundation spec / shipped standard) so the user can weigh effort vs. assurance.
- Cite the primary source, not secondary coverage.
- When adoption is early, frame it as first-mover advantage rather than dismissing. Most of these standards are inexpensive to publish.

## When to read the reference files

Load the relevant file from `references/` as needed:

- `references/discoverability.md` — robots.txt syntax for AI bots, sitemap, Link headers, named AI user agents (GPTBot, ClaudeBot, PerplexityBot, Google-Extended, etc.)
- `references/content-accessibility.md` — Markdown negotiation setup (Cloudflare Rules, nginx, Express), llms.txt format, Addy's token budget ladder (15k/20k/25k/30k)
- `references/bot-access-control.md` — Content Signals directives (ai-train, ai-input, search), Web Bot Auth, distinguishing training vs. search vs. user-action bots
- `references/protocol-discovery.md` — API Catalog RFC 9727, Agent Skills index format, MCP Server Card JSON and where it lives, AI Catalog and ARD (the site-wide list), OAuth discovery, and promoting an MCP server: landing page, connect links for Claude and ChatGPT, JSON-LD, MCP Registry, the tests to run first, who it is worth it for, and why none of it applies to in-page WebMCP tools
- `references/webmcp-build-guide.md` — field notes from live WebMCP builds: tool design rules, the three tool hints (read-only, consequential, untrusted content), reading answers from the site's own content (and the traps), a security pass for the knowledge route (password pages, loopback fetches, inline config, section names), re-checking older plugins against the whole guide and handling copies made elsewhere, topic map for high-intent questions, page speed, caching with wp-admin access only (including served-page copies after an edit or after a page is hidden), tool-use events for GA4 through Tag Manager, origin-trial token, the testing ladder, release checklist, working with the site owner. Read it before building, reviewing, testing or shipping WebMCP tools.
- `references/agents-md.md` — AGENTS.md spec, what goes in it, how it differs from README.md and CLAUDE.md
- `references/adoption-stats.md` — Cloudflare Radar numbers + primary-source citations (refresh check before quoting)

## Good habits

- Treat `skill.md` and `agent-permissions.json` as experimental Addy Osmani proposals. Offer them if the user is enthusiastic about leading-edge adoption, but they're not currently consumed by production agents.
- Cite adoption stats with their date (Apr 2026 Cloudflare Radar) so the user knows the space moves.
- `llms.txt` is a curated reading list, not a sitemap. Keep it under 5,000 tokens — include only the most important entry points.
- Default Content Signals to *allow AI with attribution* unless the user has a reason to restrict. The agent economy is growing; opting out by default often hurts the site's reach.
- WebMCP tools never submit a form for the user and do not change search rankings; say both plainly. Details in `references/webmcp-build-guide.md`.
- Stage drafts in a separate directory and diff against existing files. Customizations in `robots.txt`, `AGENTS.md`, or similar are common and easy to clobber.

## Primary sources

Cite these, not secondary coverage:

- [Agentic Engine Optimization — Addy Osmani](https://addyosmani.com/blog/agentic-engine-optimization/)
- [addyosmani/agentic-seo](https://github.com/addyosmani/agentic-seo)
- [Introducing the Agent Readiness Score — Cloudflare](https://blog.cloudflare.com/agent-readiness/)
- [Cloudflare Agents Week 2026](https://www.cloudflare.com/agents-week/)
- [Cloudflare Agents docs](https://developers.cloudflare.com/agents/)
- [isitagentready.com](https://isitagentready.com/)
- [llms.txt spec (Jeremy Howard, Answer.AI)](https://llmstxt.org/)
- [Content Signals](https://contentsignals.org/)
- [Google Search Central — Crawlers overview](https://developers.google.com/search/docs/crawling-indexing/overview-google-crawlers)
