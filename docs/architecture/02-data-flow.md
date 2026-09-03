# Data Flow

This page traces how data and requests move through the Fabric Admin Agent, from the raw Capacity Overview Events to a finding on screen, and from a user click in the workload UI to a query against the KQL database. See [01-overview.md](01-overview.md) for the component layout these flows run across.

## Real-Time Path: Capacity Overview Events → Eventstream → Eventhouse → KQL Detection

1. Each monitored Fabric Capacity emits **Capacity Overview Events** — live Capacity Unit (CU) telemetry.
2. When a capacity is onboarded, an **Eventstream** named `Eventstream-<capacity-name>-Source_<identifier>` is created automatically (one per onboarded capacity) and streams these events roughly every 30 seconds.
3. The Eventstream writes into the `FabricCapacityEvents` table in the **Eventhouse / KQL database** (`FabricAdminAgentLogs`).
4. **Real-time KQL detection logic** runs continuously against the incoming events as a chain of update policies (`CalculateCapacityUtilization` → `EngineerUpsizeFeatures`/`IdleLoadDetect` → decision → dedup → alert), evaluating each capacity for **Throttling Risk** (upscale) and **Idle Capacity** (downscale) conditions. See [05-detection-logic.md](05-detection-logic.md) for the full function-by-function pipeline and trigger math.
5. Findings are written back to `FabricAdminAgentLogs` and surfaced in the workload's **Review Active Findings** tab.

## Historical / Batch Insights Path

1. The `FabricAdminAgent_LoadCapacityMetricsData` pipeline pulls item-level usage data from the **Capacity Metrics App** on a schedule (daily, by default) into the **Staging Lakehouse** (Delta tables).
2. The `FabricAdminAgent_GenerateAIInsights` pipeline orchestrates the recommendation notebooks:
   - `FabricAdminAgent_Throttling` — long-term throttling trends → capacity scaling recommendations
   - `FabricAdminAgent_UsageRecommendation` — utilization trends → right-sizing recommendations
   - `FabricAdminAgent_WorkloadAllocation` — workspace distribution → reallocation recommendations
   - `FabricAdminAgent_FSKURecommendation` — F-SKU pause/resume schedule recommendations (runs on its own weekly schedule, independent of the pipeline above)
   - `FabricAdminAgent_FabricFindings` — aggregates the notebook outputs, enriches with capacity metadata, and prepares final recommendation records
3. Final recommendations are written to the `FabricAdminAgentLogs` KQL database and surfaced in the **Monitoring Agent** tab and the **AI Recommendation** / **F-SKU Schedule Recommendation** sections of the workload UI.

## Request & Auth Flow

This is the numbered flow shown in the architecture diagram, covering how a user request reaches the KQL database:

1. **Request Token for Backend App** — the React frontend, running inside the Fabric front end's Extension iFrame, requests a token for the Backend app registration from **Customer Entra ID**.
2. **API Call (Bearer: Token 1)** — the frontend calls the .NET Core backend, passing that token as a bearer token.
3. **On-Behalf-Of (OBO) Token Exchange** — the backend exchanges Token 1 with **Customer Entra ID** for a new, Kusto-scoped token, using the Backend Service Principal (scope: `user_impersonation`).
4. **Query KQL DB for Workload Functionality (Bearer: Kusto OBO Token)** — the backend calls the customer's KQL database directly, using the OBO-derived token as the bearer credential.

The cluster URI the backend targets in step 4 is looked up per tenant/item from the ISV Config Store (Azure Table Storage in the ISV tenant), which is authenticated separately using a dedicated service-principal client secret — this is a distinct credential from the user-delegated OBO token used against the KQL cluster itself.

## Automation & Scheduling Flow

1. Pause, resume, real-time monitoring, and autoscale schedules are configured in the workload UI and stored in the `FabricAdminAgentLogs` KQL database.
2. The **Function App** and **Automation Account runbooks** read these schedule definitions and, at the configured times, call the Fabric APIs (via their own system-assigned Managed Identities) to pause, resume, or scale the target Fabric Capacity.
3. Scaling and schedule outcomes (and any resulting notifications) are logged back to `FabricAdminAgentLogs`.

> **Note:** When a capacity is turned off, its Eventstream also turns off automatically and must be manually re-enabled once the capacity is back on — see the Disclaimer section of the Setup Guide.
