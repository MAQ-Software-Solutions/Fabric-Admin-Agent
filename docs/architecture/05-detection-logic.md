# Detection Logic

> **Status:** Idle Capacity and Throttling Risk are now fully documented from the actual KQL schema (`DatabaseSchema.kql` — all tables, functions, the materialized view, and update policies for the `FabricAdminAgentLogs` KQL database). The three Batch Insights recommendation types (Capacity Scaling, F-SKU Schedule, Workspace Reallocation) still run from notebooks outside this schema — see "Open Items."

## Detection Scenarios

The real-time detection layer (see [01-overview.md](01-overview.md), [02-data-flow.md](02-data-flow.md)) currently evaluates two core capacity health scenarios against streaming Capacity Overview Events:

- **Throttling Risk** — flags capacities approaching utilization levels that may cause Fabric throttling and degraded user experience. Resolves to an **upscale** suggestion.
- **Idle Capacity** — flags consistently underutilized capacities that represent cost-optimization / SKU right-sizing opportunities. Resolves to a **downscale** suggestion.

Batch Insights (see [02-data-flow.md](02-data-flow.md)) adds three longer-horizon, historically-driven recommendation types, generated from Capacity Metrics App and Capacity Overview Event data rather than real-time streaming:

- **Capacity Scaling Recommendations** — SKU upgrade/downgrade/right-sizing suggestions based on historical usage trends.
- **F-SKU Schedule Recommendations** — optimal pause/resume schedules for eligible capacities.
- **Workspace Reallocation Recommendations** — redistributing workspaces across capacities to reduce contention.

## Real-Time Detection Pipeline

Both real-time scenarios run as a chain of KQL **update policies** — each table below fires automatically off its source table as new rows land, all defined in `FabricAdminAgentLogs`:

| Source Table | Update Policy Function | Target Table | Transactional |
|---|---|---|---|
| `FabricCapacityEvents` | `CalculateCapacityUtilization()` | `CapacityUtilization` | Yes |
| `FabricCapacityEvents` | `CalculateCapacityStateEvents()` | `CapacityStateEvents` | Yes |
| `CapacityUtilization` | `EngineerUpsizeFeatures()` | `UpsizeFeatures` | Yes |
| `CapacityUtilization` | `IdleLoadDetect()` | `IdleLoadFindings_RT` | No |
| `UpsizeFeatures` | `ComputeUpsizeDecision()` | `UpsizeFindings_RT` | Yes |
| `UpsizeFindings_RT` | `DeduplicateUpsizeFindings()` | `UpsizeFindings_Deduped` | Yes |
| `UpsizeFindings_Deduped` | `ProcessNewApproachingThrottling()` | `FabricFindingsAlerts` | Yes |
| `IdleLoadFindings_RT` | `DeduplicateIdleLoad()` | `IdleLoadFindings_Deduped` | No |
| `IdleLoadFindings_Deduped` | `ProcessNewIdleLoad()` | `FabricFindingsAlerts` | No |

`FabricCapacityEvents` itself has streaming ingestion disabled (batched instead), and `FabricFindingsAlerts` has a 30-day soft-delete retention policy so expired/superseded findings remain recoverable for a month.

`CalculateCapacityUtilization()` parses the raw `data` JSON off each Capacity Overview Event, derives `TotalCapacityMs = <SKU number> × 30 × 1000` and `CUPercentage`, discards any row where `CUPercentage > 500` as a data-quality outlier, and only ingests events past the capacity's last processed window (`WindowStartTime >= LatestWindowEnd`) — this is what keeps `CapacityUtilization` append-only and gap-free per capacity.

`SustainedLoadDetection_MV` is a materialized view (not part of the update-policy chain above) that continuously rolls `CapacityUtilization` up into 1-minute bins per capacity/SKU (`Min/Max/AvgCUPercentage`, `DataPointsCount`) — this is the table `IdleLoadDetect()` actually scans for its rolling-window idle check.

## Per-Capacity Configuration

Configured per onboarded capacity under **Configuration → CapacityMonitoringAgent**, with a section for each scenario (Throttling Risk, Idle Capacity, Capacity Throttling, F-SKU Schedule Recommendation, AI Powered Capacity Allocation, AI Recommendation for Capacity):

| Setting | Description |
|---|---|
| Sensitivity Level | Detection sensitivity level for anomaly findings |
| Email | Comma-separated list of recipients for alert notifications |
| Suppress Finding | Temporarily disables finding generation for the capacity |
| Suppress Findings Until | Date/time until which finding generation stays suppressed |

Additional per-scenario configuration:

