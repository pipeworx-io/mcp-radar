# mcp-radar

Radar MCP — wraps the Radar Geocoding / Search API (radar.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `radar_autocomplete` | Autocomplete an address/place from partial text using Radar. Returns ranked address/place suggestions with coordinates. Optionally bias results near a lat,lng. Example: radar_autocomplete({ query: "841 broadway new", near: "40.73,-73.99", _apiKey: "prj_live_sk_..." }) |
| `radar_geocode` | Geocode an address to coordinates using Radar (forward geocoding). Returns the best-match formatted address with latitude/longitude. Example: radar_geocode({ query: "841 Broadway, New York, NY", _apiKey: "prj_live_sk_..." }) |
| `radar_reverse_geocode` | Reverse geocode coordinates to an address using Radar. Returns the formatted address for a lat/lng. Example: radar_reverse_geocode({ lat: 40.70390, lon: -73.98670, _apiKey: "prj_live_sk_..." }) |
| `radar_ip_geocode` | Geolocate an IP address to city/state/country and approximate coordinates using Radar. Omit `ip` to geolocate the caller. Example: radar_ip_geocode({ ip: "8.8.8.8", _apiKey: "prj_live_sk_..." }) |
| `radar_context` | Get place/geofence/region context for coordinates using Radar. Returns geofences, the containing place, and administrative region (country/state/postal code) for a lat/lng. Example: radar_context({ lat: 40.70390, lon: -73.98670, _apiKey: "prj_live_sk_..." }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "radar": {
      "url": "https://gateway.pipeworx.io/radar/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/radar/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1679+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/radar_autocomplete`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "radar": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-radar"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-radar
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Radar data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
