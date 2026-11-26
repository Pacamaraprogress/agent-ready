---
name: agent-ready
description: Build, review, test and ship WebMCP tools, the in-page tools a website registers with document.modelContext so an AI agent in the visitor's browser (Chrome, Edge, Brave) can find a location, look up a product, get a price range or start a request on the site. Field notes from live WordPress and Next.js builds - tool design rules, the three tool hints, answers read from the site's own content, a security pass, caching, page speed, origin-trial tokens, GA4 tool-use events, a testing ladder and a release checklist. Use whenever the user mentions WebMCP, modelContext, registerTool, tools for browser agents, the WebMCP origin trial or polyfill, "Agentic browsing" in Lighthouse, or wants a WordPress plugin or JavaScript bundle that lets AI agents use the site. Not for robots.txt, llms.txt, MCP Server Cards or a general agent-readiness audit (use the agent-readiness skill).
---

# agent-ready

Helps build WebMCP tools for a website: in-page tools that an AI agent running in the visitor's browser can call. The page registers them with `document.modelContext`; the agent finds them by opening the page. Typical tools answer what a visitor would otherwise click around for (a location, product details, a price range, the right form) and hand the visitor to the right page.

Everything practical lives in `references/webmcp-build-guide.md`: field notes from live builds, checked against the spec and Chrome's documentation. **Read it first, every time**, before planning, building, reviewing, testing or shipping tools. The API is still changing: the guide dates its version numbers and trial dates, so re-check them before quoting.

## What to read for each request

| The user wants to | Guide section |
|---|---|
| know whether WebMCP is worth it, which browsers run it, what changed lately | 1. Status and reach |
| plan or design tools, write descriptions, set the tool hints, measure tool use in GA4 | 2. Tool design rules |
| answer from the site's own pages (WordPress, page builders), check the knowledge route's security | 3. Where the answers come from |
| route high-intent questions (guides, warranty, returns, price-list requests) to the right page | 4. Topic map |
| keep the page fast | 5. Page speed |
| get changes seen through page caches with wp-admin access only | 6. Caching |
| get the origin-trial tokens (Chrome, Edge) and put them on the site | 7. Origin-trial token |
| test before anything goes live | 8. Testing ladder |
| ship a release or re-check an older build | 9. Release checklist |
| explain the work to the site owner | 10. Working with the site owner |

## How to work

1. **Plan from visitor journeys.** Start with read-only tools: find a location, product details, a price range, which form to use. A tool never submits a form: a form tool opens the right page, fills the fields and stops, and the visitor sends it.
2. **Build** as a WordPress plugin, theme code or a JavaScript bundle. Register in the top page only, once, and only when `document.modelContext` exists.
3. **Run the testing ladder** in section 8 before anything goes on a live site. The official polyfill is for test pages only: never ship it on a live site.
4. **Ship with the release checklist** in section 9, then confirm the live file is byte-identical to the build.
5. **Say plainly what WebMCP does not do:** it does not change search rankings, and it works only in Chromium-based browsers and the assistants on the spec's implementation status page. Check that page before quoting a browser version or date.

## Browsers

- Tested: Chrome, Edge and Brave, behind a flag or with an origin-trial token. Brave's assistant Leo is expected to use tools by default from Brave 1.102.2.
- Not tested: Opera, Vivaldi.
- Not supported: Safari (WebKit opposes) and Firefox (Mozilla is neutral).

Details and dates are in section 1 of the guide.

## Good habits

- When the guide gains a rule, re-check the builds made before it. In one security review, all the faults were in the oldest build, which predated the rules.
- Never throw from a tool; return an answer the agent can act on. Add `outputSchema` only where it pays, and only with a test that checks every kind of answer against it.
- Keep answers inside Chrome's size guidance and read them from the site's own pages. Re-check any fallback data file against the live site at each release.
- Date every version number, trial date and adoption claim you quote.
- Give the site owner the same two plain facts every time: no ranking effect, Chromium only.

## Not this skill

Making a site readable for AI agents in general (robots.txt for AI bots, Content Signals, llms.txt, Markdown for agents, AGENTS.md, MCP Server Cards, API Catalog, a Cloudflare Agent Readiness audit) belongs to the agent-readiness skill: [github.com/Pacamaraprogress/agent-readiness](https://github.com/Pacamaraprogress/agent-readiness).

## Primary sources

- [WebMCP specification](https://webmachinelearning.github.io/webmcp/) (W3C Web Machine Learning Community Group draft)
- [webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp) and its [implementation status page](https://github.com/webmachinelearning/webmcp/blob/main/implementation-status.md)
- [webmachinelearning/webmcp-types](https://github.com/webmachinelearning/webmcp-types)
- [w3c-oss/webmcp-polyfill](https://github.com/w3c-oss/webmcp-polyfill) (test pages only)
- [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools) and [GoogleChromeLabs/webmcp-extension](https://github.com/GoogleChromeLabs/webmcp-extension)
- [Chrome WebMCP documentation](https://developer.chrome.com/docs/ai/webmcp)
