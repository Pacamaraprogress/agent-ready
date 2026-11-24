# Changelog

Weekly updates are planned. Each entry matches a [GitHub release](https://github.com/Pacamaraprogress/agent-ready/releases), whose `agent-ready.zip` is the file to upload to claude.ai or Claude Desktop.

## 2026.11.25

Fixes from an independent review. Factual corrections were checked against their sources; the wording changes are judgement calls.

- Correction to the 2026.11.24 notes below: the Chrome 160 `Tool` interface is *expected* to reach Stable on 5 January 2027, not certain.

- `assets/templates/robots.txt`: the named AI-bot groups are gone. Each had its own `Allow: /`, and a crawler uses only the group that names it (RFC 9309), so those bots skipped the blocked paths. The comments now explain the rule and show how to opt a bot out or give it its own group safely.
- Content Signals now use the official syntax, `yes`/`no` separated by commas (`ai-train=no, search=yes, ai-input=no`), in the template, `bot-access-control.md` and `discoverability.md`.
- Build guide:
  - Continuations need the same **origin**, not only the same site.
  - Local testing uses `chrome://flags/#enable-webmcp-testing` (Chrome docs); `--enable-features=WebMCP` stays for automated tests.
  - The Chrome 160 `Tool` interface is "expected" in Stable on 5 January 2027.
  - The Brave Leo line links spec PR #383 and no longer says "first".
  - `document.modelContext` is the only entry point the spec defines.
  - Tool hints are signals, not guarantees.
  - Each manual redirect hop goes through `wp_safe_remote_get` again.
  - Some examples are more generic: no brand or hosting names, no exact plugin versions or speed scores, and one example uses neutral numbers.
- README:
  - ChatGPT Desktop and Meta Ray-Ban Display rows from the implementation status page.
  - Testing flag and Chrome 160 wording.
  - "Weekly updates planned".
- Dead links replaced: OpenAI Codex AGENTS.md, the Linux Foundation AAIF press release, the Cursor rules page. The MCP Server Card `$schema` line is removed from the template and the example until the official schema address is live.
- `SKILL.md`: a WebMCP mode comes first, and the description leads with WebMCP.
- Version numbers no longer use a hyphen suffix (a suffix reads as a pre-release in semver).

## 2026.11.24-2

- The build guide's source list now starts with the two official WebMCP sources it was missing: the [specification](https://webmachinelearning.github.io/webmcp/) and the [official polyfill repo](https://github.com/w3c-oss/webmcp-polyfill) (`@w3c/webmcp-polyfill` 0.2.0, for test pages only).

## 2026.11.24

First public release.

- WebMCP build guide: field notes from seven builds (six for WordPress, one for Next.js; October and November 2026). It covers tool design rules, the three tool hints, `outputSchema` (with an error branch that matches it too), never throwing errors, reading answers from the site's own content, the WordPress traps, a security pass, tool use in GA4, the origin-trial token, the testing ladder, the release checklist and working with the site owner.
- Status as of November 2026:
  - The Chrome 160 `Tool` interface, which reaches Stable on 5 January 2027.
  - Brave Leo uses WebMCP tools by default from Brave 1.102.2, which Brave expects in January 2027.
  - The W3C `@w3c/webmcp-polyfill` (0.2.0): use it for testing only, never on a live site.
- Also included: a smaller reference on making a site readable for AI agents in general (robots.txt for AI bots, llms.txt, AGENTS.md, Content Signals, Markdown content negotiation, MCP Server Card, AI Catalog, API Catalog).
