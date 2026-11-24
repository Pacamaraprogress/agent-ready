# Content Accessibility

How to make content consumable by agents once they've found it. Of the four Cloudflare categories, this one carries the highest real-world leverage — Markdown content negotiation alone delivers up to 80% token reduction on HTML-heavy pages (Cloudflare measurement). Publishing clean Markdown is the single best investment most sites can make today.

## Markdown content negotiation

**The mechanism:** when an agent sends `Accept: text/markdown`, serve Markdown instead of HTML. Same URL, different representation.

**Why it matters:** HTML pages carry navigation, scripts, CSS, cookie banners — often 80% of the tokens are chrome agents don't render. Stripping to Markdown is the single highest-ROI change for most docs and content sites.

**Adoption (Apr 2026):** 3.9% of top 200k sites. Anthropic, Cloudflare, Stripe, and most Mintlify-hosted docs do this. Growing steadily.

### Cloudflare Rules (if the site is behind Cloudflare)

Cloudflare rewrites requests automatically:
- Detect `Accept: text/markdown` in the request
- Rewrite to `/<path>.md` or `/<path>/index.md`
- Serve the Markdown version if it exists

Use a Cloudflare Rule of the form:
```
(http.request.headers["accept"][0] contains "text/markdown")
  → Rewrite URI path: concat(http.request.uri.path, ".md")
```

### nginx

```nginx
map $http_accept $md_suffix {
    default "";
    "~*text/markdown" ".md";
}

location / {
    try_files $uri$md_suffix $uri $uri/ =404;
}
```

### Express / Node

```js
app.use((req, res, next) => {
  if (req.accepts('text/markdown') && !req.path.endsWith('.md')) {
    const mdPath = req.path + '.md';
    if (fs.existsSync(path.join(docsRoot, mdPath))) {
      req.url = mdPath;
      res.type('text/markdown');
    }
  }
  next();
});
```

### The lazy alternative: `.md` URLs

If content negotiation is too much plumbing, expose each page at `<url>.md` as a static file. Anthropic's docs do this. Users and agents can hit either. Downside: two URLs per page — but perfectly acceptable, and easier to audit.

## Token budgets (Addy Osmani)

The ladder — target sizes for documentation pages:

| Page type | Target | Hard ceiling |
|---|---|---|
| Quick-start | 15,000 tokens | — |
| Concepts / guides | 20,000 tokens | — |
| API reference | 25,000 tokens | — |
| Any page | — | **30,000 tokens** |

**Why the ceiling:** pages over ~30k tokens risk truncation by agents or consume a disproportionate share of the context window, which can cause the agent to abandon the task. Addy cites the Cisco Firewall guide (193,217 tokens) as the extreme example.

**How to measure:** use `gpt-tokenizer` (what `agentic-seo` uses) or OpenAI's `tiktoken`. Rough rule of thumb: 1 token ≈ 4 characters of English prose, ≈ 0.75 words. A 30k token page is ~22,500 words — genuinely long.

**How to fix:** split by topic, not by page length. Link between pages. Each page should be self-contained enough that an agent pulling just that page has what it needs.

**Surface the token count** via meta tag so agents can decide whether to fetch:

```html
<meta name="tokens" content="18420">
<meta name="token-count" content="18420">  <!-- some tools look for either -->
```

## llms.txt

**Status:** proposal. Created by Jeremy Howard (Answer.AI), Sept 2024. [Spec at llmstxt.org](https://llmstxt.org/).

**Adoption:** moderate momentum after Mintlify rolled it out across hosted docs in Nov 2025 — Anthropic, Cursor, Vercel, Astro all have one. Direct consumption by major AI providers is still being formalized, but publishing costs almost nothing and signals agent-friendliness to both users and crawlers. Worth doing.

**Format:** Markdown at `/llms.txt`. Curated reading list, not a sitemap.

```markdown
# Product Name

> One-sentence description of what this is.

Short intro paragraph. What problem this solves, what's in this site.

## Docs

- [Quick start](https://example.com/docs/quickstart.md): Get running in 5 minutes
- [Concepts](https://example.com/docs/concepts.md): Core mental model
- [API reference](https://example.com/docs/api.md): Full endpoint list

## Examples

- [Auth flow](https://example.com/examples/auth.md): OAuth 2.1 walkthrough
- [Webhooks](https://example.com/examples/webhooks.md): Setup + verification

## Optional

- [Changelog](https://example.com/changelog.md)
- [Migration guide](https://example.com/migrations.md)
```

**Rules:**
- Keep the whole file under 5,000 tokens. It's an index.
- Link to `.md` URLs where available — agents prefer Markdown.
- The `## Optional` section is special — agents with tight context may skip it.
- One link per line, with a short description after the colon.

**There's also `llms-full.txt`** — the whole site concatenated into one Markdown file. Useful for small sites. Don't do this for anything over ~200k tokens; it defeats the purpose.

## Heading hierarchy and semantic HTML

Even with Markdown negotiation, HTML matters for sites without a Markdown variant. Agents use heading structure to chunk content.

- Exactly one `<h1>` per page.
- Don't skip levels (`<h2>` → `<h4>` confuses structure parsers).
- Use semantic elements: `<article>`, `<section>`, `<nav>`, `<main>`. Not just `<div>`.
- Code in `<pre><code>` with a language class (`class="language-python"`).

## "Copy for AI" UX

Increasingly common on docs sites (Anthropic, Stripe, Supabase have it):

A button on each page that copies either:
- The current page as Markdown, or
- A prompt like "Using the docs at <URL>, help me with..."

Not a standard — a UX pattern that makes the agent-friendly content discoverable to the human. Low effort, high signal to users that the site takes agents seriously.

## Sources

- [Cloudflare — Agent Readiness Score blog](https://blog.cloudflare.com/agent-readiness/) — 80% token reduction measurement
- [llms.txt spec](https://llmstxt.org/)
- [Addy Osmani — AEO](https://addyosmani.com/blog/agentic-engine-optimization/) — token ladder
- [addyosmani/agentic-seo](https://github.com/addyosmani/agentic-seo) — `gpt-tokenizer` integration
