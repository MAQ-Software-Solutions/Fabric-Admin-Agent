# Troubleshooting

## Scenario 1: Data is not being loaded into the report in Capacity Monitoring Agent tab
**Debugging Steps:**
* Verify whether the `FabricAdminAgent_LoadCapacityMetricsData` pipeline has run successfully. If the pipeline fails, check whether the connections configured in the `FabricAdminAgent_FetchWorkspacesPipeline` and `FabricAdminAgent_FetchCapacitiesPipeline` pipelines have the required permissions and access.
* Verify whether the semantic model has been refreshed successfully. If the refresh fails, check cloud connections, and data source credentials.
* If data is not being populated in the Real-Time Utilization chart, verify whether the associated Eventstream destination is active.
* **Connection Verification:** Check the following connections and their access:
  * `fabricadminagent-kql-connection_<suffix>`
  * `fabricadminagent-lakehouse-sql-endpoint_<suffix>`
  * `fabricadminagent-pbi-semantic-refresh_<suffix>`
  * `fabricadminagent-pbi-service-api-admin_<suffix>`

## Scenario 2: Capacity Scorecard shows a "Paused" state on the Home page and no real-time findings are generated when Agent Actions are disabled
**Debugging Steps:**
* Verify the associated Eventstream and ensure that its destination is active.
* Check whether the capacity utilization is within an appropriate range and whether there is sufficient workload activity to trigger a scale-up or scale-down recommendation.

## Scenario 3: Autoscale action is not executed even though Auto Scale schedule is active
**Debugging Steps:**
* Verify that the managed identity of the Function app has the required read and write access roles and permissions on that Fabric capacity in the Azure portal.
* Verify the managed identity of the Function app has at least the Contributor role on the Fabric workspace where the Fabric Admin Agent item is setup.
* Verify the managed identity has the Key Vault Secrets User role on the deployed Key vault.

## Scenario 4: Eventstream for a capacity is not functioning
**Debugging Steps:**
* Navigate to **Configuration → Capacities** and offboard the respective capacity from the workload.
* Once the capacity has been offboarded, onboard it again.
* Re-onboarding the capacity will provision a new functional Eventstream with the same name and remove the previously deployed Eventstream.

## Scenario 5: Real Time Monitoring Schedule / Auto-Scale Schedule / F-SKU Schedule failure
**Debugging Steps:**
* Verify that the Automation Account's managed identity has the required permissions to execute the scheduled jobs:
  * The managed identity must have Read and Write access to the relevant capacity.
  * The managed identity must have at least the Contributor role on the relevant workspace.
  * The managed identity must have the Key Vault Secrets User role on the deployed Key vault.
  * The managed identity must be added to the security group that is configured to Allow service principals to use Fabric APIs.

## Scenario 6: API failures (e.g., Internal Server Error or Kusto Throttling Errors)
**Debugging Steps:**
* Verify that the underlying Fabric capacity for the workload item is currently active and running.
* Check if the capacity is being throttled due to high utilization, which can prevent the workload tabs from loading or cause operations to fail. 
* If the capacity is throttled or unresponsive, attempt to scale up the capacity (increase the F-SKU) or restart the capacity to restore normal operations. 

## Scenario 7: Timeout scenarios during operations or tab loading 
**Debugging Steps:**
* Similar to API failures, timeouts often indicate that the underlying compute resources are exhausted. Verify that the Fabric capacity is active and not in a throttled state. 
* Check if the capacity is overloaded. If it is, attempt scaling up the capacity or restarting it to clear the bottleneck and allow operations to complete within the expected timeframes. 

## Scenario 8: Workload deployment or operations fail due to missing permissions 
**Debugging Steps:**
* Verify Prerequisites: [Prerequisites](./../setup/prerequisites.md)
* Verify Permissions: [Permissions](./../setup/04-permissions.md)

## Scenario 9: Semantic model (FabricAdminAgent_CapacityMonitoringAgentDataset_(identifier)) refresh failures 
**Debugging Steps:**
* Verify connection configurations: [Connections](./../setup/05-connections.md)