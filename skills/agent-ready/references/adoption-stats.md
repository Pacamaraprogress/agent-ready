# Adoption Stats

Current adoption data with citation dates. Always note the date when quoting — this space is moving fast and numbers drift by the quarter.

## Cloudflare Radar — top 200,000 sites (Apr 2026)

Source: [Introducing the Agent Readiness Score — Cloudflare](https://blog.cloudflare.com/agent-readiness/)

| Signal | Adoption |
|---|---|
| robots.txt present | 78% |
| AI preferences declared (Content Signals) | 4% |
| Markdown content negotiation (`Accept: text/markdown`) | 3.9% |
| MCP Server Card + API Catalog (combined) | <15 sites (0.0075%) |

**Takeaway:** the overwhelming majority of top sites have not tuned for agents. Even small moves — adding Content Signals, publishing llms.txt, supporting Markdown negotiation — place a site in the top few percent for agent-readiness signaling.

## AI crawler traffic composition

Source: [Cloudflare — AI crawler traffic by purpose and industry (2025)](https://blog.cloudflare.com/ai-crawler-traffic-by-purpose-and-industry/)

Cloudflare's three-purpose taxonomy for AI bots, measured across their network:

- **Training:** ~80% of AI crawl traffic (GPTBot, ClaudeBot, Bytespider, CCBot)
- **Search:** <20% of AI crawl traffic (OAI-SearchBot, Claude-SearchBot, PerplexityBot)
- **User-action:** <5% of AI crawl traffic (ChatGPT-User, Claude-Web, Perplexity-User)

**Takeaway:** most AI requests against a site today are training-related. The user-action slice — where agents act on behalf of a specific person — is small but growing and carries disproportionate commercial value.

## Standard-by-standard backing

| Standard | Backing | Confidence |
|---|---|---|
| robots.txt (RFC 9309) | IETF RFC, universally honored | Highest |
| Sitemaps Protocol | De facto standard since 2005 | Highest |
| Link headers (RFC 8288) | IETF RFC | High |
| API Catalog (RFC 9727) | IETF RFC (Mar 2024) | High |
| OAuth server discovery (RFC 8414 / 9728) | IETF RFCs | High |
| **AGENTS.md** | Linux Foundation / Agentic AI Foundation spec; OpenAI, Google, Cursor, Amp, Aider aligned | **High** |
| HTTP Message Signatures (RFC 9421) | IETF RFC — used by Web Bot Auth | High |
| Markdown content negotiation | Uses IETF Accept-header mechanism; no dedicated spec yet | Medium |
| llms.txt | Proposal (Jeremy Howard / Answer.AI, Sept 2024) | Medium — growing adoption, no major provider confirmed consumption |
| Content Signals | Proposal (contentsignals.org, Cloudflare-championed) | Medium — growing publisher adoption |
| Web Bot Auth | IETF draft, Cloudflare-championed | Medium — infrastructure exists, mostly enforced at edge |
| Agent Skills Discovery | Cloudflare RFC, draft | Low-Medium — first-mover territory |
| MCP Server Card | SEP-2127, marked Final (Extensions Track); found through an AI Catalog (checked Nov 2026) | Low-Medium — adoption <15 sites in top 200k (Apr 2026) |
| AI Catalog / ARD (site-wide list of agent resources) | Two drafts with different file names (checked Nov 2026) | Low — see `protocol-discovery.md` |
| skill.md / agent-permissions.json (Addy Osmani) | Single-author proposal | Low — experimental |

## Who's doing what (illustrative examples)

- **Anthropic docs** — Markdown content negotiation, `.md` URLs, llms.txt, Copy-for-AI button
- **Cloudflare docs** — Markdown rewriting via Cloudflare Rules, extensive `.well-known/` paths
- **Stripe docs** — Markdown negotiation, Copy-for-AI
- **Mintlify-hosted docs (Cursor, many others)** — llms.txt auto-generated across customers since Nov 2025
- **Vercel, Astro** — llms.txt published
- **Google Search Central docs** — published an llms.txt in late 2025 despite public statements that Google Search doesn't consume it

## How to use these numbers

- **Framing recommendations:** "Only 3.9% of top sites support Markdown negotiation — this is a low-effort change that sharply differentiates agent-readiness" is more useful than "you must adopt this."
- **Calibrating priority:** the high-confidence standards (robots.txt, sitemaps, AGENTS.md, API Catalog) should be the default ask. Medium-confidence ones (llms.txt, Content Signals, Markdown negotiation) are opportunistic wins. Low-confidence ones (skill.md, MCP Server Cards without a server) are only worth doing when they correspond to real capabilities.
- **Avoiding stale citations:** before quoting a specific percentage, check whether Cloudflare Radar has published a newer snapshot. The [Radar AI Insights page](https://radar.cloudflare.com/ai-insights) updates continuously.

## Sources

- [Cloudflare — Introducing the Agent Readiness Score](https://blog.cloudflare.com/agent-readiness/)
- [Cloudflare Radar AI Insights](https://radar.cloudflare.com/ai-insights)
- [Cloudflare — AI crawler traffic by purpose and industry](https://blog.cloudflare.com/ai-crawler-traffic-by-purpose-and-industry/)
- [Cloudflare Radar 2025 Year in Review](https://blog.cloudflare.com/radar-2025-year-in-review/)
- [Addy Osmani — Agentic Engine Optimization](https://addyosmani.com/blog/agentic-engine-optimization/)
