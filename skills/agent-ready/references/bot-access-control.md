# Bot Access Control

How to express what agents may do with your content, and how to verify agents are who they claim to be. Two main standards: Content Signals (directives in robots.txt) and Web Bot Auth (HTTP request signing).

## Content Signals

**Source:** [contentsignals.org](https://contentsignals.org/)

**Status:** proposal championed by Cloudflare, adoption growing. Designed to give publishers granular control that robots.txt alone can't express — "block training but allow search" is the classic case.

**Three directives:**

| Directive | Meaning |
|---|---|
| `ai-train` | Content may be used for training AI models |
| `ai-input` | Content may be used as context in a live AI response (RAG, grounding) |
| `search` | Content may be indexed for search (including AI search) |

**Values:** `y` (yes, allowed) or `n` (no, denied).

**Placement:** Served as a directive in `robots.txt`, either scoped to a user agent or applied site-wide.

### Examples

**Allow everything (the default stance this skill recommends unless the user has a reason):**
```
User-agent: *
Allow: /
Content-Signal: ai-train=y; ai-input=y; search=y
```

**Allow search and grounding, deny training:**
```
User-agent: *
Allow: /
Content-Signal: ai-train=n; ai-input=y; search=y
```

**Per-bot overrides:**
```
User-agent: GPTBot
Disallow: /
Content-Signal: ai-train=n

User-agent: ClaudeBot
Allow: /
Content-Signal: ai-train=y; ai-input=y
```

### Current adoption

Apr 2026 Cloudflare Radar: **4%** of top 200k sites declare AI preferences via Content Signals. Publishing a Content Signal directive is a ~5-minute change that positions the site in the top 4% for agent-respectful signaling.

## Web Bot Auth

**Source:** IETF draft, championed by Cloudflare. [Cloudflare blog post on Web Bot Auth](https://blog.cloudflare.com/web-bot-auth/) covers the details.

**Problem it solves:** anyone can claim to be `GPTBot` in their User-Agent header. Web Bot Auth lets legitimate bots cryptographically prove they're who they say they are, so publishers can allow real bots and block impersonators.

**How it works:**
1. The bot signs each HTTP request using HTTP Message Signatures (RFC 9421).
2. The bot's public key is published at `/.well-known/http-message-signatures-directory` on the operator's domain.
3. The server validates the signature against the published key.

**What publishers do:** nothing required to *use* this as a publisher — Cloudflare (and similar edge providers) validate signatures automatically. If you run your own origin, you can validate using any HTTP Message Signatures library.

**What site operators running bots do:** publish your key directory at `/.well-known/http-message-signatures-directory`, sign outbound requests. Primarily relevant if you're building an agent or crawler, not a destination site.

## Distinguishing bot purposes

Cloudflare's three-purpose taxonomy is the useful frame:

| Purpose | Examples | Share of AI crawl traffic |
|---|---|---|
| **Training** | GPTBot, ClaudeBot, Bytespider, CCBot | ~80% |
| **Search** | OAI-SearchBot, Claude-SearchBot, PerplexityBot | <20% |
| **User action** | ChatGPT-User, Claude-Web, Perplexity-User | <5% |

**Why this matters for policy:** most sites benefit from allowing search and user-action bots (they drive visibility and conversions) while deciding independently about training. Content Signals lets you express this.

## Blocking vs. throttling

Hard-blocking bots with `Disallow: /` isn't always the right answer. Training bots can crawl aggressively — a single crawler pulling a large site repeatedly can spike infrastructure costs. Options, in order of increasing restriction:

1. **Allow + Content Signal** — permits use with declared policy
2. **Rate-limit at edge** — Cloudflare, Fastly, and similar offer bot-specific rate limits
3. **Allow with crawl-delay** in robots.txt — honored by some crawlers, ignored by others
4. **Disallow specific bots** — blocks compliant crawlers; non-compliant ones ignore this
5. **Edge-level block** — Cloudflare Bot Fight Mode or equivalent enforces regardless of bot compliance

## Sources

- [Content Signals](https://contentsignals.org/)
- [Web Bot Auth — Cloudflare](https://blog.cloudflare.com/web-bot-auth/)
- [RFC 9421 — HTTP Message Signatures](https://www.rfc-editor.org/rfc/rfc9421)
- [AI crawler traffic by purpose — Cloudflare](https://blog.cloudflare.com/ai-crawler-traffic-by-purpose-and-industry/)
- [Cloudflare Agent Readiness Score](https://blog.cloudflare.com/agent-readiness/)
