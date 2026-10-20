# Security

MAQ Software takes the security of the Fabric Admin Agent and our customers' data seriously. This page explains how to report a security vulnerability and summarizes how the agent protects your data.

---

## Reporting a vulnerability

**Please do not report security vulnerabilities through public GitHub issues, discussions, or pull requests.**

Instead, email **[CustomerSuccess@MAQSoftware.com](mailto:CustomerSuccess@MAQSoftware.com?subject=%5BSECURITY%5D%20Fabric%20Admin%20Agent)** with the subject line `[SECURITY] Fabric Admin Agent`.

### What to include

Include as much of the following as you can. It helps us understand and resolve the issue faster.

- The type of issue (for example, authentication bypass, cross-tenant data access, privilege escalation, secret exposure, or injection)
- The affected component (see [Scope](#scope))
- Step-by-step instructions to reproduce the issue, including any special configuration
- Proof-of-concept or exploit code, if available
- The impact: what an attacker could do, and what access they would need first
- How we can reach you for follow-up questions

Do not include real capacity data, Key Vault secrets, access tokens, or other customer data in your report. If you need to share sensitive evidence, tell us and we'll arrange a secure way to send it.

### What to expect

- We'll acknowledge your report and may ask follow-up questions.
- We'll investigate, keep you informed of our progress, and let you know when the issue is resolved.
- If the issue affects customers, we'll address it promptly and notify affected customers, including any action they need to take.
- We follow coordinated vulnerability disclosure. Please give us a reasonable amount of time to fix the issue before you disclose it publicly.

---

## Scope

The Fabric Admin Agent spans two tenants: MAQ Software's and yours. See the [Architecture overview](docs/architecture/01-overview.md) for how the pieces fit together.

| In scope | Examples |
|---|---|
| **MAQ-hosted application** | The workload frontend and backend API that render the agent inside Fabric, including sign-in and the On-Behalf-Of token exchange |
| **MAQ-side configuration store** | The per-customer record that maps your tenant to your KQL database |
| **Components we deploy into your tenant** | The Function App package and its [ARM template](deploy/ARM-FunctionApp-FAA.json), the Automation Account runbooks, and the notebooks, pipelines, KQL functions, and reports the workload creates |
| **This repository** | Documentation or setup steps that would lead customers to an insecure configuration |

### Out of scope

- **Vulnerabilities in Microsoft Fabric, Azure, or Microsoft Entra ID.** Report these to the [Microsoft Security Response Center (MSRC)](https://msrc.microsoft.com/create-report).
- **Issues specific to one environment's configuration**, such as permissions granted beyond those in the [Permissions matrix](docs/reference/permissions-matrix.md). Contact us through [SUPPORT.md](SUPPORT.md).
- Denial-of-service attacks, spam, social engineering, and physical attacks.

### Testing guidelines

- Test only in a Microsoft Entra tenant and Azure subscription that you own or are authorized to test.
- Never try to access another customer's tenant, data, or capacities. If you come across data that isn't yours, stop and report it to us.
- Do not run load or denial-of-service tests against the MAQ-hosted service, and do not pause, resume, or scale capacities that you don't own.

---

## Supported versions

| Version | Supported |
|---|---|
| 2.x | Yes |
| 1.x and earlier | No |

MAQ Software updates the hosted application directly, so you don't need to do anything to receive those fixes. If a fix requires changes to resources in your own tenant, such as the Function App, runbooks, or Fabric artifacts, we'll tell affected customers what to do.

---

## How the agent protects your data

- **Your data stays in your tenant.** Capacity telemetry, findings, schedules, and audit logs are stored in your Fabric workspace and Azure subscription. MAQ Software stores only a pointer to your KQL database (its cluster URI and database name) so it can route requests. See [Architecture overview](docs/architecture/01-overview.md).
- **Requests run as the signed-in user.** The backend uses the Microsoft Entra On-Behalf-Of flow to query your KQL database with a token for the signed-in user, so users see only what their own permissions allow. See [Data flow](docs/architecture/02-data-flow.md).
- **Automation uses managed identities.** The Function App and Automation Account authenticate to Fabric and Key Vault with system-assigned managed identities, limited to the roles in the [Permissions matrix](docs/reference/permissions-matrix.md).
- **Secrets stay in your Key Vault.** The High Volume Email credentials and, if you use AI Insights, your Azure OpenAI API key are stored in a Key Vault in your subscription and read at runtime. See [Azure components](docs/architecture/04-azure-components.md).
- **Data residency.** Customer data stays within your tenant's geography, both at rest and in transit, in line with Microsoft Fabric's data residency commitments.
- **Audit trail.** Every finding, recommendation, and action is logged in the `FabricAdminAgentLogs` KQL database in your tenant.
- **Reviewed and certified.** The workload has completed the security and privacy reviews required by Microsoft's Fabric workload publishing requirements, and MAQ Software is ISO 27001 compliant. See the [Workload attestation](docs/compliance/attestation.md) and our [Privacy Statement](https://maqsoftware.com/privacy-statement/).

---

## Securing your deployment

Security is shared between MAQ Software and you. We recommend that you:

- Grant only the permissions listed in the [Permissions matrix](docs/reference/permissions-matrix.md), and review setup-only role assignments once setup is complete.
- Keep the security group used for the **Allow service principals to use Fabric APIs** tenant setting limited to the identities that need it.
- Limit who can edit the agent's workspace. Anyone who can change its settings can schedule capacity pause, resume, and scale actions.
- Rotate the High Volume Email password and Azure OpenAI API key regularly, and update the matching secrets in Key Vault.
- Review the agent's audit log in `FabricAdminAgentLogs` regularly.
