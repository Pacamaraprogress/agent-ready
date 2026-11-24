# Protocol Discovery

How sites advertise APIs, workflows, and MCP servers that agents can invoke. This is the frontier — adoption is in the single digits across the top 200k sites — but the cost of publishing these files is low, and the upside if the site has capabilities agents should reach is high.

Most of the files in this section live under `/.well-known/`. Only publish the ones that apply; don't publish empty stubs.

Sections: API Catalog · Agent Skills Index · MCP Server Card · AI Catalog and ARD · OAuth discovery · Promoting an MCP server · WebMCP · Common mistakes · Sources.

## API Catalog (RFC 9727)

**Status:** published RFC. This one is real, not a draft.

**Purpose:** point agents at your API's OpenAPI specs, documentation, and entry points.

**Path:** `/.well-known/api-catalog`

**Format:** JSON-LD following the RFC 9727 schema.

### Minimal example

```json
{
  "linkset": [
    {
      "anchor": "https://example.com",
      "service-desc": [
        {
          "href": "https://example.com/openapi.yaml",
          "type": "application/openapi+yaml"
        }
      ],
      "service-doc": [
        {
          "href": "https://example.com/docs/api",
          "type": "text/html"
        },
        {
          "href": "https://example.com/docs/api.md",
          "type": "text/markdown"
        }
      ]
    }
  ]
}
```

**When to publish:** any site with a public REST or GraphQL API. If there's an OpenAPI spec, publishing this is a 10-minute task.

## Agent Skills Index

