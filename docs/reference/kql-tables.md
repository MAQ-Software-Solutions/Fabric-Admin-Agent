# KQL Tables Reference

The Fabric Admin Agent relies on an Eventhouse KQL Database (typically named `FabricAdminAgentLogs`) to process and store telemetry data. The real-time detection layer uses a chain of KQL update policies to transform raw events into actionable findings.

## Core Telemetry and Processing Tables

| Table Name | Description | Update Policy Function |
|---|---|---|
| `FabricCapacityEvents` | Raw source table for Capacity Overview Events. Streaming ingestion is disabled (batched instead). | N/A (Source) |
| `CapacityUtilization` | Stores parsed telemetry. Discards outliers (`CUPercentage > 500`) and maintains gap-free, append-only records per capacity. | `CalculateCapacityUtilization()` |
| `CapacityStateEvents` | Tracks state transitions and event states for the capacities. | `CalculateCapacityStateEvents()` |
| `UpsizeFeatures` | Pre-computes pressure, rejection, and delay metrics for throttling risk evaluation. | `EngineerUpsizeFeatures()` |

## Findings and Alerts Tables

| Table Name | Description | Update Policy Function |
|---|---|---|
| `UpsizeFindings_RT` | Real-time table storing raw upscale suggestions based on `EffectivePressure` thresholds. | `ComputeUpsizeDecision()` |
| `UpsizeFindings_Deduped` | Deduplicates real-time upsize findings to one row per SessionId. | `DeduplicateUpsizeFindings()` |
| `IdleLoadFindings_RT` | Real-time table storing raw idle load detection evaluations. | `IdleLoadDetect()` |
| `IdleLoadFindings_Deduped` | Deduplicates idle load sessions, ensuring the capacity is still under the threshold. | `DeduplicateIdleLoad()` |
| `FabricFindingsAlerts` | Final persisted findings (Alerts and Insights) surfaced to the user. Features a 365-day soft-delete retention policy so expired/superseded findings remain recoverable for one year. | `ProcessNewApproachingThrottling()`, `ProcessNewIdleLoad()` |

## Materialized Views and Reference Tables

* **`SustainedLoadDetection_MV`**: A materialized view that continuously rolls `CapacityUtilization` up into 1-minute bins per capacity/SKU (`Min/Max/AvgCUPercentage`, `DataPointsCount`). Used by the idle load detection logic.
* **`CapacitySetting`** / **`SettingTypes`**: Stores per-capacity configuration settings (e.g., Sensitivity Level, Thresholds, Suppression states).
* **`SkuProgression`**: Lookup table used to determine the next tier for upscaling (`CurrentSku → NextSku`).
* **`SkuDowngrade`**: Lookup table used to determine the next lower tier for downscaling.