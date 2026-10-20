# SMTP & Email Settings Reference

The Fabric Admin Agent uses a dedicated High Volume Email (HVE) account to dispatch notification emails for alerts, findings, and autoscale actions.

## HVE SMTP Configuration

Configure the Fabric Admin Agent notification service with the following standard Microsoft 365 HVE parameters:

| Setting | Value |
|---|---|
| **SMTP Server** | `smtp.hve.mx.microsoft` |
| **Port** | `587` |
| **Encryption** | `TLS` |
| **Authentication** | HVE Account Credentials |
| **Sender Address** | The configured HVE Account Email Address |
| **Recipient Scope** | Internal Tenant Recipients |

## Azure Key Vault Integration

The SMTP credentials must be securely stored in the Azure Key Vault deployed alongside the Azure Function App. The application code explicitly looks for exact secret names. 

Ensure the following secrets are created using this exact naming convention:

| Secret Name | Value |
|---|---|
| `FabricAdminAgentEmail` | The primary email address of the HVE account |
| `FabricAdminAgentEmailPassword` | The strong password for the HVE account |

*The Azure Function App and the Automation Account managed identities must be assigned the **Key Vault Secrets User** role to retrieve and resolve these variables from Azure Key Vault at runtime.*