---
layout: layouts/home.njk
---

# asmap

Currently opt-in in Bitcoin Core since 0.20, asmap is a change to how Bitcoin Core ensures that it connects to a diverse set of outbound peers beyond just netgroup bucketing. It considers IP-to-ASN mappings to ensure diversity instead. ASNs (Autonomous System Numbers) are a scarcer resource than netgroups, and the mapping passed to Bitcoin Core by default map the gateway ASNs (bottlenecks) for IPs which have sole control over what a node with that IP would see (since P2P connections are currently not encrypted). Essentially, it's an effective mitigation against the Erebus attack, an eclipse attack where the attacker spoofs a part of the internet.

### Background reading

* Gleb Naumenko's BitMEX [blog post](https://blog.bitmex.com/call-to-action-testing-and-improving-asmap/)
* [Tools to explore BGP](https://jvns.ca/blog/2021/10/05/tools-to-look-at-bgp-routes/)

### Relevant issues and PRs

* [Issue #16599: ASN-based bucketing of the network nodes](https://github.com/bitcoin/bitcoin/issues/16599)
* [PR #18573: [RFC] `bitcoin-asmap` utility](https://github.com/bitcoin/bitcoin/pull/18573) (Closed in favour of sipa/asmap)


### asmap Tools
* [sipa/asmap](https://github.com/sipa/asmap) - Bottleneck finding, asmap generation and compresion. Most development happening on `nextgen` branch.
* [asmap-rs](https://github.com/rrybarczyk/asmap-rs) - A Rust tool to read and parse RIS raw data from [RIPE NCC](https://www.ripe.net/analyse/internet-measurements/routing-information-service-ris/ris-raw-data) and generate a file of AS mappings. (sipa/asmap is moving forward as the preferred tool).

### How do we make this default in Bitcoin Core?

* Analyse historic RIPE RIS data to get a general idea of how much these mappings change on a monthly basis. This will advise how often the asmap updates need to happen. Current speculation is that asmaps will be useful for a duration on the order of years, so generating a new one per release would be reasonable.
* We need a comparison tool to compare two encoded asmaps to determine how much they have changed. We may also want to compare the original non-encoded files too for auditability.
* We need to have a way to combine/sanitise the RIPE RIS data with another source that is more trusted (involves individuals collecting their own views of the internet).
* We need a consensus on what the steps for maintainers will be to update the asmap.

