# mcp-radar

Radar MCP — wraps the Radar Geocoding / Search API (radar.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1866+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

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

Both URLs reach the same gateway and the same 1866+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/radar_autocomplete`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-radar` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/radar/mcp` endpoint shown above. It needs network access;
there is no offline mode.

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

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

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