- **F-SKU Schedule** — enable the capacity, then create Pause / Resume / Upscale / Downscale actions with One-Time, Daily, or Weekly recurrence.
- **Real-Time Monitoring** — enable the capacity, then set a start date, end date, operating days, and daily monitoring time window; notification recipients configured separately.
- **Auto-Scale** — enable the capacity, then set scaling limits (min/max SKU guardrails) and an autoscaling schedule; notification recipients configured separately.

## Idle Capacity Detection (`IdleLoadDetect()`)

Runs against the `SustainedLoadDetection_MV` materialized view (30-second CU windows per capacity), stored in the `DetectionFunctions` KQL folder.

**Per-capacity config** (from `CapacitySetting`/`SettingTypes` where `SettingTypeName == "Idle Capacity"`, driven by the workload UI's Sensitivity/Threshold settings):

| Parameter | Source | Default if unset/invalid |
|---|---|---|
| `Threshold` | `Threshold` column, falling back to `Threshold1` | — (required) |
| `idleDurationMinutes` | `ConfigurationJSON` (or `DefaultConfigurationJSON`) | 15 minutes |
| `sessionGapMinutes` | `ConfigurationJSON` (or `DefaultConfigurationJSON`) | 5 minutes |

**Utilization calculation**, per 30-second event window:

- `CUBudgetPerWindow = BaseCapacityUnits × 1000 × 30` (CU-ms budget for a 30s window)
- `TotalCULoad = CurrentCUMs + OverageTotalCUMs`
- `EffectiveUtilizationPct = TotalCULoad / CUBudgetPerWindow × 100`
- A window's `MaxCUPercentage` (from the sustained-load MV) is what's compared against `Threshold`.

**Trigger condition — sustained idle window:** for a rolling window of `DurationMins` consecutive 30-second samples on the capacity's *currently active* SKU:

1. The full window must be present (`TotalWindows == DurationMins`) — no trigger on incomplete data.
2. At least 80% of those samples (`ceiling(DurationMins × 0.8)`) must have `MaxCUPercentage < Threshold`.
3. The most recent 20% of the window (`ceiling(DurationMins × 0.2)`) must *also* be entirely idle — this recency check prevents alerting on a capacity that was idle earlier in the window but has since become active again.

**Sessions:** consecutive idle windows are merged into one "session" as long as the gap between them is ≤ `sessionGapMinutes`; a gap larger than that (or a SKU change) starts a new session. Each session gets a stable `SessionKey` of `CapacityId||Idle Capacity||CapacitySku||<session start>`.

**Suppression & dedup:** capacities with an active `SuppressAlertsUntil` (from the Suppress Finding setting) are excluded entirely. New sessions are also compared against the most recently persisted finding for that capacity/SKU (`FabricFindingsAlerts`) so that `Active` findings are only extended forward, and `Expired`/`Suppressed`/`Approved` findings only regenerate a *new* finding once fresh idle activity starts after that finding's window end — this is what prevents duplicate findings for the same idle period.

### From Session to Finding

- **Live re-check (`DeduplicateIdleLoad()`):** before a deduped session is allowed through, the function re-pulls the capacity's *current* utilization (last 5 minutes) and recomputes `EffectiveUtilization`. Only sessions where `EffectiveUtilization < Threshold` still holds (`BurndownSafe == true`) proceed — this stops a finding from firing if the capacity became busy again between detection and persistence.
- **SKU suggestion (`ProcessNewIdleLoad()`):** looks up the capacity's current SKU in the `SkuDowngrade` table (`CurrentSku → NextSku`) to compute `FinalSKU`, and writes the finding text as `"Consider downsizing the Capacity to <FinalSKU>"`.
- **Floor guard:** if both `CurrentSKU` and `FinalSKU` resolve to `F2` (the smallest SKU), the finding is dropped — there's nowhere lower to scale to.
- **Auto-expiration:** unlike Throttling Risk, Idle Capacity findings **auto-expire after 24 hours** (`expirationHours = 24`, measured from the finding's `EventTime`) if not otherwise acted on.
- **Approval/suppression short-circuit:** as with Throttling Risk, a new finding isn't (re)written if the capacity is currently suppressed, and prior terminal findings (`Expired`/`Suppressed`/`Approved`) only regenerate once a new idle window starts after their persisted window end.

## Feature Engineering — `EngineerUpsizeFeatures()`

Before `ComputeUpsizeDecision()` can evaluate a window, `EngineerUpsizeFeatures()` (fired by an update policy off `CapacityUtilization`) computes the inputs it consumes:

- `D = InteractiveDelayThresholdPercentage / 100`, `R = InteractiveRejectionThresholdPercentage / 100`, `B = BackgroundRejectionThresholdPercentage / 100` — normalized 0–1 versions of the raw Fabric-reported threshold percentages.
- `O_total`, `O_add`, `O_burn` — total/added/burned-down overage CU-ms, each normalized by that window's CU budget (`WindowBudgetMs = BaseCapacityUnits × 1000 × 30`).
- `trend_D = D - previous window's D` for the same capacity (0 if this is the capacity's first window) — captures whether delay pressure is rising or falling.
- **Weighted pressure score:**

  ```
  Pressure = 0.35·D + 0.25·R + 0.10·B + 0.15·O_total + 0.10·O_add − 0.05·O_burn
  EffectivePressure = Pressure + 0.25·trend_D
  ```

  Delay (`D`) and rejection (`R`) dominate the score (60% combined weight); a rising delay trend (`trend_D > 0`) pushes `EffectivePressure` up further, while active burndown (`O_burn`) pulls it slightly back down.

`EffectivePressure` is exactly what `ComputeUpsizeDecision()`'s "Math trigger" compares against the sensitivity-based `PrimaryTrigger` below.

## Throttling Risk Detection — Upscale (`ComputeUpsizeDecision()`)

Reads from `UpsizeFeatures` (pre-computed pressure/rejection/delay metrics per capacity/window) and evaluates whether a capacity should be flagged for an upscale (`Decision = "UPSCALE"`).

**Sensitivity thresholds** — read from `CapacitySetting.ConfigurationJSON` for `SettingTypeId = 6` ("Throttling Risk"), keyed by the `sensitivity` field (`low` / `medium` / `high`, **default `high`** if unset or unrecognized):

| Sensitivity | Primary Trigger (`EffectivePressure`) | Delay Guardrail (`D`) | Rejection Guardrail (`R`) |
|---|---|---|---|
| Low | ≥ 0.60 | ≥ 0.90 | ≥ 0.87 |
| Medium | ≥ 0.57 | ≥ 0.85 | ≥ 0.83 |
| **High (default)** | **≥ 0.55** | **≥ 0.80** | **≥ 0.78** |

**Trigger condition:** a window is flagged `UPSCALE` if **any** of the three hold:

- **Math trigger** — `EffectivePressure >= PrimaryTrigger`
- **Delay guardrail** — `D >= DelayGuardrailThreshold`
- **Rejection guardrail** — `R >= RejectionGuardrailThreshold`

The specific condition(s) that fired are recorded in a `TriggeredBy` string (e.g. `"Math (P>=0.55)"`) for traceability on the resulting finding.

**Post-scale-up stale-SKU guard:** after an `UPSCALE` finding is `Approved`, `CapacityUtilization` can keep reporting the pre-scale SKU for a few more events (2–4) before it catches up. To avoid a spurious duplicate finding, any incoming row is dropped if there's an `Approved` Throttling Risk finding for that capacity within the last **3 minutes** and the row's SKU still matches the pre-approval SKU.

**Sessions:** consecutive triggered windows for the same capacity/SKU are merged into a session if the gap is ≤ 5 minutes (`sessionGapMins`); a larger gap or a SKU change starts a new session. Sessions are further chained to a prior session (extending `TrueSessionStart` backward) if the gap from the prior session's end is ≤ 5 minutes and that prior session wasn't `Suppressed` or `Approved`.

**Suppression:** as with Idle Capacity, capacities with an active `SuppressAlertsUntil` (`SettingTypeId = 6`) are excluded before any session logic runs.

> **Note:** `debtGuardrailThreshold = 0.8` is declared at the top of `ComputeUpsizeDecision()`, but a search across the full schema confirms it is never referenced again anywhere — this is dead code, not logic implemented elsewhere. Worth removing or wiring up, but no longer a documentation gap.

### From Decision to Finding

- **Dedup (`DeduplicateUpsizeFindings()`):** collapses `UpsizeFindings_RT` to one row per `SessionId` (latest `DetectedAt` wins) before anything is persisted.
- **SKU suggestion (`ProcessNewApproachingThrottling()`):** looks up the capacity's current SKU in the `SkuProgression` table (`CurrentSku → NextSku`) to compute `FinalSKU`, and writes the finding text as `"Increase the Capacity to <FinalSKU>"`.
- **Ceiling guard:** if both `CurrentSKU` and `FinalSKU` resolve to `F8192` (the top SKU), the finding is dropped — there's nowhere higher to scale to.
- **Supersession:** if a capacity already has an `Active` Throttling Risk finding and a *different* session now qualifies, the old finding is marked `Expired` and a new one is written for the new session.
- **Approval/suppression short-circuit:** a new finding is not (re)written if the capacity is currently suppressed, or if the same session was already `Approved` after the triggering event.
- Unlike Idle Capacity, there is **no time-based auto-expiration** for Throttling Risk findings — a finding stays `Active` until superseded, approved, or suppressed.

## Notifications

An integrated email framework sends alerts for:

- Throttling Risk findings
- Idle Capacity findings
- Automatic scale-up / scale-down actions
- Capacity pause / resume actions
