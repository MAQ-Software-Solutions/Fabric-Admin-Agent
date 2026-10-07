# Frequently Asked Questions

---

## About the product

**Is the Fabric Admin Agent a Microsoft product?**
No. It's built and supported by MAQ Software using Microsoft's Fabric Workload Development Kit and is available through the Microsoft Marketplace. Because it's a partner workload, your Fabric administrator enables a tenant setting allowing "additional workloads" before it can be installed — see [Tenant settings](../setup/01-tenant-settings.md).

**What does it cost?**
The Fabric Admin Agent is a paid offer billed per capacity through Microsoft Marketplace metered billing. Current pricing is on the [Marketplace listing](https://marketplace.microsoft.com/en-us/product/maqsoftware.fabricadminagent), or email CustomerSuccess@MAQSoftware.com.

**Can I try it first?**
There's no self-service trial today. Contact CustomerSuccess@MAQSoftware.com to arrange a demo or an evaluation in your environment.

**Which regions is it available in?**
All Microsoft Fabric regions.

**How do I get help?**
Email CustomerSuccess@MAQSoftware.com. [SUPPORT.md](../../SUPPORT.md) explains what to include so we can help quickly.

---

## Data, privacy, and security

**Where does my data live?**
In your tenant. The real-time event store, historical data, reports, and the automation resources are all deployed to your Fabric workspace and your Azure subscription. See [Architecture overview](../architecture/01-overview.md).

**What does MAQ Software see?**
MAQ hosts the application that renders the agent's screens inside Fabric and reads your data on your behalf, using your signed-in identity for each request. It keeps only a pointer to where your data store lives so it can find it next time. It does not copy or retain your capacity data. See [Data flow](../architecture/02-data-flow.md).

**Why does it need admin approval and several roles?**
Two one-time approvals let the agent's screens call Fabric on your behalf. The automation resources need permission to scale, pause, and resume your capacities, and to read the email credentials you store. Every role and its purpose is listed in the [Permissions matrix](../reference/permissions-matrix.md).

**Does it work with Microsoft Entra Conditional Access?**
Yes, fully.

**Does it store passwords or keys?**
The automation resources use Azure managed identities — no saved credentials. The only secrets involved are the email account password and, if you use it, your Azure OpenAI key. Both are stored in a Key Vault in *your* subscription. See [Azure components](../architecture/04-azure-components.md).

**Can I use a service principal instead of a user account for the agent's connections?**
Yes. A user account, a service principal, or a Fabric workspace identity are all supported. See [Connections](../setup/05-connections.md).

---

## What you need

**Do I need the Capacity Metrics App?**
Real-time detection works without it. The historical AI Insights and the trend section of the monitoring report rely on it, so we strongly recommend installing it. See [Prerequisites](../setup/prerequisites.md).

**Do I need an Azure subscription?**
Yes. The agent deploys a small set of resources — a Function App, an Automation Account, a Key Vault, and their supporting services — to a resource group you choose. Without them, findings still appear in the agent's screens, but automatic scaling, schedules, and email alerts aren't available.

**Do I need Azure OpenAI?**
Only if you want AI-written summaries of each insight. Bring your own Azure OpenAI resource; the agent uses your key. See [Azure OpenAI setup](../setup/06-azure-openai-api-key.md).

**Who needs to be involved in setup?**
A Fabric Administrator (tenant settings and connection approval), someone able to grant Microsoft Entra admin consent, someone with Contributor rights on an Azure resource group, and — for email alerts — an Exchange Administrator. See [Prerequisites](../setup/prerequisites.md).

**How long does setup take?**
Plan for about two hours for a first install. Most of that is automated: the Fabric deployment runs for 20–25 minutes on its own, and the Azure template takes 2–3 minutes. See the [setup guide](../setup/prerequisites.md).

---

## How it behaves

**How fast does it notice a problem?**
Utilization data arrives about every 30 seconds and is evaluated as it lands, so a finding typically appears within seconds to a few minutes of a condition starting — depending on the sensitivity and durations you've set.

**Will it change my capacity without asking?**
Only if you turn on **Auto-Scale** for that capacity, and only within the minimum, maximum, and hours you set. Out of the box, the agent recommends and does not act. See [Auto-Scale](../operations/autoscaling-and-scheduling.md#auto-scale).

**Can it jump from F32 straight to F128?**
Auto-Scale moves one SKU step at a time (F32 → F64) so cost changes stay predictable. If you want a bigger jump at a known time, schedule it explicitly with a **F-SKU Schedule** action.

**What happens when I pause a capacity?**
Monitoring for that capacity pauses too. When you resume the capacity, re-enable monitoring — or simply remove and re-add the capacity in the agent to reset it. See [Known limitations](../operations/known-limitations.md).

**Why didn't it scale back down right after scaling up?**
Fabric takes a minute or two to report the new SKU after a change. The agent waits three minutes before evaluating again so it never flip-flops. See [Known limitations](../operations/known-limitations.md).

**How do I silence alerts during maintenance?**
Use **Suppress Finding Until** (pauses detection) or **Suppress Notification** (pauses emails but keeps actions running), with an end time so everything resumes automatically. See [Notifications](../operations/notifications.md).

**Does the agent itself use capacity?**
A small amount — its monitoring and daily analysis run on the capacity hosting the agent's workspace. Use **monitoring windows** to stop collection during hours that don't matter. See [Monitoring windows](../operations/autoscaling-and-scheduling.md#real-time-monitoring).

**Can it monitor capacities in other workspaces or regions?**
Yes. The agent lives in one workspace; the capacities you onboard can be anywhere in your tenant. The person onboarding each capacity needs to be an administrator of it. See [Onboard a capacity](../operations/onboard-a-capacity.md).

---

## When something looks wrong

**The findings list is empty.**
Check that monitoring for the capacity is active and that utilization is actually crossing the thresholds you set. See [Troubleshooting](../operations/troubleshooting.md).

**The monitoring report is blank.**
The daily data load or the report refresh probably hasn't completed yet, or a connection needs re-authorizing. See [Troubleshooting](../operations/troubleshooting.md).

**An automatic action or schedule didn't run.**
Almost always a missing permission on one of the automation resources. See [Troubleshooting](../operations/troubleshooting.md).

**I can't find the Fabric Admin Agent under "New item."**
The additional-workloads tenant settings aren't enabled yet, or haven't finished propagating. See [Tenant settings](../setup/01-tenant-settings.md).

---

*Still have a question? Email CustomerSuccess@MAQSoftware.com.*

*See also: [What it does](what-it-does.md) · [Key features](key-features.md) · [Use cases](use-cases.md)*
