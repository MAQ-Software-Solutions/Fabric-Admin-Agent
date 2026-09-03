# Fabric & Azure Artifacts

All Fabric artifacts below are deployed into the same Fabric workspace as the Fabric Admin Agent workload item, via the workload's **Deploy Fabric Resources** action. See [04-azure-components.md](04-azure-components.md) for the Azure-side resources.

| Artifact Name | Type | Role |
|---|---|---|
| FabricAdminAgent | Lakehouse | Delta tables: usage metrics |
| FabricAdminAgent_AggregatedMetrics | Notebook | Creates day/week/month/quarter/year level CU metrics at workspace and item level |
| FabricAdminAgent_Capacities | Notebook | Gathers capacities in the tenant; executed in FetchCapacitiesPipeline |
| FabricAdminAgent_CapacityMonitoringAgentDataset | Semantic Model | Semantic model for CapacityMonitoringAgentReport |
| FabricAdminAgent_CapacityMonitoringAgentReport | Report | Embedded visual: capacity monitoring metrics |
| FabricAdminAgent_ConfigNotebook | Notebook | Returns the KQL URI; called by all other notebooks at startup |
| FabricAdminAgent_Dates | Notebook | Creates the Date table |
| FabricAdminAgent_FetchCapacitiesPipeline | Pipeline | Gathers data of capacities present in the tenant |
| FabricAdminAgent_FetchWorkspacesPipeline | Pipeline | Gathers workspace data within capacities in the tenant |
| FabricAdminAgent_InitializeTables | Notebook | One-time: creates all KQL tables in the KQL database |
| FabricAdminAgent_ItemOperationMetrics | Notebook | Gathers item operation level data from the Capacity Metrics App |
| FabricAdminAgent_Items | Notebook | Gathers item data in capacities |
| FabricAdminAgent_LoadCapacityMetricsData | Pipeline | Pulls item-level data from the Capacity Metrics App |
| FabricAdminAgent_Workspaces | Notebook | Gathers workspaces in capacities; executed in FetchWorkspacesPipeline |
| FabricAdminAgent_WorkloadAllocation | Notebook | Generates workspace reallocation recommendations by analyzing workspace distribution, capacity utilization patterns, and available headroom across onboarded capacities; runs as part of GenerateAIInsights |
| FabricAdminAgent_FSKURecommendation | Notebook | Analyzes historical utilization/operational patterns to generate F-SKU pause/resume schedule recommendations; runs independently, on demand or on a schedule |
| FabricAdminAgent_Throttling | Notebook | Processes historical Capacity Metrics App and Capacity Overview Event data to identify long-term throttling trends and generate capacity scaling recommendations; runs as part of GenerateAIInsights |
| FabricAdminAgent_UsageRecommendation | Notebook | Analyzes historical consumption/utilization trends to generate capacity right-sizing (scale-up/scale-down) recommendations; runs as part of GenerateAIInsights |
| FabricAdminAgent_FabricFindings | Notebook | Aggregates the Batch Insights notebook outputs, enriches findings with capacity metadata, and prepares final recommendation records for the KQL database; runs as part of GenerateAIInsights |
| FabricAdminAgent_GenerateAIInsights | Pipeline | Orchestrates the end-to-end Batch Insights workflow (scaling, F-SKU scheduling, workspace reallocation) and publishes finalized insights to `FabricAdminAgentLogs` |
| FabricAdminAgent_Variables | Variable Library | Centralized configuration store for user-provided parameters, runtime settings, environment-specific values, and reusable configuration variables |
| FabricAdminAgentEnvironment | Environment | Execution environment for notebooks, including the custom Fabric Admin Agent `.whl` package and external Python libraries used for batch insights, data processing, and recommendation engines |
| FabricAdminAgentLogs | Eventhouse | Eventhouse for storing live capacity events |
| FabricAdminAgentLogs | KQL Database | Stores `FabricCapacityEvents`, `CapacityUtilization`, `ConfigTable`, and materialized views |
| fabricadminagent-pbi-service-api-admin | Connection | Web V2 connection used to authenticate and invoke Power BI Admin APIs for capacity, workspace, and tenant-level metadata |
| fabricadminagent-pbi-semantic-refresh | Connection | Used by the Fabric pipeline to execute semantic model refresh operations, keeping the Monitoring Agent report current |
| fabricadminagent-kql-connection | Connection | Connects the semantic model to the `FabricAdminAgentLogs` KQL database for real-time reporting/visualization |
| fabricadminagent-lakehouse-sql-endpoint | Connection | Connects the semantic model to the Lakehouse SQL Endpoint, providing access to historical and intermediate analytics data |

## Notebook Execution Order (Batch Insights)

`GenerateAIInsights` orchestrates the following notebooks, all of which call `FabricAdminAgent_ConfigNotebook` at startup to resolve the KQL URI:

1. `FabricAdminAgent_Throttling`
2. `FabricAdminAgent_UsageRecommendation`
3. `FabricAdminAgent_WorkloadAllocation`
4. `FabricAdminAgent_FabricFindings` (aggregation step, runs last)

`FabricAdminAgent_FSKURecommendation` is scheduled and run independently of this pipeline.
