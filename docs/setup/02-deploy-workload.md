# 02 — Deploy the Workload

*Previous: [Tenant Settings](./01-tenant-settings.md)*

---

## Step 3: Create the Fabric Admin Agent Workload Item

**1.** In the Fabric workspace, click the **New Item** button.

![NewItem](../assets/images/setup/newitem.png)

**2.** Search for **Fabric Admin Agent** and click on it.

![Search](../assets/images/setup/searchFaa.png)

**3.** Enter a name for the artifact and confirm creation.

![Create](../assets/images/setup/createitemdialoguebox.png)

The item is created in its initial state.

![Create](../assets/images/setup/initialstate.png)

---

## Step 4: Approve Frontend Service Principal Permissions

Upon creation, a popup appears requesting permissions for the **Frontend Service Principal (SPN)**. This is required for the workload UI to access Fabric APIs on behalf of the user.

**1.** Review the requested permissions in the popup.

![Create](../assets/images/setup/frontendadminapproval.png)

**2.** Click **Request Approval** to submit the consent request.

> **Note:** Approval must be granted by a user with one of the admin roles listed in [Prerequisites → Fabric Roles](./prerequisites.md#fabric-roles) (Global Administrator, Privileged Role Administrator, Application Administrator, or Cloud Application Administrator).

---

## Step 5: Authorize the Backend Application

The Backend app registration requires admin consent and **must be performed by** a **Global Administrator**, **Privileged Role Administrator**, **Application Administrator**, or **Cloud Application Administrator**.

**1.** The admin navigates to the Backend app authorization screen within the workload setup.

![Create](../assets/images/setup/backendappconsent.png)

**2.** The admin reviews and grants the requested API permissions.

![Create](../assets/images/setup/initialstatebackendsignin.png)

> **Important:** Both the Frontend SPN approval (Step 4) and Backend app authorization (Step 5) must be completed before proceeding to Step 6.

---

## Step 6: Deploy Fabric Resources

Once both SPN approvals are complete:

**1.** Click the **Deploy Fabric Resources** button in the workload item.

![Create](../assets/images/setup/fabric_deploy.png)

**2.** Wait for the deployment to complete. This process takes approximately **20–25 minutes**.

![Create](../assets/images/setup/fabric_deployment_progress.png)

**3.** Confirm all Fabric artifacts have been successfully deployed. See [Fabric Artifacts reference](../reference/kql-tables.md) for the full list of items this deploys (Lakehouse, notebooks, pipelines, semantic model, report, KQL database, connections, etc.).

![Create](../assets/images/setup/fabric_deployment_complete.png)

---

**Next:** [Deploy Azure Resources →](./03-deploy-azure.md)
