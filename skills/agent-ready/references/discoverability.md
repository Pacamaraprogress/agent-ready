# Discoverability

How agents find your content. Three mechanisms, all well-standardized: robots.txt (RFC 9309), sitemap.xml (Sitemaps Protocol), and Link headers (RFC 8288).

## robots.txt — the AI-bot-aware version

Most sites have a robots.txt written for Googlebot circa 2010. To be agent-ready, add explicit rules for modern AI crawlers. This is the single biggest reason sites get silently excluded from agent consumption — misconfigured `User-agent: *` rules block AI bots without the operator knowing.

### Named AI user agents (as of Apr 2026)

| Bot | Operator | Purpose |
|---|---|---|
| `GPTBot` | OpenAI | Training |
| `OAI-SearchBot` | OpenAI | Search indexing |
| `ChatGPT-User` | OpenAI | User-triggered fetches |
| `ClaudeBot` | Anthropic | Training + indexing |
| `Claude-Web` | Anthropic | User-triggered fetches |
| `Claude-SearchBot` | Anthropic | Search indexing |
| `PerplexityBot` | Perplexity | Search + answer generation |
| `Perplexity-User` | Perplexity | User-triggered fetches |
| `Google-Extended` | Google | Controls training use (does NOT affect Search) |
| `Googlebot` | Google | Search |
| `Bytespider` | ByteDance | Training |
| `CCBot` | Common Crawl | General corpus (used by many LLMs) |
| `Cohere-AI` | Cohere | Training |
| `Meta-ExternalAgent` | Meta | Training |
| `Applebot-Extended` | Apple | Training (separate from Applebot search) |

The three-purpose taxonomy Cloudflare uses — **training**, **search**, **user action** — is the useful frame. Most operators now publish separate user agents per purpose so you can opt in/out independently.

### Allow-most-bots template

```
# robots.txt — agent-ready, allow-with-attribution default

User-agent: *
Disallow: /admin/
Disallow: /checkout/
Disallow: /cart/
Disallow: /my-account/
Allow: /

Sitemap: https://example.com/sitemap.xml

# Content Signals (contentsignals.org) — granular AI use policy
# ai-train: allow with attribution
# ai-input: allow
# search: allow
Content-Signal: ai-train=y; ai-input=y; search=y
```

### Deny-training-allow-search template

For sites that want to be findable in AI search but don't want their content used as training data:

```
User-agent: *
Disallow: /admin/

# Block AI training specifically
User-agent: GPTBot
Disallow: /

User-agent: ClaudeBot
Disallow: /

User-agent: Google-Extended
Disallow: /

User-agent: Bytespider
Disallow: /

User-agent: CCBot
Disallow: /

# Allow AI search (user-action bots typically follow the * rules)

Sitemap: https://example.com/sitemap.xml

Content-Signal: ai-train=n; ai-input=y; search=y
```

## sitemap.xml

Standard Sitemaps Protocol. For large sites, use a sitemap index (`sitemap_index.xml`) pointing to multiple child sitemaps.

**Agent-aware tweaks:**
- Reference the sitemap in robots.txt (`Sitemap: https://.../sitemap.xml`) — this is how most crawlers find it.
- Keep URL counts under 50,000 per child sitemap (protocol limit).
- Include `<lastmod>` for each URL — agents use this to decide whether to re-fetch cached content.

## Link headers (RFC 8288)

HTTP response headers that point to related resources. Useful for pointing agents at `.well-known/` paths without requiring them to probe for them.

Example response header:
```
Link: </.well-known/api-catalog>; rel="api-catalog",
      </.well-known/ai-catalog.json>; rel="ai-catalog",
      </llms.txt>; rel="llms-index"
```

Most content sites don't need this. Primarily relevant for sites with APIs or MCP servers where the `.well-known/` discovery mechanism needs a hint.

## Common misconfigurations to audit for

- `Disallow: /*?*` patterns that accidentally block AI bots from crawling parameterized pages
- Cloudflare Bot Fight Mode blocking AI bots by default (it does, by design — you may need to add explicit allow rules)
- `Disallow: /` buried in a `User-agent: *` block that catches AI bots
- robots.txt served as `text/html` instead of `text/plain` (some crawlers will ignore it)
- robots.txt returning 404 on the `www.` vs. apex variant

## Sources

- [RFC 9309 — Robots Exclusion Protocol](https://www.rfc-editor.org/rfc/rfc9309)
- [RFC 8288 — Web Linking](https://www.rfc-editor.org/rfc/rfc8288)
- [Sitemaps Protocol](https://www.sitemaps.org/protocol.html)
- [Google crawler overview](https://developers.google.com/search/docs/crawling-indexing/overview-google-crawlers)
- [From Googlebot to GPTBot — Cloudflare](https://blog.cloudflare.com/from-googlebot-to-gptbot-whos-crawling-your-site-in-2025/)
- [Cloudflare AI crawler traffic by purpose](https://blog.cloudflare.com/ai-crawler-traffic-by-purpose-and-industry/)
