# Architecture and content flows

These are generalized logical diagrams, not physical deployment maps. Arrows are labeled by flow type; exact production routing and configurations are intentionally omitted.

## Live streaming

```mermaid
flowchart TD
    B[Broadcaster] -->|RTMP publish| R[NGINX streaming gateway]
    subgraph G[Main delivery gateway]
        R --> H[HLS packaging]
        H --> S[Short-lived local segments and playlists]
    end
    S -->|HTTP HLS delivery| V[Direct viewers]
```

Live streams are packaged and served at the main gateway. Temporary HLS files are stored locally; long-term live archiving is outside this design.

## VOD content lifecycle

```mermaid
flowchart TD
    E[Separate encoding team] --> F[Encoded media files]
    F --> U[SFTP upload]
    F --> A[Administration web application]
    U --> M[Persistent media storage]
    A -->|NFS-mounted storage| M
    M -->|NFS| O[Private origin layer]
    O --> C[Private NGINX cache layer]
    C --> L[Main streaming gateway]
    L --> V[Direct viewers]
    L --> D[Downstream delivery environment]
```

Arrows in this diagram indicate content movement, not HTTP request direction. Media encoding is external to the delivery infrastructure. The administration application and SFTP service are externally reachable as required; the main cache and origin layers remain private.

## Distributed CDN request routing

```mermaid
flowchart TD
    V[Local viewers] -->|HTTP requests| A[Remote viewer-facing gateway]
    A --> C[Remote NGINX cache pool]
    C -->|Cache miss: upstream HTTP request| B[Remote upstream routing gateway]
    C -. Cache-hit response via viewer-facing gateway .-> A
    B -->|Inter-site routed traffic| N[Redundant network paths]
    N --> M[Main-site gateway]
    M --> L[Main NGINX streaming gateway]
    L --> P[Main delivery backend as required]
    A -. Mutual role takeover .- B
```

The remote NGINX cache uses the main NGINX streaming gateway as its upstream HTTP endpoint. The intervening remote gateway forwards network traffic; it is not assumed to be an additional HTTP proxy. Cache-hit responses travel back through the viewer-facing gateway to viewers. Some downstream environments also host their own content; their exact local routing is outside this overview.

## Routing boundary

OSPF operates in a single area among participating remote routing nodes and the main-site router. Main-site servers use static default routes to their gateway. Local main-site cache and origin traffic does not require inter-site dynamic routing.
