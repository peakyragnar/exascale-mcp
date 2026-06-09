# exascale.build — agent-ready, source-cited US power data

**MCP endpoint:** `https://api.exascale.build/mcp` (streamable HTTP, no auth) · **Website:** [exascale.build](https://exascale.build) · **Docs:** [exascale.build/docs](https://exascale.build/docs)

An **agent-first** data layer for the US power system. Query operating, planned, and retired generation **capacity** (EIA-860M), net **generation** (EIA-923), and **capacity factor** — over **MCP or REST**. Every value is returned **cited to its source**, and any number resolves to its **raw source cell with a matching SHA-256**, so an agent can *verify* what it reports instead of trusting it.

- 🔒 **Read-only**, no authentication, no personal data collected
- ✅ **Verifiable** — `source` + `as_of` + a hash-checked evidence handle on every value
- 🧭 **Agent-first** — discover → describe → query → verify; tools declare what they *won't* answer
- 🆓 **Free & open** today (fair-use rate limits); industrial & logistics data rolling out

## Connect

**Claude Code**
```bash
claude mcp add --transport http exascale https://api.exascale.build/mcp
```

**Claude.ai / Claude Desktop** — Settings → Connectors → **Add custom connector** → `https://api.exascale.build/mcp` (no authentication).

**Perplexity** — Settings → Connectors → **Custom connector** → Remote → `https://api.exascale.build/mcp` → Open.

**REST**
```bash
curl https://api.exascale.build/v1/capabilities
curl -X POST https://api.exascale.build/v1/power/capacity/query \
  -H 'content-type: application/json' \
  -d '{"group_by":["state"],"order_by":"nameplate_mw","top_n":5}'
```

## Capabilities

| Data point | What it answers | Source |
|---|---|---|
| `power.capacity` | Operating / planned / retired generator capacity (MW) | EIA-860M |
| `power.generation` | Net generation (MWh) by fuel, state, balancing authority | EIA-923 |
| `power.capacity_factor` | How hard a fleet runs — generation ÷ (capacity × hours) | EIA |

8 read-only tools: `list_capabilities`, `describe_*` / `query_*` per data point, and `get_source_evidence` (re-opens the raw file, re-hashes it, returns the literal source cell). Full reference at **[exascale.build/docs](https://exascale.build/docs)**.

## Why it's different

Built for agents that must be *right*: nothing reaches the agent surface until it passes a fail-closed integrity gate, and every figure is traceable to a hash-verified source cell. Tools also declare what they *don't* answer, so an agent refuses out-of-scope questions instead of guessing.

## Links

Website [exascale.build](https://exascale.build) · Docs [/docs](https://exascale.build/docs) · Privacy [/privacy](https://exascale.build/privacy) · Terms [/terms](https://exascale.build/terms) · Official MCP Registry: `build.exascale/osint` · Contact: team@exascale.build
