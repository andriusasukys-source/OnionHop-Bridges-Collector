# OnionHop Bridges Collector

Automatically collects, validates and archives Tor bridges for the
[OnionHop](https://github.com/center2055/OnionHop) app. A GitHub Action runs
hourly to fetch fresh bridges from the official Tor Project and community
sources, then TCP/TLS-tests them.

_Last updated: 2026-11-11 05:58 UTC_

## Local development

The collector requires Python 3.12 or newer and the packages in
[`requirements.txt`](requirements.txt). On Debian or Ubuntu, install the
optional `obfs4proxy` binary to enable real obfs4 handshake checks:

```sh
sudo apt-get update
sudo apt-get install -y obfs4proxy
```

Create a virtual environment and install the Python dependencies:

```sh
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Check the environment without making network requests or changing collected
bridge data:

```sh
python -m py_compile main.py
python -c "import requests; from bs4 import BeautifulSoup"
```

Run `python main.py` to perform a live collection. It fetches bridge sources,
probes bridge connectivity, and updates files in `bridge/` and the counts in
this README. Network access is required; review those changes before committing.
If `obfs4proxy` is unavailable, the collector skips the obfs4 handshake check
and retains its TCP-tested results.

## Pooled transports

These have large, rotating bridge pools that the Tor Project and community
sources distribute, so they are scraped fresh and connectivity-tested each run.

| Transport | Tested & Active (IPv4) | Fresh 72h (IPv4) | Full Archive (IPv4) | Full Archive (IPv6) |
| :--- | :--- | :--- | :--- | :--- |
| **obfs4** | [obfs4_tested.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/obfs4_tested.txt) (212) | [obfs4_72h.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/obfs4_72h.txt) (14) | [obfs4.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/obfs4.txt) (771) | [obfs4_ipv6.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/obfs4_ipv6.txt) (373) |
| **webtunnel** | [webtunnel_tested.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/webtunnel_tested.txt) (198) | [webtunnel_72h.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/webtunnel_72h.txt) (11) | [webtunnel.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/webtunnel.txt) (316) | [webtunnel_ipv6.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/webtunnel_ipv6.txt) (269) |
| **vanilla** | [vanilla_tested.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/vanilla_tested.txt) (157) | [vanilla_72h.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/vanilla_72h.txt) (4) | [vanilla.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/vanilla.txt) (506) | [vanilla_ipv6.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/vanilla_ipv6.txt) (72) |

IPv6 variants exist for every pooled list (e.g. `obfs4_ipv6_tested.txt`,
`obfs4_ipv6_72h.txt`). Note: IPv6 `*_tested` lists may be empty because CI
runners often lack IPv6 connectivity — prefer IPv4 where possible.

## Fronted transports

Snowflake, meek and conjure have **no rotating pool** — they reach Tor through a
broker and/or domain fronting using a small set of fixed default bridge lines
(the ones shipped with Tor Browser; the listed IP is a placeholder). These lists
are therefore small and essentially static. The `_tested` list contains the
lines whose broker/front host answered on port 443 (there is no `_72h` or
`_ipv6` variant for these).

| Transport | Tested & Active | Default lines |
| :--- | :--- | :--- |
| **snowflake** | [snowflake_tested.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/snowflake_tested.txt) (0) | [snowflake.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/snowflake.txt) (2) |
| **meek-azure** | [meek-azure_tested.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/meek-azure_tested.txt) (0) | [meek-azure.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/meek-azure.txt) (1) |
| **conjure** | [conjure_tested.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/conjure_tested.txt) (1) | [conjure.txt](https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/conjure.txt) (1) |

## Consuming these lists

Fetch the raw files directly, e.g.:

```
https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/obfs4_tested.txt
https://raw.githubusercontent.com/center2055/OnionHop-Bridges-Collector/main/bridge/snowflake_tested.txt
```

For censorship resilience, mirror the same paths behind GitHub Pages, a CDN,
and/or a self-hosted domain, and try them in order. OnionHop's in-app
**Bridge Scanner** reads these files and tests them (TCP for pooled transports,
broker/front reachability for fronted ones) so users can pick the bridges that
actually work in their region.

## Sources

- Official Tor BridgeDB: `https://bridges.torproject.org`
- Community seed: [Delta-Kronecker/Tor-Bridges-Collector](https://github.com/Delta-Kronecker/Tor-Bridges-Collector) — this project is **derived from** it (see License)
- Fronted defaults: the snowflake/meek/conjure bridge lines shipped with Tor Browser

## License

Licensed under the **GNU Affero General Public License v3.0 (AGPL-3.0)** — see
[`LICENSE`](LICENSE) and [`NOTICE`](NOTICE).

This project is a derivative work, adapted from
[Delta-Kronecker/Tor-Bridges-Collector](https://github.com/Delta-Kronecker/Tor-Bridges-Collector)
(also AGPL-3.0); it is released under the same license with the original
author's copyright preserved.

Tor bridge lines (addresses, fingerprints, transport parameters) are public
data published by the Tor network, not original work of this project.

## Disclaimer

For educational and circumvention purposes. Use bridges responsibly and in
accordance with your local laws.
