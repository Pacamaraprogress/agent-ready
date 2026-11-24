# Changelog

The skill is updated every week. Each entry matches a [GitHub release](https://github.com/Pacamaraprogress/agent-ready/releases), whose `agent-ready.zip` is the file to upload to claude.ai or Claude Desktop.

## 2026.11.24

First public release.

- WebMCP build guide: field notes from seven builds (six for WordPress, one for Next.js; October and November 2026). It covers tool design rules, the three tool hints, `outputSchema` (with an error branch that matches it too), never throwing errors, reading answers from the site's own content, the WordPress traps, a security pass, tool use in GA4, the origin-trial token, the testing ladder, the release checklist and working with the site owner.
- Status as of November 2026:
  - The Chrome 160 `Tool` interface, which reaches Stable on 5 January 2027.
  - Brave Leo uses WebMCP tools by default from Brave 1.102.2, which Brave expects in January 2027.
  - The W3C `@w3c/webmcp-polyfill` (0.2.0): use it for testing only, never on a live site.
- Also included: a smaller reference on making a site readable for AI agents in general (robots.txt for AI bots, llms.txt, AGENTS.md, Content Signals, Markdown content negotiation, MCP Server Card, AI Catalog, API Catalog).
