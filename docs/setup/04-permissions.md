# 04 — Permissions (Managed Identity Role Grants)

*Previous: [Deploy Azure Resources](./03-deploy-azure.md)*

The Function App and Automation Account each use a **System Assigned Managed Identity**. This section grants those identities the Fabric, Azure, and Power BI permissions they need to operate.

---

## Step 10: Copy the Function App and Automation Account Managed Identity

**1.** In the Azure Portal, navigate to the deployed **Function App**.

**2.** Go to **Settings → Identity**.

**3.** Under the **System assigned** tab, copy the **Object (principal) ID**.

**4.** Perform the same steps for the deployed **Automation Account**.

![Create](../assets/images/setup/function_app_identity.png)

---

## Step 11: Grant Workspace Contributor Role to the Function App and Automation Account

**1.** Navigate to the Fabric workspace in the Fabric Portal.

**2.** Go to **Workspace settings → Manage access**.

**3.** Add the Function App's managed identity and Automation Account's managed identity (using the Object IDs from Step 10) with the **Contributor** role.

![Create](../assets/images/setup/function_app_fabric_access.png)

---

## Step 12: Grant Capacity Roles to the Function App and Automation Account Identities

For each capacity to be onboarded, grant the following Fabric permissions to the Function App's and Automation Account's managed identities:
- **Microsoft.Fabric/capacities/read**
- **Microsoft.Fabric/capacities/write**

**1.** Go to **Microsoft Fabric Admin Portal → Capacities**.

**2.** Select the target capacity.

**3.** Add the Function App's managed identity and the Automation Account's managed identity with the required roles.

![Create](../assets/images/setup/function_app_capacity_access.png)

---

## Step 13: Grant Key Vault Secrets User Role to the Function App and Automation Account

**1.** In the Azure Portal, navigate to the deployed **Key Vault**.

**2.** Go to **Access control (IAM) → Add role assignment**.

**3.** Assign the **Key Vault Secrets User** role to the Function App's managed identity and the Automation Account's managed identity.

![Create](../assets/images/setup/function_app_vault_access.png)

---

## Step 14: Add the Automation Account's Managed Identity to the "Allow service principals to use Fabric APIs" Security Group

**1.** Navigate to **Microsoft Fabric Admin Portal**

**2.** Search for the **Allow service principals to use Fabric APIs** setting.

**3.** Open the associated Security Group and add the Automation Account's Managed Identity as a member.

![Create](../assets/images/setup/function_app_vault_access.png)

---

**Next:** [Email & Notifications →](./05-email-notifications.md)
