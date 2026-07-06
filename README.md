# exascale.build — agent-ready, source-cited US machine-economy data

**MCP endpoint:** `https://api.exascale.build/mcp` (streamable HTTP, no auth) · **Discovery:** [`/.well-known/agent.json`](https://api.exascale.build/.well-known/agent.json) · **Website:** [exascale.build](https://exascale.build) · **Docs:** [exascale.build/docs](https://exascale.build/docs)

An **agent-first** data layer for the US machine-economy buildout. The **power** layer: operating / planned / retired **capacity** (EIA-860M), net **generation** (EIA-923), hourly **demand** (EIA-930 + EIA's national/region rollup), annual **retail sales** (EIA-861), **capacity factor**, the generator **interconnection queues of all seven US ISOs/RTOs**, and **ERCOT day-ahead prices**. The **AI-infrastructure** layer: private **data-center + fab construction** spending (Census C30), data-center-industry + semiconductor **employment** (BLS QCEW), monthly **chip imports** (Census HS-8542), monthly **chip-making-equipment imports** (Census HS-8486), and **semiconductor production & capacity utilization** (Fed G.17, monthly to 1972). The **robotics** layer: monthly **industrial-robot imports** — customs value *and robot counts* — by country of origin (Census HS-10), plus **robotics adoption** — the share of US factories using robots, workers exposed, and robotics capex by industry, state, and plant size (Census Industrial Robotic Equipment). The **space** layer: the **FCC satellite licensing docket** — every space-station filing back to the 1960s, applicant, status, and lifecycle dates (FCC IBFS daily dump). All over **MCP or REST**. Every value is returned **cited to its source**, and any number resolves to its **raw source cell with a matching SHA-256**, so an agent can *verify* what it reports instead of trusting it.

- 🔒 **Read-only**, no authentication, no personal data collected
- ✅ **Verifiable** — `source` + `as_of` + a hash-checked evidence handle on every value
- 🧭 **Agent-first** — discover → describe → query → verify; tools declare what they *won't* answer
- 🆓 **Free & open** today (fair-use rate limits); all four verticals serving

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
| `power.demand` | Hourly electricity demand (MW) by balancing authority | EIA-930 |
| `power.demand_rollup` | EIA's published US48 + region hourly demand totals | EIA Grid Monitor |
| `power.retail_sales` | Annual retail sales, revenue, customers by utility / state / sector | EIA-861 |
| `power.capacity_factor` | How hard a fleet runs — generation ÷ (capacity × hours) | EIA (compound) |
| `power.interconnection_queue` | MISO generator queue — requested (not built) MW, project status | MISO GI queue |
| `power.interconnection_queue_pjm` | PJM queue — requested MW, status | PJM New Services queue |
| `power.interconnection_queue_pjm_cycle` | PJM cluster/cycle grid — cycle phase MW | PJM cycle grid |
| `power.interconnection_queue_caiso` | CAISO queue — requested MW, status | CAISO Public Queue Report |
| `power.interconnection_queue_nyiso` | NYISO queue — requested MW, status (+ large-load projects) | NYISO queue |
| `power.interconnection_queue_isone` | ISO-NE queue — requested MW, status | ISO-NE IRTT |
| `power.interconnection_queue_ercot` | ERCOT queue — requested MW, status | ERCOT GIS report |
| `power.interconnection_queue_spp` | SPP queue — requested MW, status | SPP GI summary |
| `power.price_ercot` | ERCOT day-ahead settlement-point prices ($/MWh) | ERCOT DAM |
| `ai_infrastructure.construction` | Private data-center + semiconductor-fab construction spending ($M/month) | Census C30 |
| `ai_infrastructure.employment` | Data-center-industry + semiconductor employment, establishments, wages by state/county | BLS QCEW |
| `ai_infrastructure.trade` | Monthly US chip-import value (HS-8542, all ICs) by country of origin | Census International Trade |
| `ai_infrastructure.equipment_trade` | Monthly US chip-making-equipment import value (HS-8486, incl. flat-panel machinery) by country of origin | Census International Trade |
| `ai_infrastructure.production` | Monthly semiconductor & electronic-component production index (2017=100) + capacity utilization (%) — NAICS 3344, to 1972 | Fed G.17 |
| `robotics.trade` | Monthly US industrial-robot imports — customs value **and robot counts** — by country of origin (HS-10 8479.50 + 8428.70) | Census International Trade |
| `robotics.adoption` | Share of US manufacturing plants using robots, workers exposed, robotics capex — by industry, state, plant size (ASM 2018-2021 + 2022 Economic Census editions, never spliced) | Census Industrial Robotic Equipment |
| `space.satellite_filings` | The FCC satellite licensing docket — every space-station filing since the 1960s: applicant, type, status, lifecycle dates (new-filing intake ends at the FCC's ICFS cutover, ~mid-2025) | FCC IBFS daily dump |

Queue MW is **requested, not built** capacity — most of it historically withdraws — and the seven ISO queues are **never summed** into a national total (methodologies differ; each response says so).

50 read-only tools: `list_capabilities_v1`, `describe_*` / `query_*` per data point, the generic pair `describe_capability_v1` / `query_capability_v1` (reach any capability by name — including ones shipped after your client cached its tool list), and `get_source_evidence_v1` (re-opens the raw file, re-hashes it, returns the literal source cell). Blocks share entity/geography anchors (`state`, `county_fips`, `eia_plant_id`, …) so an agent can join them itself — e.g. data-center jobs against grid capacity by county. Full reference at **[exascale.build/docs](https://exascale.build/docs)**.

## Why it's different

Built for agents that must be *right*: nothing reaches the agent surface until it passes a fail-closed integrity gate, and every figure is traceable to a hash-verified source cell. Tools also declare what they *don't* answer, so an agent refuses out-of-scope questions instead of guessing.

## Links

Website [exascale.build](https://exascale.build) · Docs [/docs](https://exascale.build/docs) · Privacy [/privacy](https://exascale.build/privacy) · Terms [/terms](https://exascale.build/terms) · Official MCP Registry: `build.exascale/osint` · Contact: info@exascaledata.net
