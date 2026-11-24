# Audit Checklist

Use this to structure an audit output when running in AUDIT mode. Work through each category, mark present / absent / misconfigured, and translate into the prioritized fix list at the end.

## 1. Discoverability

- [ ] `/robots.txt` returns 200 and is served as `text/plain`
- [ ] robots.txt does not accidentally block AI crawlers via wildcards
- [ ] robots.txt declares Content Signals (`ai-train`, `ai-input`, `search`)
- [ ] Named AI bot rules present (GPTBot, ClaudeBot, PerplexityBot, etc.) — or verified that `User-agent: *` rules are safe for them
- [ ] `Sitemap:` directive in robots.txt
- [ ] `/sitemap.xml` or `/sitemap_index.xml` returns 200
- [ ] Link headers point to `.well-known/` resources (optional, for sites with APIs)

## 2. Content Accessibility

- [ ] Markdown content negotiation works (`curl -H "Accept: text/markdown" <url>`)
- [ ] Or: `.md` URLs exposed for each page
- [ ] `/llms.txt` returns 200 with curated reading list
- [ ] llms.txt under 5,000 tokens
- [ ] Pages within token budgets (quick-start <15k, concepts <20k, reference <25k, ceiling 30k)
- [ ] `<meta name="tokens">` or equivalent meta tag surfaces token count
- [ ] Heading hierarchy clean (one `<h1>`, no skipped levels)
- [ ] "Copy for AI" button present on docs/content pages

## 3. Bot Access Control

- [ ] Content Signals directive published
- [ ] Training vs. search vs. user-action policy is intentional (not accidental wildcard)
- [ ] Edge-level bot management not inadvertently blocking AI bots (Cloudflare, Fastly)
- [ ] If running an agent/crawler: `/.well-known/http-message-signatures-directory` published

## 4. Protocol Discovery (optional, applies if site has APIs/workflows)

- [ ] `/.well-known/api-catalog` returns valid RFC 9727 JSON
- [ ] `/.well-known/agent-skills/index.json` returns valid skill list
- [ ] `/.well-known/ai-catalog.json` (same list at `/.well-known/ard.json`) points to the MCP Server Card, and the card's URL returns valid JSON (only if a remote MCP server exists)
- [ ] The MCP server answers `initialize`, `tools/list` and one `tools/call` from outside the site's network, and from the assistant itself (only if a remote MCP server exists)
- [ ] `/.well-known/oauth-authorization-server` resolves (if OAuth required)
- [ ] `/.well-known/oauth-protected-resource` resolves (if applicable)

## 5. Cross-cutting

- [ ] `/AGENTS.md` present at repo root (for code repos) or site root (optional)
- [ ] AGENTS.md includes build/test/lint commands
- [ ] No `.env`, secrets, or generated files served via `.well-known/` paths

## Prioritized fix list format

Output findings as a prioritized list, not a letter grade:

### Critical (fix first)
- Issues that actively block or corrupt agent use

### High-value easy wins (fix this week)
- Low-effort additions that place the site above adoption curve

### Strategic (as the site grows into them)
- Protocol-discovery files that only make sense with real capabilities behind them
