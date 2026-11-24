# agent-ready

A free Claude skill for **building WebMCP tools for websites** (your own or your clients') and for making a site ready for AI agents. It runs in claude.ai, Claude Desktop and Claude Code. MIT licensed, **updated every week**.

**[Download the latest zip](https://github.com/Pacamaraprogress/agent-ready/releases/latest/download/agent-ready.zip)** · [Releases](https://github.com/Pacamaraprogress/agent-ready/releases) · [Changelog](CHANGELOG.md) · [Türkçe](#türkçe)

## What WebMCP is, in one paragraph

WebMCP lets a web page give the AI agent in a visitor's browser a set of tools, such as "find the nearest location", "what are this product's dimensions" or "fill in the quote form for me". The agent then answers from the site's own content and lands on the right page or form, instead of guessing from a screenshot. Chrome runs it as an origin trial today.

This skill holds field notes from seven real WebMCP builds made in October and November 2026: six for WordPress and one for Next.js, four of them running on live sites. With it, Claude can plan, build, review, test and ship WebMCP tools for any site you work on.

## What it helps you do

- **Build WebMCP tools.** It follows the build guide in [`references/webmcp-build-guide.md`](skills/agent-ready/references/webmcp-build-guide.md):
  - Tool design rules: read-only tools first, and a form is never submitted on the visitor's behalf.
  - The three tool hints (read-only, consequential, untrusted content) and `outputSchema`.
  - Reading answers from the site's own pages, and the WordPress traps (page builders, password-protected pages, caching).
  - A security pass for the route that serves the answers.
  - Counting tool use in GA4 through Tag Manager.
  - The origin-trial token, a testing ladder, a release checklist, and how to work with the site owner.
- **Audit a site.** It checks robots.txt rules for AI bots, llms.txt, AGENTS.md, Content Signals, Markdown content negotiation, MCP Server Cards, the AI Catalog and the API Catalog. You get a fix list in priority order, with the file contents to publish, not just a score.
- **Write the files.** It drafts robots.txt, llms.txt, AGENTS.md and the protocol files for your site type (docs, marketing site, SaaS, e-commerce, code repo). It puts them in a draft folder first, so it never overwrites your existing files.
- **Answer questions.** "Is llms.txt a real standard?" or "Does Google use AGENTS.md?" get an answer with the adoption status and the primary source.

## Install

### claude.ai and Claude Desktop

1. Download **[agent-ready.zip](https://github.com/Pacamaraprogress/agent-ready/releases/latest/download/agent-ready.zip)**. Leave it zipped.
2. In Claude, open **Customize → Skills**, click **+**, choose **Upload skill** and pick the zip.
3. Code execution must be turned on in Settings.

To update, download the zip again from the same link and upload it again. The link always points to the latest release.

### Claude Code: as a plugin (recommended, easy weekly updates)

```
/plugin marketplace add Pacamaraprogress/agent-ready
/plugin install agent-ready@agent-ready
```

To get the weekly update:

```
/plugin marketplace update agent-ready
/plugin update agent-ready@agent-ready
```

### Claude Code: copy by hand

```bash
git clone https://github.com/Pacamaraprogress/agent-ready.git
cp -R agent-ready/skills/agent-ready ~/.claude/skills/
```

To update, run `git -C agent-ready pull` and copy the folder again.

## Try it

You don't need to name the skill; Claude picks it up from requests like these:

- "Plan WebMCP tools for example.com. Which visitor journeys deserve a tool?"
- "Build a WordPress plugin that registers WebMCP tools to find our showrooms and start a quote request. It must never submit the form."
- "Review this WebMCP plugin against the build guide before I ship it."
- "Audit example.com for agent readiness and give me the fixes in priority order."
- "Write robots.txt, llms.txt and AGENTS.md for our docs site."

## Good to know

- **WebMCP tools do not change Google rankings.** Their value is that an agent visiting the site gets correct answers and is sent to the right page or form.
- **Chromium only for now.** Chrome runs WebMCP as an origin trial, and Edge has its own. Brave's assistant Leo is expected to use a site's tools by default from Brave 1.102.2 (expected January 2027). WebKit opposes the proposal; Mozilla is neutral.
- **The API is still changing.** One example: the new `Tool` interface reaches Chrome Stable with Chrome 160 on 5 January 2027. Versions and dates in the guide were checked in November 2026, so re-check them before you quote them. Keeping up with these changes is the reason for the weekly updates.
- The examples come from US local-business and e-commerce sites. Adapt them to your market.
- The files are in English. Claude answers in the language you write in.

## What's inside

```
skills/agent-ready/
├── SKILL.md                         how Claude uses the skill (audit, author, reference)
├── references/
│   ├── webmcp-build-guide.md        the WebMCP field notes
│   ├── protocol-discovery.md        MCP Server Card, AI Catalog, API Catalog, promoting an MCP server
│   ├── content-accessibility.md     Markdown negotiation, llms.txt, token budgets
│   ├── bot-access-control.md        Content Signals, Web Bot Auth, AI bot types
│   ├── discoverability.md           robots.txt for AI bots, sitemaps, Link headers
│   ├── agents-md.md                 the AGENTS.md spec
│   └── adoption-stats.md            adoption numbers with dates and sources
└── assets/
    ├── checklists/audit-checklist.md
    └── templates/                   robots.txt, llms.txt, AGENTS.md, JSON templates
```

## Updates

The skill is updated every week with what new builds and spec changes teach. Each update is a [GitHub release](https://github.com/Pacamaraprogress/agent-ready/releases) with notes, and [CHANGELOG.md](CHANGELOG.md) lists them all. To get notified, click **Watch → Custom → Releases** on this repo.

## Feedback

Tried it on your own site? Please [open an issue](https://github.com/Pacamaraprogress/agent-ready/issues) and say what worked, what didn't, and on which kind of site and browser. Useful findings go into the next update.

## Sources

- [Agentic Engine Optimization — Addy Osmani](https://addyosmani.com/blog/agentic-engine-optimization/)
- [Introducing the Agent Readiness Score — Cloudflare](https://blog.cloudflare.com/agent-readiness/)
- [Chrome WebMCP documentation](https://developer.chrome.com/docs/ai/webmcp)
- [webmachinelearning/webmcp (W3C community group)](https://github.com/webmachinelearning/webmcp)

Each reference file lists its own primary sources.

## License

[MIT](LICENSE). You can use, change and share it, including for commercial work, as long as you keep the copyright notice.

---

## Türkçe

**agent-ready**, web sitelerine (kendi sitenize ya da müşterilerinizin sitelerine) **WebMCP araçları kurmak** ve siteyi yapay zeka ajanlarına hazırlamak için ücretsiz bir Claude skill'i. claude.ai, Claude Desktop ve Claude Code'da çalışır. MIT lisanslıdır, herkes kullanabilir. **Her hafta güncellenir.**

WebMCP, sitenin ziyaretçinin tarayıcısındaki yapay zeka ajanına kendi araçlarını sunmasını sağlar. Örneğin "en yakın şube hangisi", "bu ürünün ölçüleri ne" ya da "teklif formunu doldur". Ajan tahmin etmek yerine sitenin kendi içeriğinden cevap verir. Skill'in içinde canlı sitelerde yapılmış WebMCP kurulumlarından çıkan notlar var: araç tasarım kuralları, WordPress tuzakları, güvenlik kontrolü, GA4'te ölçüm, test merdiveni ve sürüm kontrol listesi.

**Kurulum:**
- **claude.ai / Claude Desktop:** [agent-ready.zip](https://github.com/Pacamaraprogress/agent-ready/releases/latest/download/agent-ready.zip) dosyasını indirin. Zip'i açmadan **Özelleştir → Skills → + → Skill yükle** adımıyla yükleyin. Ayarlar'da kod çalıştırma açık olmalı. Güncellemek için aynı bağlantıdan yeniden indirip yeniden yükleyin.
- **Claude Code:** `/plugin marketplace add Pacamaraprogress/agent-ready`, ardından `/plugin install agent-ready@agent-ready` yazın. Haftalık güncelleme için `/plugin marketplace update agent-ready` ve `/plugin update agent-ready@agent-ready` yazın.

Dosyalar İngilizce, ama Claude'a Türkçe yazarsanız Türkçe cevap verir. WebMCP araçları Google sıralamasını değiştirmez. Şimdilik yalnızca Chromium tabanlı tarayıcılarda çalışır. Geri bildiriminizi [Issues](https://github.com/Pacamaraprogress/agent-ready/issues) bölümüne yazabilirsiniz.
