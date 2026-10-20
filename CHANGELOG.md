# Changelog

All notable changes to the Fabric Admin Agent are documented here.

## [2.0.0]

Version 2 moves detection from scheduled batch jobs to real time. Findings now appear within seconds to a few minutes of a condition starting, instead of after the next scheduled notebook run.

### Added

- **Real-time findings.** KQL update policies evaluate Capacity Overview Events as they arrive, about every 30 seconds, and raise findings immediately. See [Detection logic](docs/architecture/05-detection-logic.md).
- **Throttling Risk and Idle Capacity findings**, with three sensitivity levels and thresholds you set per capacity. See [Configure findings](docs/operations/configure-findings.md).
- **Auto-Scale with guardrails.** Scales up on Throttling Risk and down on Idle Capacity, one SKU step at a time, within the minimum and maximum SKU and the hours you allow, with a cooldown to prevent oscillation. See [Autoscaling & scheduling](docs/operations/autoscaling-and-scheduling.md).
- **Monitoring windows** to choose the days and hours each capacity is monitored.
- **AI Insights** from Capacity Metrics App history: Capacity Throttling, AI Recommendation for Capacity, F-SKU Schedule Recommendation, and AI Powered Capacity Allocation. Optional AI-written summaries use your own Azure OpenAI resource.
- **Azure Function App**, deployed from an [ARM template](deploy/ARM-FunctionApp-FAA.json), for capacity operations and notifications.

### Changed

- **Detection engine.** The scheduled PySpark detection notebooks from version 1 (Sustained Load, Spike Detection, CU Trending Upwards, and Short-Term CU Prediction) are replaced by real-time KQL detection.
- **Onboarding.** An Eventstream is created automatically when you onboard a capacity. Manual Eventstream setup is no longer needed.
- **Storage.** Findings, settings, schedules, and audit logs move from the `FabricAdminAgentDB` SQL database to the `FabricAdminAgentLogs` KQL database.
- **Automation identity.** The Function App and Automation Account use system-assigned managed identities. Key Vault no longer stores a service principal client ID and secret. It holds only the email credentials and the optional Azure OpenAI key.
- **Agent actions.** Auto-Scale replaces the version 1 AgentAction notebook, which applied recommended SKU upgrades on its 30-minute schedule.
- **Scheduling.** F-SKU Schedule extends the version 1 capacity on/off schedules with scale-up and scale-down actions, one-time, daily, or weekly recurrence, and a visual timeline.

## [1.0.0]

Initial release. Capacity telemetry streamed in continuously, but detection ran as scheduled batch jobs.

- An Eventstream, created manually for each capacity, streamed Capacity Overview Events into an Eventhouse KQL database.
- PySpark detection notebooks, scheduled every 30 minutes by default, produced four finding types: Sustained Load, Spike Detection, CU Trending Upwards, and Short-Term CU Prediction, which used a forecasting model retrained weekly.
- An aggregator notebook combined the results into a SQL database (`FabricAdminAgentDB`) that powered the workload UI.
- An optional AgentAction notebook applied recommended SKU upgrades and sent email notifications, on the same 30-minute schedule.
- Azure Automation runbooks turned capacities on and off on a schedule.
- A daily pipeline loaded item-level history from the Capacity Metrics App into a Lakehouse for the embedded Power BI reports.
