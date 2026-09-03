# Prerequisites

> **Important:** All prerequisites below must be met before starting the setup steps. Missing any item will cause failures during Eventstream creation or notebook execution.

Before starting, also ensure the **Microsoft Fabric Capacity Metrics App** is installed and its semantic model is refreshing. Note down the **Workspace Name** and **Semantic Model Name** — these are required later when configuring capacity monitoring.

---

## Fabric Roles

| Role / Permission | Where | Why It Is Needed |
|---|---|---|
| Contributor (minimum) | Fabric Workspace (Admin Agent WS) | Required to create the Fabric Admin Agent workload item and deploy all Fabric artifacts |
| Capacity Administrator | Microsoft Fabric (per capacity) | Required to read Capacity Overview Events via Eventstream and to add capacities in the workload Configuration tab; must be assigned for every capacity being monitored |
| Global Administrator, Privileged Role Administrator, Application Administrator, or Cloud Application Administrator | Microsoft Entra ID (tenant level) | Required to grant admin consent for the Frontend and Backend Service Principal during initial workload setup |
| Fabric Administrator or Global Administrator (with Tenant.Read.All or Tenant.ReadWrite.All) | Microsoft Fabric / Power BI | Required to authorize the PBI Service connection in Manage Connections and Gateways |

---

## Azure Resources

| Resource | Configuration / Required Permission | Purpose |
|---|---|---|
| Azure Function App | Created during custom deployment | Hosts APIs for capacity operations, notification services, and automation workflows |
| App Service Plan | Created during custom deployment | Provides compute resources for the Azure Function App |
| Azure Storage Account | Created during custom deployment | Provides storage required by the Azure Function App runtime and application operations |
| Log Analytics Workspace | Created during custom deployment | Stores operational logs, diagnostics, and monitoring telemetry for the function app |
| Application Insights | Created during custom deployment | Provides application monitoring, tracing, performance metrics, and diagnostics for the function app |
| Azure Key Vault | User must have **Key Vault Administrator** role during setup | Stores HVE (High Volume Email) account credentials |
| Application Insights Smart Detection | Automatically created by Azure Monitor | Detects application failures, performance anomalies, and operational issues |
| Action Group | Automatically created by Azure Monitor | Used by Smart Detection alert rules to generate and route alert notifications |
| Azure Automation Account | Created during Azure Resource Deployment | Hosts runbooks, schedules, and managed identities for scheduled capacity operations |
| Azure Runbooks | Created during Azure Resource Deployment | Execute scheduled operational actions such as capacity pause, capacity resume, capacity F-SKU scaling and other automated Real Time and Auto Scale actions |

### Azure Deployment Outputs

**Automated Deployment**
- Azure Key Vault
- Azure Automation Account
- Azure Runbooks (Count — 2)

**Custom ARM Template Deployment**
- Azure Storage Account
- Log Analytics Workspace
- Application Insights
- App Service Plan
- Azure Function App

**Automatically Created by Azure Monitor**
- Application Insights Smart Detection
- Smart Detector Alert Rule (Failure Anomalies)
- Action Group

### Minimum Azure Permissions Required

The user or deployment Service Principal performing the setup must have:
- **Contributor** (or higher) on the target Azure Resource Group.
- **Key Vault Administrator** (or equivalent permissions to create and manage secrets) on the Azure Key Vault.

> **Note:** No Azure RBAC permissions on the Microsoft Fabric Capacity Azure resource are required solely for Capacity Overview Event ingestion. Fabric capacity permissions are documented below.

---

## Fabric Capacity Events — Required Permissions

The workload uses **Capacity Overview Events** as the source for its Eventstream. These events provide live Capacity Unit (CU) telemetry that is ingested into the KQL Database and processed by the monitoring notebooks.

To configure and operate this functionality, the onboarding user or Service Principal must have the following permissions:

| Scope | Required Permission |
|---|---|
| Microsoft Fabric Capacity | **Contributor or higher** on the monitored capacity |
| Workspace hosting the Eventstream, KQL Database, and notebooks | **Contributor or higher** |

Without these permissions:
- The **Capacity Overview Events** source may not be available for configuration in the Eventstream.
- No live CU telemetry will be streamed into the KQL Database.
- Monitoring and detection notebooks will have no capacity telemetry data to process.

> **Note:** Microsoft documentation specifies that users must have **Contributor or higher permissions on the selected capacity** to stream Capacity Overview Events.

### How to Assign Capacity Permissions
1. Open **Microsoft Fabric Admin Portal**.
2. Navigate to **Capacities**.
3. Select the target capacity.
4. Grant the onboarding user or Service Principal **Contributor** (or higher) permissions on the capacity.

### How to Assign Workspace Permissions
1. Open the target Fabric workspace.
2. Select **Manage Access**.
3. Add the onboarding user or Service Principal.
4. Grant **Contributor** (or higher) permissions.

---

**Next:** [Tenant Settings →](./01-tenant-settings.md)
