# Azure Components

The Azure-side resources are deployed in two stages during one-time infrastructure setup: an automated deployment (Key Vault, Automation Account, runbooks) triggered from the workload item, followed by a custom ARM template deployment (Function App and its supporting resources). See [03-fabric-artifacts.md](03-fabric-artifacts.md) for the Fabric-side artifacts these components interact with.

## Resources

| Resource | Configuration / Deployment | Purpose |
|---|---|---|
| Azure Function App | Deployed via the custom ARM template (`ARM-FunctionApp-FAA.json`) | Hosts APIs for capacity operations, notification services, and automation workflows |
| App Service Plan | Created during custom deployment | Provides compute for the Function App |
| Azure Storage Account | Created during custom deployment | Storage required by the Function App runtime and application operations |
| Log Analytics Workspace | Created during custom deployment | Stores operational logs, diagnostics, and monitoring telemetry for the Function App |
| Application Insights | Created during custom deployment | Application monitoring, tracing, performance metrics, and diagnostics for the Function App |
| Azure Key Vault | Created via the automated deployment; requires **Key Vault Administrator** during setup | Stores HVE (High Volume Email) account credentials and other runtime secrets |
| Application Insights Smart Detection | Auto-created by Azure Monitor | Detects application failures, performance anomalies, and operational issues |
| Action Group | Auto-created by Azure Monitor | Routes Smart Detection alert rule notifications |
| Azure Automation Account | Created via the automated deployment | Hosts runbooks, schedules, and a managed identity for scheduled capacity operations |
| Azure Runbooks (×2) | Created via the automated deployment | Execute scheduled operational actions: capacity pause, resume, F-SKU scaling, and other Real-Time/Auto-Scale actions |

## Deployment Sequence

1. **Automated deployment** (triggered from the workload item's **Deploy Azure Resources** button): Key Vault, Automation Account, 2 runbooks.
2. **Custom ARM template deployment** (manual, in the Azure Portal, using `ARM-FunctionApp-FAA.json`): Function App, App Service Plan, Storage Account, Log Analytics Workspace, Application Insights. Requires the Function App name, the Key Vault name from step 1, and the KQL DB URI/name from the Fabric deployment as inputs.
3. Application Insights Smart Detection, its alert rule, and an Action Group are then created automatically by Azure Monitor.

## Identity & Permissions

Both the Function App and the Automation Account use a **system-assigned Managed Identity** — no stored credentials. Each requires:

| Role | Scope | Purpose |
|---|---|---|
| Key Vault Secrets User | Deployed Key Vault | Read email credentials at runtime |
| Contributor | Fabric workspace | Interact with Fabric APIs |
| `Microsoft.Fabric/capacities/read`, `Microsoft.Fabric/capacities/write` | Each monitored Fabric capacity | Scale up, scale down, pause, and resume capacities |

The Automation Account's managed identity additionally must be added to the security group used for the tenant setting **Allow service principals to use Fabric APIs**.

Minimum permissions for the **user or deployment Service Principal** performing setup:

- **Contributor** (or higher) on the target Azure Resource Group
- **Key Vault Administrator** (or equivalent) on the Azure Key Vault

## Key Vault Secret Naming Convention

The following secrets must exist in the Key Vault, using these exact names:

- `FabricAdminAgentEmail`
- `FabricAdminAgentEmailPassword`
- `AZURE-OPENAI-API-KEY`

## Related: ISV-Side Storage

Separately from the customer-tenant Azure resources above, the ISV tenant (MAQ Software) hosts an **Azure Table Storage** account (the "ISV Config Store") that maps each customer tenant/item to its KQL DB connection string and database name. This is authenticated by the backend using a dedicated service-principal client secret, not a managed identity, since it lives outside the customer tenant. See the backend's `IsvTableStorageProvider` for implementation details.
