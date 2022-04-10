---
layout: layouts/home.njk
---

# asmap

Currently opt-in in Bitcoin Core since 0.20, asmap is a change to how Bitcoin Core ensures that it connects to a diverse set of outbound peers beyond just netgroup bucketing. It considers IP-to-ASN mappings to ensure diversity instead. ASNs (Autonomous System Numbers) are a scarcer resource than netgroups, and the mapping passed to Bitcoin Core by default map the gateway ASNs (bottlenecks) for IPs which have sole control over what a node with that IP would see (since P2P connections are currently not encrypted).

### Background reading

* Gleb Naumenko's BitMEX [blog post](https://blog.bitmex.com/call-to-action-testing-and-improving-asmap/)
* [Tools to explore BGP](https://jvns.ca/blog/2021/10/05/tools-to-look-at-bgp-routes/)

### Relevant issues and PRs

* [Issue #16599: ASN-based bucketing of the network nodes](https://github.com/bitcoin/bitcoin/issues/16599)
* [PR #18573: [RFC] `bitcoin-asmap` utility](https://github.com/bitcoin/bitcoin/pull/18573)

### Auxillary tools

* [asmap-rs](https://github.com/rrybarczyk/asmap-rs) - A tool to read and parse RIS raw data from [RIPE NCC](https://www.ripe.net/analyse/internet-measurements/routing-information-service-ris/ris-raw-data) and generate a file of AS mappings.
* [sipa/asmap](https://github.com/sipa/asmap) - This is the original Python version of the proposed `bitcoin-asmap` utility above that compresses the AS mappings file produced by asmap-rs. The `bitcoin-asmap` utility is preferred, but leaving this here as reference.

### How do we make this default in Bitcoin Core?

* Analyse historic RIPE RIS data to get a general idea of how much these mappings change on a monthly basis. This will advise how often the asmap updates need to happen. Current speculation is that asmaps will be useful for a duration on the order of years, so generating a new one per release would be reasonable.
* We need a comparison tool to compare two encoded asmaps to determine how much they have changed. We may also want to compare the original non-encoded files too for auditability.
* We need to have a way to combine/sanitise the RIPE RIS data with another source that is more trusted.
* We need a consensus on what the steps for maintainers will be to update the asmap.
