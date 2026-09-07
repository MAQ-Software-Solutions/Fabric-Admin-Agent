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