**Status:** Cloudflare RFC, draft. [cloudflare/agent-skills-discovery-rfc](https://github.com/cloudflare/agent-skills-discovery-rfc).

**Purpose:** advertise invocable "skills" — discrete capabilities an agent can trigger, like "create a support ticket" or "check order status."

**Path:** `/.well-known/agent-skills/index.json`

### Example

```json
{
  "version": "1.0",
  "skills": [
    {
      "id": "check-order-status",
      "name": "Check Order Status",
      "description": "Look up the current status of a customer order given an order number.",
      "endpoint": "https://example.com/api/orders/{order_id}",
      "method": "GET",
      "auth": "oauth2",
      "documentation": "https://example.com/docs/api/orders.md",
      "input_schema": {
        "type": "object",
        "properties": {
          "order_id": {"type": "string", "description": "Order ID, format: ORD-XXXXX"}
        },
        "required": ["order_id"]
      }
    }
  ]
}
```

**When to publish:** when the site offers a small, well-defined set of operations agents might invoke on behalf of users (checking status, creating a quote, submitting a form, etc.). Not relevant for purely content sites.

## MCP Server Card

**Status (checked 18 Nov 2026):** SEP-2127 is marked Final (Extensions Track) on modelcontextprotocol.io. The extension's own repository, `modelcontextprotocol/ext-server-card`, still labels itself experimental, so expect small changes. It follows the earlier SEP-1649 draft, which used a `.well-known` path.

**Purpose:** a small public JSON file that describes one *remote* MCP server before anyone connects: its name, version, description and the address to connect to.

**Where it lives:** at any URL. The recommended place is the server's own address plus `/server-card`, so a server at `https://example.com/mcp` serves its card at `https://example.com/mcp/server-card`. Media type: `application/mcp-server-card+json`.

It is **not** a `.well-known` file. Older drafts and many guides still say `/.well-known/mcp/server-card.json` or `/.well-known/mcp.json`; the final text considered that placement and rejected it. Agents find the card through the site's AI Catalog (next section), which gives the card's exact URL.

### Example

```json
{
  "name": "com.example/catalog",
  "version": "1.0.0",
  "title": "Example Catalog",
  "description": "Search the Example catalog, check stock and book a consultation.",
  "websiteUrl": "https://example.com/mcp/",
  "remotes": [
    {
      "type": "streamable-http",
      "url": "https://example.com/mcp",
      "supportedProtocolVersions": ["2025-11-25"]
    }
  ]
}
```

**Rules worth knowing:**

- Required fields: `$schema`, `name`, `version`, `description`. `name` has the form `owner/server`, usually reverse DNS (`com.example/catalog`).
- The card does not list tools, resources or prompts. That is on purpose: a client reads those from the live server, so a stale file cannot mislead it. Earlier drafts had a `capabilities` block; leave it out.
- No credentials, private addresses or anything per-user. The file is public.
- Serve it with `Access-Control-Allow-Origin: *` and a cache header of about an hour.
- Keep it true to the running server. Clients treat the card as a hint and trust the live connection where the two differ.

**When to publish:** only if you actually run a remote MCP server. Publishing a card without a functioning server confuses agents and wastes their context. Build first, publish second.

## AI Catalog and ARD (the site-wide list)

A server card describes one server. The site-wide list says which cards, and which other agent resources, a domain offers. Two drafts cover this job, and they disagree on the file name.

| | AI Catalog | ARD (Agentic Resource Discovery) |
|---|---|---|
| Path | `/.well-known/ai-catalog.json` | `/.well-known/ard.json` |
| Also found by | `Link` relation `ai-catalog` | `Link` relation `ard`, or an `Agentmap:` line in robots.txt |
| Status (checked 18 Nov 2026) | Draft by the Agent Card working group, dated 12 Nov 2026 | v0.91, "Proposal", dated 26 Aug 2026 |
| Who reads it | MCP's discovery text tells clients to fetch it to find server cards | Lighthouse 13.5+ checks it in the "Agentic browsing" audits; ARD registries |

The entry is the same shape in both: an `identifier` (`urn:air:<domain>:<namespace>:<name>`), a `type` (a media type) and exactly one of `url` or inline `data`. ARD says every ARD entry is a well-formed catalog entry, calls `ai-catalog.json` its predecessor's path, and lets consumers read either file. ARD also asks for its own discovery terms on each entry; an entry without them is still valid, but ARD will not surface it.

Entry types: MCP server cards (`application/mcp-server-card+json`), A2A agent cards, agent skills, agent plugins and nested catalogs. There is **no type for WebMCP tools or for ordinary pages**.

### Example

```json
{
  "specVersion": "1.0",
  "entries": [
    {
      "identifier": "urn:air:example.com:mcp:catalog",
      "type": "application/mcp-server-card+json",
      "url": "https://example.com/mcp/server-card"
    }
  ]
}
```

Serve it as `application/ai-catalog+json`. An entry does not repeat the card's title or description; clients read those from the card.

**Which file to publish:** while the two names coexist, serve the same list at both paths. Read the ARD spec before counting on ARD registries, because of its extra terms.

**When to publish:** only when there is a real entry to list. An empty catalog published to turn an audit green helps nobody. Lighthouse shows "Not applicable" when the file is absent, and that does not count against the site.

## OAuth server discovery (RFC 8414, RFC 9728)

**Status:** published RFCs, widely adopted. Not AEO-specific — general OAuth 2.0/2.1 infrastructure.

**Paths:**
- `/.well-known/oauth-authorization-server` (RFC 8414) — authorization server metadata
- `/.well-known/oauth-protected-resource` (RFC 9728) — protected resource metadata

**When to publish:** if your APIs or MCP server require OAuth, these files are how agents discover how to authenticate. Most OAuth libraries generate them automatically.

## Promoting an MCP server

A working endpoint is not found by itself. Nothing below makes an assistant discover a server on its own today. Each layer removes one step between a person hearing about the server and using it. Do them in this order.

| Layer | What it does | Status (checked 18 Nov 2026) |
|---|---|---|
| 1. Landing page | Tells a person what the server does and how to add it | Always worth doing |
| 2. Connect links | Opens the assistant's "add" screen | Claude: a working link, with gaps. ChatGPT: no link format, and only some paid plans can add a server |
| 3. JSON-LD on the page | Describes the endpoint for crawlers | No assistant is known to read it |
| 4. Server card and catalog | Lets a client find the address from the domain | Final SEP, few readers yet |
| 5. MCP Registry | Lists the server in the official index | Preview |

### 1. Landing page

One page on the site that owns the server. It should:

- say what an assistant can do with the server, in the words a user would use;
- give two or three example requests;
- print the server address as text that can be copied;
- say whether sign-in is needed and what leaves the user's chat.

Layers 2 and 3 live on this page. Keep the server on the same registrable domain as the page where possible: a page on one domain advertising a server on another looks like spoofing to a careful client.

### 2. Connect links

**Claude.** A link can open the "Add custom connector" dialog with the name and address filled in:

```
https://claude.ai/customize/connectors?modal=add-custom-connector&connectorName=<name>&connectorUrl=<URL-encoded server address>
```

An Anthropic maintainer confirmed this format on 13 May 2026 (anthropics/claude-ai-mcp issue 74). Claude shows a notice asking the user to check the address, and the user still approves the connection. Build the link with a URL library so the address is encoded.

Known gaps (issue 895, open on 18 Nov 2026): the dialog does not open in iOS Safari, and a visitor who has to sign in first lands on an empty form. So always print the address and the manual steps beside the button: Settings → Connectors → Add custom connector.

**ChatGPT.** No link format that fills in an address is documented. Custom MCP servers can be added by Business and Enterprise/Edu workspaces, and by Pro users for read and fetch tools only (OpenAI help centre, checked 18 Nov 2026). Everyone else reaches a server only as an app OpenAI has reviewed and listed.

- Before approval: give setup steps and say which plans they work on.
- After approval: link to the listing page. Copy its address from the directory; do not build it from a pattern.

**Other assistants.** Print the address and a short setup prompt the user can paste, for example: "Add a connector named Example Catalog for the remote MCP server at https://example.com/mcp. It uses Streamable HTTP and needs no sign-in. List its tools when it is connected."

Leave out a button most of the audience cannot use. A ChatGPT button on a consumer site sends free users to a dead end.

### 3. JSON-LD on the landing page

Two shapes exist. No assistant has been shown to read either, and neither is a ranking factor, so treat this as a cheap label and not as a channel.

- **Schema.org today:** a `WebAPI` (a kind of `Service`) with a `potentialAction` whose `target` is an `EntryPoint` holding the server address.
- **A proposed MCP vocabulary:** `mcp:MCPApp` with `mcp:endpointUrl` and links to each platform's listing. It has been an open idea in the MCP Apps discussions since April 2026 (ext-apps discussion 606) and its vocabulary is not published. Do not use it yet.

```json
{
  "@context": "https://schema.org",
  "@type": "WebAPI",
  "name": "Example Catalog MCP",
  "description": "Lets an AI assistant search the Example catalog and check stock.",
  "documentation": "https://example.com/mcp/",
  "provider": {
    "@type": "Organization",
    "name": "Example",
    "url": "https://example.com"
  },
  "potentialAction": {
    "@type": "UseAction",
    "target": {
      "@type": "EntryPoint",
      "urlTemplate": "https://example.com/mcp",
      "httpMethod": "POST",
      "contentType": "application/json"
    }
  }
}
```

This example was written from the Schema.org type definitions. Run it through validator.schema.org before publishing. Put one block on the landing page, not on every page, and never include a private address, a key or a token.

### 4. Server card and catalog

See the two sections above. They are the only layer a client can follow from a bare domain name to a working connection.

### 5. MCP Registry

The official index at `registry.modelcontextprotocol.io`, in preview. It holds metadata only and points to where a server runs or is packaged.

Publish with the `mcp-publisher` tool: `init` writes a `server.json`, `login` proves the namespace, `publish` sends it. The name takes one of two forms:

- `io.github.<user>/<server>`, proved by signing in to GitHub;
- `com.example/<server>`, proved by a DNS record or a file on the domain.

Use the domain form for a client's server, so the listing belongs to the business and not to a developer's account.

### Test before promoting

Three calls must work from outside the site's own network: `initialize`, `tools/list`, and one `tools/call` with a real question.

1. Run them with the MCP Inspector (`npx @modelcontextprotocol/inspector`).
2. Add the server in the assistant itself and ask a question that needs a tool.

The second test matters. Hosted assistants call the server from their own data centres, and a firewall or bot rule that blocks those requests makes every button above useless while the Inspector on a laptop still passes.

### Who it is for

Ask who would add the connector. It fits tools, data and catalogues whose users already work inside an assistant: a SaaS product, a research or SEO data source, a large product catalogue. It rarely fits a local service business, whose customers will not add a connector to ask one question.

A public server is also a public endpoint. It needs rate limits, monitoring and upkeep, and anyone's assistant can call it.

### In-page WebMCP tools are a different case

WebMCP tools have no address. An agent finds them by opening the page, so there is nothing to link, list or register. Connect links, server cards, catalogs and the registry do not apply, and the catalog formats have no entry type for them.

The WebMCP group has open proposals for announcing a site's tools before a visit (webmachinelearning/webmcp issues 340 and 341, both open on 18 Nov 2026). None is adopted. Until one is, the only thing a site can add is a plain description of its tools in `llms.txt` or on a page, and no agent is known to act on it.

## WebMCP

**Status (checked Nov 2026):** Chrome origin trial (Chrome 149–162); Edge runs its own trial; WebKit opposes, Mozilla is neutral. Chromium only for now.

**Purpose:** expose MCP-like capabilities directly from a web page via JavaScript, without requiring a separate MCP server. The agent runs in the visitor's browser and calls tools the page registers (`document.modelContext` / `navigator.modelContext`).

**When to build:** the site has a clear visitor journey an agent could serve (find a location, get a price range, start a request, search products) and the owner accepts Chromium-only reach during the trial. It does not affect search rankings.

It can be built and shipped today with an origin-trial token. For design rules, data sources, caching, token setup, the testing ladder and a release checklist, read `webmcp-build-guide.md`.

## Common mistakes

- **Publishing empty files.** An `agent-skills/index.json` with zero skills is worse than no file at all. The same goes for an empty AI Catalog.
- **Pointing to Swagger UI instead of the raw OpenAPI spec.** `service-desc` should be the spec; `service-doc` is where human-readable docs go.
- **Listing an MCP server that doesn't exist yet.** Agents will try to connect and fail.
- **Putting the server card under `/.well-known/`.** That was the older draft. The card sits beside the server (`<server address>/server-card`) and the catalog points to it.
- **Listing tools in the server card.** The final format leaves them out; clients read them from the live server.
- **A connect button with nothing beside it.** The prefilled link fails on some devices, so the address and manual steps must be on the page too.
- **Testing only from your own machine.** The assistant's servers make the real calls, and a firewall can block them.
- **Forgetting OAuth metadata.** If agents need to authenticate, the discovery URLs need to resolve.

## Sources

- [RFC 9727 — API Catalog](https://www.rfc-editor.org/rfc/rfc9727)
- [RFC 8414 — OAuth Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414)
- [RFC 9728 — OAuth Protected Resource Metadata](https://www.rfc-editor.org/rfc/rfc9728)
- [Cloudflare Agent Skills Discovery RFC](https://github.com/cloudflare/agent-skills-discovery-rfc)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [Cloudflare Agent Readiness Score](https://blog.cloudflare.com/agent-readiness/)
- [SEP-2127 — MCP Server Cards](https://modelcontextprotocol.io/seps/2127-mcp-server-cards)
- [Server Card extension: discovery text, schema and examples](https://github.com/modelcontextprotocol/ext-server-card/blob/main/docs/discovery.md)
- [AI Catalog specification](https://ai-catalog.io/spec/) and [repository](https://github.com/Agent-Card/ai-catalog)
- [ARD specification](https://agenticresourcediscovery.org/spec/)
- [MCP Registry — about](https://modelcontextprotocol.io/registry/about) and [publishing quickstart](https://modelcontextprotocol.io/registry/quickstart)
- [Claude connector link, maintainer's answer (issue 74)](https://github.com/anthropics/claude-ai-mcp/issues/74) and [its open bug (issue 895)](https://github.com/anthropics/claude-ai-mcp/issues/895)
- [OpenAI help centre — Connecting custom MCP servers in ChatGPT](https://help.openai.com/en/articles/12584461-connecting-custom-mcp-servers-in-chatgpt)
- [Schema.org WebAPI](https://schema.org/WebAPI)
- [Proposal: JSON-LD markup for MCP App discovery (ext-apps discussion 606)](https://github.com/modelcontextprotocol/ext-apps/discussions/606)
- [WordLift — How to Promote Your MCP Server (18 Nov 2026)](https://wordlift.io/blog/en/how-to-promote-your-mcp-server/)
- WebMCP proposals for announcing tools before a visit: [issue 340](https://github.com/webmachinelearning/webmcp/issues/340), [issue 341](https://github.com/webmachinelearning/webmcp/issues/341)
