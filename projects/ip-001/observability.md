# Monitoring and usage reporting

## Usage reporting

```mermaid
flowchart LR
    N[NGINX access logs] --> T[Tagged log sources]
    T --> G[Graylog]
    G --> P[Pipeline classification by domain and content path]
    P --> A[Usage aggregation]
    A --> R[Customer usage reports]
    R --> M[Periodic Telegram delivery]
```

Graylog is used to generate content-level usage reports, including viewer-related metrics and transferred data volumes, grouped by domain and URL subdirectory. These reports support customer charging decisions; the system does not itself perform billing or payment processing. The viewer identity method and byte-counting accuracy are not independently validated here.

## Monitoring

Zabbix monitors physical servers, availability, CPU, memory, disk, network utilization, physical link status, services and URLs. Telegram is used to deliver operational notifications.

```mermaid
flowchart LR
    I[Infrastructure and service checks] --> Z[Zabbix]
    Z --> A[Alert conditions]
    A --> T[Telegram notifications]
```

Actual customer logs, addresses, access tokens, notification destinations and operational thresholds are intentionally excluded.
