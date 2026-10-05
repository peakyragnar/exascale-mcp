# exascale.build

**MCP endpoint:** `https://api.exascale.build/mcp` (streamable HTTP) · **Discovery:** [`/.well-known/agent.json`](https://api.exascale.build/.well-known/agent.json) · **Website:** [exascale.build](https://exascale.build) · **Docs:** [exascale.build/docs](https://exascale.build/docs) · **Pricing:** [exascale.build/pro](https://exascale.build/pro/)

US ISO interconnection queues and cited energy data, over MCP or REST. Also served: EIA power data, ERCOT day-ahead prices, natural gas prices, fuel receipts and plant costs, generator ownership, AI-infrastructure construction, production, and trade, robotics adoption and trade, and FCC satellite filings and notices. Every value returns its `source`, `as_of`, and `source_url`.

## Access

25 free queries, no key required. The free queries are one-time, not a daily refill, and cover the latest snapshot of the open data points. After that, a key is [$49/mo or $490/yr](https://exascale.build/pro/), with a 7-day full refund.

Send a paid key as `Authorization: Bearer <key>`, or connect with the personal MCP URL from the welcome email. Querying a past `as_of` vintage needs a key. These four data points need a key on every query and are not part of the 25 free queries: `power.capacity_accreditation_pjm`, `power.capacity_market_pjm`, `power.capacity_accreditation_miso`, `power.capacity_market_miso`.

`initialize` and `tools/list` do not need a key. Fair-use rate limits still apply.

## Connect

**Claude Code**

```bash
claude mcp add --transport http exascale https://api.exascale.build/mcp
```

**JSON config**

```json
{
  "mcpServers": {
    "exascale": {
      "type": "http",
      "url": "https://api.exascale.build/mcp"
    }
  }
}
```

For a paid key, add `"headers": { "Authorization": "Bearer <key>" }`. A personal MCP URL from the welcome email replaces the URL above.

**Claude.ai** — Settings → Connectors → Add custom connector → `https://api.exascale.build/mcp`. Set Authentication to None for the free queries. Paid users paste the personal MCP link from the welcome email.

**Claude Desktop** — Settings → Connectors → Add custom connector → `https://api.exascale.build/mcp`. Set Authentication to None for the free queries. Paid users paste the personal MCP link from the welcome email. The JSON config above is the same server.

**Cursor** — Settings → MCP → New MCP server, or `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "exascale": {
      "url": "https://api.exascale.build/mcp"
    }
  }
}
```

Paid key:

```json
{
  "mcpServers": {
    "exascale": {
      "url": "https://api.exascale.build/mcp",
      "headers": {
        "Authorization": "Bearer <key>"
      }
    }
  }
}
```

**Cline** — MCP Servers → Configure → Remote Servers → transport Streamable HTTP → `https://api.exascale.build/mcp`. In the MCP settings JSON, set `"type": "streamableHttp"` (a missing type is treated as legacy SSE):

```json
{
  "mcpServers": {
    "exascale": {
      "type": "streamableHttp",
      "url": "https://api.exascale.build/mcp",
      "disabled": false
    }
  }
}
```

Add `"headers": { "Authorization": "Bearer <key>" }` for a paid key.

**REST**

```bash
curl https://api.exascale.build/v1/capabilities
curl -X POST https://api.exascale.build/v1/power/capacity/query \
  -H 'content-type: application/json' \
  -d '{"group_by":["state"],"order_by":"nameplate_mw","top_n":5}'
```

A paid key is the same `Authorization: Bearer <key>` header.

## Capabilities

| Data point | What it answers | Source |
|---|---|---|
| `power.capacity` | Operating, planned, and retired generator capacity (MW) | EIA-860M |
| `power.generation` | Net generation (MWh) by plant, fuel, state, and balancing authority | EIA-923 |
| `power.demand` | Hourly electricity demand (MW) by balancing authority | EIA-930 |
| `power.demand_rollup` | Published US48 and 13-region hourly demand totals | EIA-930 |
| `power.retail_sales` | Annual retail sales, revenue, and customers by utility, state, and sector | EIA-861 |
| `power.capacity_factor` | Generation ÷ (operating nameplate × hours) | EIA-860M + EIA-923 |
| `power.asset_ownership` | Annual owner names and ownership shares by plant and generator | EIA-860 |
| `power.fuel_cost` | Receipt-level delivered fuel cost (cents/MMBtu) and quantity | EIA-923 |
| `power.plant_costs` | As-filed large-steam plant capacity, generation, balances, fuel expense, and O&M lines | FERC Form 1 |
| `natural_gas.prices` | Daily Henry Hub spot ($/MMBtu) and monthly state electric-power prices ($/Mcf) | EIA |
| `power.interconnection_queue` | MISO queue — requested MW and status | MISO GI queue |
| `power.interconnection_queue_pjm` | PJM queue — requested MW and status | PJM New Services queue |
| `power.interconnection_queue_pjm_cycle` | PJM cluster/cycle grid — cycle phase MW | PJM cycle grid |
| `power.interconnection_queue_caiso` | CAISO queue — requested MW and status | CAISO Public Queue Report |
| `power.interconnection_queue_nyiso` | NYISO queue — requested MW and status | NYISO queue |
| `power.interconnection_queue_isone` | ISO-NE queue — requested MW and status | ISO-NE IRTT |
| `power.interconnection_queue_ercot` | ERCOT queue — requested MW and status | ERCOT GIS report |
| `power.interconnection_queue_spp` | SPP queue — requested MW and status | SPP GI summary |
| `power.price_ercot` | ERCOT day-ahead settlement-point prices ($/MWh), including hubs such as HB_NORTH | ERCOT DAM (NP4-190-CD) |
| `power.capacity_accreditation_pjm` | PJM marginal ELCC class ratings (paid key on every query) | PJM |
| `power.capacity_market_pjm` | PJM RPM clearing prices and cleared UCAP (paid key on every query) | PJM |
| `power.capacity_accreditation_miso` | MISO seasonal class accreditation ratios (paid key on every query) | MISO |
| `power.capacity_market_miso` | MISO PRA seasonal zonal clearing prices (paid key on every query) | MISO |
| `ai_infrastructure.construction` | Private data-center and semiconductor-fab construction spending ($M/month) | Census C30 |
| `ai_infrastructure.trade` | Monthly US chip-import value (HS-8542) by country of origin | Census International Trade |
| `ai_infrastructure.equipment_trade` | Monthly US chip-making-equipment import value (HS-8486) by country of origin | Census International Trade |
| `ai_infrastructure.production` | Semiconductor and electronic-component production index and capacity utilization (NAICS 3344) | Fed G.17 |
| `robotics.trade` | Monthly US industrial-robot imports — customs value and robot counts — by country of origin | Census International Trade |
| `robotics.adoption` | Share of plants using robots, workers exposed, and robotics capex, by industry, state, and plant size | Census Industrial Robotic Equipment |
| `space.satellite_filings` | FCC space-station filings back to the 1960s (applicant, type, status, lifecycle dates). New-filing intake ends at the ICFS cutover (~mid-2025). | FCC IBFS |
| `space.satellite_notices` | FCC weekly satellite public notices since the ICFS cutover: applications accepted for filing and actions taken | FCC Space Bureau |

Queue MW is requested capacity, not built capacity. The seven ISO queues are separate data points; methodologies differ, and they are not a national total.

66 read-only tools: `list_capabilities_v1`, a `describe_*` / `query_*` pair for each data point, `describe_capability_v1` / `query_capability_v1` (any capability by id, including ones added after a client cached its tool list), and `get_source_evidence_v1`. `get_source_evidence_v1` re-opens the raw government file, re-checks its SHA-256, and returns the exact cell.

Where the source provides them, rows share anchors such as `state`, `county_fips`, and `eia_plant_id`. Field reference: [exascale.build/docs](https://exascale.build/docs).

## Agent flow

discover → describe → query → verify

1. **discover** — `list_capabilities_v1` lists the data points.
2. **describe** — the matching `describe_*` tool returns filters, groupings, measures, and what that data point does not answer.
3. **query** — filters plus `group_by`, `order_by`, and `top_n`. Every value includes `source`, `as_of`, and `source_url`.
4. **verify** — pass the citation to `get_source_evidence_v1`. It re-opens the raw file, re-checks the SHA-256, and returns the exact cell.

## Example questions

1. How much of PJM's interconnection queue has withdrawn vs reached service?
2. How did ERCOT HB_NORTH day-ahead prices compare with the same week last year?
3. What are the top 5 states for planned capacity by fuel?

Each figure comes back with its `source`, `as_of`, and `source_url`. Pass that citation to `get_source_evidence_v1` to read the exact source cell.

## Links

Website [exascale.build](https://exascale.build) · Docs [/docs](https://exascale.build/docs) · Pricing [/pro](https://exascale.build/pro/) · Privacy [/privacy](https://exascale.build/privacy) · Terms [/terms](https://exascale.build/terms) · Official MCP Registry: `build.exascale/osint` · Contact: info@exascaledata.net
