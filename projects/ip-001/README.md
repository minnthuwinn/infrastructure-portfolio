# IP-001 — Multi-Site Live Streaming and VOD Delivery Infrastructure

**Project type:** Anonymized production architecture case study  
**Status:** Draft — pending author and confidentiality review

## Overview

This case study describes my experience designing and operating Linux-based media delivery infrastructure for live and on-demand video. The architecture combines NGINX, distributed caching, private backend networks, dynamic routing, centralized logging and infrastructure monitoring.

The objective was to deliver centrally hosted media to viewers and downstream CDN environments while reducing repeated upstream content retrieval and maintaining operational visibility across sites.

## My contribution

My work included building and maintaining NGINX-based streaming and CDN infrastructure, including downstream CDN deployments. My responsibilities covered infrastructure design, implementation, routing, monitoring, usage reporting and troubleshooting. Video encoding was performed by a separate team; it was not part of my infrastructure implementation.

## Architecture

See [Architecture and content flows](architecture.md) for generalized diagrams of live delivery, VOD ingestion and distributed CDN routing.

| Layer | Technologies and responsibilities |
| --- | --- |
| Live delivery | NGINX RTMP ingestion, HLS packaging, short-lived local media files |
| VOD delivery | NGINX load balancing, CDN caching and origin services |
| Content ingestion | SFTP and administration web application |
| Storage | Persistent media storage and NFS |
| Distributed delivery | Downstream cache nodes and local delivery gateways |
| Network routing | Single-area OSPF across participating routing nodes; static routing at the main-site servers |
| Usage analytics | Tagged NGINX logs, Graylog pipelines and aggregation |
| Monitoring | Zabbix infrastructure and service checks |
| Notifications | Telegram operational alerts and periodic usage reports |

## Engineering decisions

- Keep the main delivery cache and origin layers on private networks, while exposing the required streaming and content-ingestion services.
- Separate short-lived live media from persistent VOD storage. Live packaging and direct delivery occur on the streaming gateway.
- Use disk-based caching in downstream delivery environments. The main streaming gateway acts as their upstream content endpoint.
- Separate viewer-facing load balancing from upstream gateway duties during normal operation; the paired remote gateways are designed to take over each other's responsibilities.
- Use dynamic routing over redundant inter-site links to respond to upstream path failures even when local Ethernet remains up.
- Classify access logs by domain and content path to generate customer-level usage reports.
- Monitor infrastructure, network links, system resources, services and application endpoints centrally.

## Operational perspective

The architecture addresses different failure domains: inter-site connectivity, delivery gateways, storage and application services. Network convergence and application recovery are distinct concerns. A route change does not by itself guarantee uninterrupted video playback.

See [Reliability and limitations](reliability.md) and [Monitoring and reporting](observability.md).

## Evidence and disclosure

This case study is based on my reported production experience. It is not a newly executed lab, an independent audit or a benchmark. The diagrams are generalized and intentionally omit production addresses, exact node counts, customer identities, security rules and proprietary configurations. No measured cache hit ratios, availability percentages, convergence times or customer outcomes are claimed.

Detailed reproducible configuration examples and failure tests may be published separately using synthetic lab environments.
