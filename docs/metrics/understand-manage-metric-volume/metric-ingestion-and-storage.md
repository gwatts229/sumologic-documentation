---
id: metric-ingestion-and-storage
---

# Metric Ingestion and Storage

## Metric ingestion

Sumo does not ingest metric data that is more than one week old.

## Metric retention

Metrics data is stored in Sumo Logic as raw, one minute, and one hour resolutions. It's retained according to the following retention policy:

| Data Type Retained | Retention Period |
|------------------------|----------------------|
| Raw                    | 7 days               |
| 1 minute resolution    | 30 days              |
| 1 hour resolution      | 13 months            |

For historical rollups (1 minute and 1 hour) Sumo calculates the max, min, avg, sum, and count values for a metric per minute or hour.