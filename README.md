# agent-ready: a Claude skill for building WebMCP tools

A free Claude skill for **building WebMCP tools for websites**, whether the site is yours or a client's. It runs in claude.ai, Claude Desktop and Claude Code. MIT licensed, with **weekly updates planned**.

**[Download the latest zip](https://github.com/Pacamaraprogress/agent-ready/releases/latest/download/agent-ready.zip)** · [Releases](https://github.com/Pacamaraprogress/agent-ready/releases) · [Changelog](CHANGELOG.md) · [Türkçe](#türkçe)

## What WebMCP is

WebMCP lets a web page give the AI agent in a visitor's browser a set of tools. Examples: "find the nearest location", "what are this product's dimensions", "fill in the quote form for me". The agent then answers from the site's own content and lands on the right page or form, instead of guessing from a screenshot. The [specification](https://webmachinelearning.github.io/webmcp/) is a draft of the W3C Web Machine Learning Community Group. Chrome runs it as an origin trial today.

This skill holds field notes from seven real WebMCP builds made in October and November 2026: six for WordPress and one for Next.js, four of them running on live sites. With it, Claude can plan, build, review, test and ship WebMCP tools for any site you work on (most of the notes come from WordPress builds).

## What the skill covers

The heart of the skill is the build guide, [`webmcp-build-guide.md`](skills/agent-ready/references/webmcp-build-guide.md):

- **Plan:** the visitor journeys worth a tool, and a topic map for high-intent questions.
- **Design:** read-only tools first, and a form is never submitted on the visitor's behalf. The three tool hints (read-only, consequential, untrusted content), `outputSchema` with an error branch that matches too, and tools that never throw.
- **Build:** reading answers from the site's own pages, and the WordPress traps (page builders, password-protected pages, caching).
- **Secure:** a security pass for the route that serves the answers.
- **Measure:** counting tool use in GA4 through Tag Manager.
- **Ship:** the origin-trial token, a testing ladder, a release checklist, page speed, and how to work with the site owner.

## Browsers and agents (checked November 2026)

In browsers, WebMCP works only in Chromium-based ones for now. The official implementation status page also lists two clients outside the browser (last two rows).

| Browser | Engine | WebMCP status |
| --- | --- | --- |
| Google Chrome | Chromium | Origin trial (Chrome 149 to 162). Without a trial token on the page, turn on `chrome://flags/#enable-webmcp-testing` to see the tools (for automated tests, start Chrome with `--enable-features=WebMCP`). The new `Tool` interface is expected to reach Stable with Chrome 160 on 5 January 2027. |
| Microsoft Edge | Chromium | Edge's own origin trial (Edge 150) with its own token. In tests it also honoured a valid Chrome token. |
| Brave | Chromium | Behind the flag; in tests it honoured a valid Chrome token. Brave's assistant **Leo** is expected to use a site's tools by default from Brave 1.102.2 (expected 5 January 2027; [spec PR #383](https://github.com/webmachinelearning/webmcp/pull/383)), with no test flag on the visitor's side. |
| Opera, Vivaldi and other Chromium-based browsers | Chromium | Same engine, but **not tested**. Don't promise them to a client until you have tried them. |
| Safari | WebKit | Not supported. WebKit's standards position is "oppose". |
| Firefox | Gecko | Not supported. Mozilla's standards position is "neutral". |
| ChatGPT Desktop | Not a browser | Listed as supported on the implementation status page. Not tested with the reference builds. |
| Meta Ray-Ban Display | Not a browser | Listed as "coming soon" for Web Apps, off by default (Developer Mode or rollout). |

The API is still changing, so check the [implementation status page](https://github.com/webmachinelearning/webmcp/blob/main/implementation-status.md) before you quote a version. Keeping up with these changes is the reason for the planned weekly updates.

## Install

### claude.ai and Claude Desktop

1. Download **[agent-ready.zip](https://github.com/Pacamaraprogress/agent-ready/releases/latest/download/agent-ready.zip)**. Leave it zipped.
2. In Claude, open **Customize → Skills**, click **+**, choose **Upload skill** and pick the zip.
3. Code execution must be turned on in Settings.

To update, download the zip again from the same link and upload it again. The link always points to the latest release.

### Claude Code: as a plugin (recommended, easy updates)

```
/plugin marketplace add Pacamaraprogress/agent-ready
/plugin install agent-ready@agent-ready
```

To get the latest update:

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
- "Write a test that checks every tool answer against its outputSchema."
- "How do I count WebMCP tool calls in GA4 without double counting?"

## Good to know

- **WebMCP tools do not change Google rankings.** Their value is that an agent visiting the site gets correct answers and is sent to the right page or form.
- Versions and dates in the guide were checked in November 2026. Re-check them before you quote them.
- The examples come from US local-business and e-commerce sites. Adapt them to your market.
- The files are in English. Claude answers in the language you write in.

## What's inside

```
skills/agent-ready/
├── SKILL.md                         how Claude uses the skill
└── references/
    └── webmcp-build-guide.md        the WebMCP field notes
```

Making a site readable for AI agents in general (robots.txt for AI bots, Content Signals, llms.txt, Markdown for agents, MCP Server Cards, a Cloudflare Agent Readiness audit) is a different job, with its own skill: **[agent-readiness](https://github.com/Pacamaraprogress/agent-readiness)**. Until release 2026.11.26 a smaller version of that material sat in this repo.

## Updates

The plan is to update the skill every week with what new builds and spec changes teach. Each update is a [GitHub release](https://github.com/Pacamaraprogress/agent-ready/releases) with notes, and [CHANGELOG.md](CHANGELOG.md) lists them all. To get notified, click **Watch → Custom → Releases** on this repo.

## Feedback

Tried it on your own site? Please [open an issue](https://github.com/Pacamaraprogress/agent-ready/issues) and say what worked, what didn't, and on which kind of site and browser. Useful findings go into the next update.

## Official WebMCP sources

- [WebMCP specification](https://webmachinelearning.github.io/webmcp/): Draft Community Group Report, W3C Web Machine Learning Community Group
- [webmachinelearning/webmcp](https://github.com/webmachinelearning/webmcp): the spec repo, with the explainer and open issues
  - [Implementation status](https://github.com/webmachinelearning/webmcp/blob/main/implementation-status.md): browsers and assistants
  - [Continuations explainer](https://github.com/webmachinelearning/webmcp/blob/main/continuations-explainer.md): tools that carry over to the next page
- [webmachinelearning/webmcp-types](https://github.com/webmachinelearning/webmcp-types): TypeScript types, including the `Tool` interface
- [w3c-oss/webmcp-polyfill](https://github.com/w3c-oss/webmcp-polyfill): the official polyfill, `@w3c/webmcp-polyfill`. For test pages only, never on a live site.
- [GoogleChromeLabs/webmcp-tools](https://github.com/GoogleChromeLabs/webmcp-tools): the Chrome team's developer tools and demos, including [webmcp-skills](https://github.com/GoogleChromeLabs/webmcp-tools/tree/main/webmcp-skills)
- [GoogleChromeLabs/webmcp-extension](https://github.com/GoogleChromeLabs/webmcp-extension): Google's example agent extension for testing
- Chrome documentation: [WebMCP](https://developer.chrome.com/docs/ai/webmcp) and [WebMCP tool security](https://developer.chrome.com/docs/ai/webmcp/secure-tools)
- Standards positions: [WebKit #670](https://github.com/WebKit/standards-positions/issues/670), [Mozilla #1412](https://github.com/mozilla/standards-positions/issues/1412)

## License

[MIT](LICENSE). You can use, change and share it, including for commercial work, as long as you keep the copyright notice.

---

## Türkçe

**agent-ready**, web sitelerine (kendi sitenize ya da müşterilerinizin sitelerine) **WebMCP araçları kurmak** için ücretsiz bir Claude skill'i. claude.ai, Claude Desktop ve Claude Code'da çalışır. MIT lisanslıdır, herkes kullanabilir. **Haftalık güncelleme planlanıyor.**

WebMCP, sitenin ziyaretçinin tarayıcısındaki yapay zeka ajanına kendi araçlarını sunmasını sağlar. Örneğin "en yakın şube hangisi", "bu ürünün ölçüleri ne" ya da "teklif formunu doldur". Ajan tahmin etmek yerine sitenin kendi içeriğinden cevap verir. Skill'in içinde canlı sitelerde yapılmış WebMCP kurulumlarından çıkan notlar var: araç tasarım kuralları, WordPress tuzakları, güvenlik kontrolü, GA4'te ölçüm, test merdiveni ve sürüm kontrol listesi.

**Hangi tarayıcılarda çalışıyor (Kasım 2026):** Yalnızca Chromium tabanlı tarayıcılarda.
- **Google Chrome:** Origin trial (Chrome 149–162). Sayfada token yoksa araçları görmek için `chrome://flags/#enable-webmcp-testing` ayarını açın (otomatik testlerde Chrome'u `--enable-features=WebMCP` ile başlatın).
- **Microsoft Edge:** Kendi origin trial'ı (Edge 150) ve kendi token'ı var. Testlerde geçerli bir Chrome token'ını da kabul etti.
- **Brave:** Bayrakla açılıyor. Brave'in asistanı **Leo**, Brave 1.102.2 ile (5 Ocak 2027 bekleniyor) sitenin araçlarını varsayılan olarak kullanmaya başlayacak.
- **Opera, Vivaldi ve diğer Chromium tabanlı tarayıcılar:** Motor aynı ama test edilmedi. Denemeden müşteriye söz vermeyin.
- **Safari ve Firefox:** Desteklemiyor.
- **Tarayıcı dışı:** Resmi durum sayfasına göre ChatGPT Desktop destekliyor; Meta Ray-Ban Display için destek "yakında".

**Kurulum:**
- **claude.ai / Claude Desktop:** [agent-ready.zip](https://github.com/Pacamaraprogress/agent-ready/releases/latest/download/agent-ready.zip) dosyasını indirin. Zip'i açmadan **Özelleştir → Skills → + → Skill yükle** adımıyla yükleyin. Ayarlar'da kod çalıştırma açık olmalı. Güncellemek için aynı bağlantıdan yeniden indirip yeniden yükleyin.
- **Claude Code:** `/plugin marketplace add Pacamaraprogress/agent-ready`, ardından `/plugin install agent-ready@agent-ready` yazın. Güncelleme için `/plugin marketplace update agent-ready` ve `/plugin update agent-ready@agent-ready` yazın.

Sitenin genel olarak yapay zeka ajanlarına hazır olması (AI botları için robots.txt, Content Signals, llms.txt, ajanlar için Markdown, MCP Server Card, Cloudflare Agent Readiness denetimi) ayrı bir iş ve ayrı bir skill'i var: [agent-readiness](https://github.com/Pacamaraprogress/agent-readiness).

Dosyalar İngilizce, ama Claude'a Türkçe yazarsanız Türkçe cevap verir. WebMCP araçları Google sıralamasını değiştirmez. Resmi kaynaklar yukarıdaki "Official WebMCP sources" bölümünde. Geri bildiriminizi [Issues](https://github.com/Pacamaraprogress/agent-ready/issues) bölümüne yazabilirsiniz.
