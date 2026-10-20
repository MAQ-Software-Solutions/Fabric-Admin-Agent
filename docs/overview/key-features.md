# Key Features

Everything the Fabric Admin Agent can do for you, grouped by what you're trying to achieve. Each feature links to the page that shows you how to use it.

---

## See what's happening across every capacity

| Feature | What it gives you | Learn more |
|---|---|---|
| **Real-time monitoring** | Live utilization from every onboarded capacity, refreshed about every 30 seconds — no waiting for a report to refresh. | [How it works](what-it-does.md#how-the-agent-helps) |
| **Zero-touch onboarding** | Add a capacity and monitoring starts automatically. Remove and re-add it to reset monitoring if anything goes wrong. | [Onboard a capacity](../operations/onboard-a-capacity.md) |
| **Tenant-wide scorecard** | One screen showing every capacity's SKU, state, and open findings. | [What you'll see](what-it-does.md#what-youll-see) |
| **Built-in monitoring report** | An embedded report combining live utilization with historical, item-level trends from the Capacity Metrics App. | [Verify deployment](../setup/09-verify-deployment.md) |
| **Monitoring windows** | Choose the days and hours each capacity is monitored — save compute and skip off-hours noise on non-production capacities. | [Monitoring windows](../operations/autoscaling-and-scheduling.md#real-time-monitoring) |

## Catch problems before users do

| Feature | What it gives you | Learn more |
|---|---|---|
| **Throttling Risk detection** | An early warning that a capacity is heading toward its limit, with a recommendation to scale up. Three sensitivity levels let you tune it per capacity. | [Configure findings](../operations/configure-findings.md#throttling-risk) |
| **Idle Capacity detection** | A flag when a capacity runs below a utilization percentage you set for as long as you specify — with a recommendation to scale down. | [Configure findings](../operations/configure-findings.md#idle-capacity) |
| **One finding per event** | A sustained condition produces a single finding, not dozens of duplicate alerts. | [Detection logic](../architecture/05-detection-logic.md) |
| **Maintenance-window muting** | Pause detection until a date and time you choose — ideal for migrations, load tests, or planned work — and it resumes automatically. | [Notifications](../operations/notifications.md) |
| **Finding history** | Findings are retained for review and audit. | [Reference](../reference/kql-tables.md) |

## Make better sizing decisions

| Feature | What it gives you | Learn more |
|---|---|---|
| **Capacity Throttling insight** | Tells you whether throttling is caused by a capacity that is genuinely too small or by a handful of inefficient reports. | [Insights](../operations/configure-findings.md#insights) |
| **AI Recommendation for Capacity** | Reviews 30 days of usage and growth and recommends whether the SKU should go up, down, or stay. | [Insights](../operations/configure-findings.md#insights) |
| **F-SKU Schedule Recommendation** | Finds the times a capacity reliably goes quiet and proposes a pause/resume or scaling schedule to capture the saving. | [Insights](../operations/configure-findings.md#insights) |
| **AI Powered Capacity Allocation** | Identifies noisy-neighbor workspaces and suggests which capacity has room to take them. | [Insights](../operations/configure-findings.md#insights) |
| **AI-written summaries (optional)** | Connect your own Azure OpenAI resource for plain-language explanations of each insight. | [Azure OpenAI setup](../setup/06-azure-openai-api-key.md) |

## Let the agent act — within your limits

| Feature | What it gives you | Learn more |
|---|---|---|
| **Auto-Scale with guardrails** | Scales up on Throttling Risk and down on Idle Capacity, one SKU step at a time, never outside the minimum and maximum you set or the hours you allow. | [Auto-Scale](../operations/autoscaling-and-scheduling.md#auto-scale) |
| **Oscillation protection** | A built-in cooldown after each scaling action prevents the agent from immediately reversing itself. | [Known limitations](../operations/known-limitations.md) |
| **F-SKU Schedule** | Calendar-based pause, resume, scale-up, and scale-down actions — one-time, daily, or weekly — with a visual timeline of the resulting capacity state. | [F-SKU Schedule](../operations/autoscaling-and-scheduling.md#f-sku-schedule) |
| **Alert-only mode** | Keep automation off and still receive every recommendation. Build confidence before granting the agent authority to act. | [Auto-Scale](../operations/autoscaling-and-scheduling.md#auto-scale) |

## Keep the right people informed

| Feature | What it gives you | Learn more |
|---|---|---|
| **Email alerts** | Notifications sent from a dedicated address in your own Microsoft 365 tenant. | [Email setup](../setup/07-email-notifications.md) |
| **Recipients per capacity and finding type** | Route production alerts to the platform team and cost findings to finance — not everything to one shared inbox. | [Notifications](../operations/notifications.md) |
| **Custom subject lines** | Add tags like `[PROD] [CRITICAL]` so mailbox rules and paging tools can act on them. | [Notifications](../operations/notifications.md) |
| **Quiet mode for trusted automation** | Mute routine "action completed" emails while the actions keep running. | [Notifications](../operations/notifications.md) |

## Trust it with your environment

| Feature | What it gives you | Learn more |
|---|---|---|
| **Your data stays in your tenant** | The event store, data lake, reports, and automation resources are all deployed to your Fabric workspace and Azure subscription. | [Architecture overview](../architecture/01-overview.md) |
| **Microsoft Entra sign-in only** | The agent uses your organization's identities and respects Conditional Access policies. | [Data flow](../architecture/02-data-flow.md) |
| **No stored passwords for automation** | Scaling actions use Azure managed identities rather than saved credentials. | [Azure components](../architecture/04-azure-components.md) |
| **Your choice of connection identity** | Use a user account, a service principal, or a Fabric workspace identity for the agent's data connections. | [Connections](../setup/05-connections.md) |
| **Complete audit trail** | Every finding, recommendation, schedule, and action is logged in your tenant. | [Reference](../reference/kql-tables.md) |
| **Published attestation** | Our self-attestation against Microsoft's Fabric workload publishing requirements is available for your review. | [Attestation](../compliance/attestation.md) |

## Get running quickly

| Feature | What it gives you | Learn more |
|---|---|---|
| **One-click Fabric deployment** | All Fabric components are created for you in about 20–25 minutes. | [Deploy the workload](../setup/02-deploy-workload.md) |
| **Guided Azure deployment** | Automation resources are deployed from the workload itself; one Azure template completes the rest. | [Deploy Azure resources](../setup/03-deploy-azure.md) |
| **Illustrated setup guide** | Every step documented with screenshots and the exact role required. | [Setup guide](../setup/prerequisites.md) |
| **Multiple Capacity Metrics Apps** | Link each capacity to its own Capacity Metrics App if your tenant uses more than one. | [Onboard a capacity](../operations/onboard-a-capacity.md) |

---

*See also: [What it does](what-it-does.md) · [Use cases](use-cases.md) · [FAQ](faq.md)*
