# Permissions Matrix

The Fabric Admin Agent requires specific permissions across Azure, Microsoft Fabric, and Microsoft Entra ID for the deployment user(s), the Azure Function App, and the Azure Automation Account.

## Setup User Permissions (One-Time)

The user(s) performing the initial workload setup must have:

| Scope | Role / Permission | Purpose |
|---|---|---|
| Microsoft Entra ID | Global Administrator, Privileged Role Admin, Application Admin, or Cloud Application Admin | Grant admin consent for Frontend and Backend Service Principals |
| Microsoft Fabric (Tenant) | Fabric Administrator or Global Administrator | Authorize the PBI Service connection in Manage Connections and Gateways |
| Microsoft Fabric (Workspace) | **Contributor** (minimum) | Create the workload item and deploy Fabric artifacts |
| Microsoft Fabric (Capacity) | **Capacity Administrator** | Read Capacity Overview Events via Eventstream and add capacities in the Configuration tab |
| Azure Resource Group | **Contributor** | Deploy Azure infrastructure (Function App, Vault, etc.) |
| Azure Key Vault | **Key Vault Administrator** | Store HVE credentials during deployment |

## Azure Function App Managed Identity

The System Assigned Managed Identity of the Azure Function App requires:

| Scope | Role / Permission | Purpose |
|---|---|---|
| Azure Key Vault | **Key Vault Secrets User** | Read email credentials (HVE) and Azure OpenAI keys at runtime |
| Microsoft Fabric (Workspace) | **Contributor** | Interact with Fabric APIs and manage workspace items |
| Microsoft Fabric (Capacity) | `Microsoft.Fabric/capacities/read`<br>`Microsoft.Fabric/capacities/write` | Scale up, scale down, pause, and resume capacities |

## Azure Automation Account Managed Identity

The System Assigned Managed Identity of the Azure Automation Account requires:

| Scope | Role / Permission | Purpose |
|---|---|---|
| Azure Key Vault | **Key Vault Secrets User** | Read required secrets for scheduled job execution |
| Microsoft Fabric (Workspace) | **Contributor** | Execute scheduled operations in the workspace |
| Microsoft Fabric (Capacity) | `Microsoft.Fabric/capacities/read`<br>`Microsoft.Fabric/capacities/write` | Execute scheduled capacity actions |
| Microsoft Entra ID (Security Group) | Member of security group for Fabric APIs | Allows the service principal to use Power BI APIs (configured in Fabric Tenant Settings) |