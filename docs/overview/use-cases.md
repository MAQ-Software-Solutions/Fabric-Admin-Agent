# Use Cases

Who the Fabric Admin Agent is for, and the everyday situations where it pays for itself.

---

## Is this for you?

### You run the whole Fabric tenant

You're accountable for every capacity but can't watch all of them. The agent gives you one scorecard for the entire estate, one queue of findings, and a complete record of what happened and why. You decide how much autonomy to grant each capacity — alerts only on production, full automation on dev/test.

### You own one or two capacities

You're the person who gets the call when reports slow down. The agent does the triage before you open a tab: it tells you a spike is building, which action fits, and — if you've enabled it — has already scaled the capacity within the ceiling you set. Fewer fire drills, more time for real platform work.

### You own the Fabric budget

You need to justify spend and find savings without a data-platform background. The agent replaces estimates with recommendations backed by actual usage: which capacities are oversized, when they sit idle, and what a pause/resume schedule would recover. Every recommendation shows its evidence.

### You set the standards

You define how Fabric should be operated. The agent enforces those standards mechanically — minimum and maximum SKUs, allowed scaling hours, required notification recipients — and logs every action to a store you own, so policy doesn't depend on individual vigilance.

---

## Seven situations the agent handles

### 1. Month-end reporting keeps throttling

**The situation.** A finance capacity hits its ceiling on the last two business days of every month. Users see slow dashboards, someone scales up in a hurry, and nobody remembers to scale back.

**What the agent does.** With Throttling Risk set to **High** sensitivity, a finding appears as pressure builds — not after throttling starts. With Auto-Scale enabled between F64 and F128, the capacity steps up on its own and steps back down once the rush is over.

**Where to set it up.** [Throttling Risk](../operations/configure-findings.md#throttling-risk) · [Auto-Scale](../operations/autoscaling-and-scheduling.md#auto-scale)

---

### 2. Dev and test capacities run all night and all weekend

**The situation.** Three development capacities are used weekdays from 8 to 6, but billed 24×7. That's roughly three-quarters of the cost buying nothing.

**What the agent does.** A **F-SKU Schedule** pauses each capacity at 7 PM and resumes it at 7 AM on weekdays, staying paused over the weekend. The calendar view lets the team confirm the plan before switching it on. If you'd rather not work out the schedule yourself, the F-SKU Schedule Recommendation proposes one from actual usage.

**Where to set it up.** [F-SKU Schedule](../operations/autoscaling-and-scheduling.md#f-sku-schedule) · [F-SKU Schedule Recommendation](../operations/configure-findings.md#insights)

---

### 3. A production capacity is still sized for last year's migration

**The situation.** A capacity was set to F256 for a one-time migration eighteen months ago and has run at 20–30% utilization ever since.

**What the agent does.** The **AI Recommendation for Capacity** reviews 30 days of usage and growth and recommends the right lower tier. Idle Capacity detection confirms it with live evidence. The admin reviews the finding, checks the report, and scales down in the next maintenance window.

**Where to set it up.** [AI Recommendation for Capacity](../operations/configure-findings.md#insights) · [Idle Capacity](../operations/configure-findings.md#idle-capacity)

---

### 4. A shared capacity throttles and every team blames another

**The situation.** A shared analytics capacity slows down unpredictably. Nobody can say which workspace is responsible.

**What the agent does.** The **Capacity Throttling** insight shows whether the capacity is too small or a few heavy reports are the cause. **AI Powered Capacity Allocation** names the workspace consuming more than its share and suggests a capacity with room to take it.

**Where to set it up.** [Insights](../operations/configure-findings.md#insights)

---

### 5. Leadership isn't ready to let software scale production

**The situation.** The idea of automated scaling makes people nervous — reasonably so.

**What the agent does.** Leave Auto-Scale off. The agent still produces every scale-up and scale-down recommendation in **Review Active Findings**. After two weeks, the team compares those recommendations against what they would have done, adjusts sensitivity, and then turns Auto-Scale on with a conservative maximum.

**Where to set it up.** [Alert-only mode](../operations/autoscaling-and-scheduling.md#auto-scale) · [Sensitivity](../operations/configure-findings.md#throttling-risk)

---

### 6. A regional capacity is only busy during its own business hours

**The situation.** An Asia-Pacific capacity is idle while North America works. Monitoring it around the clock spends compute and raises idle findings nobody needs.

**What the agent does.** A **monitoring window** limits collection and detection to Monday–Friday, 22:00–10:00 UTC. Outside that window, no compute is spent on monitoring and no findings are raised.

**Where to set it up.** [Monitoring windows](../operations/autoscaling-and-scheduling.md#real-time-monitoring)

---

### 7. A planned load test would set off every alarm

**The situation.** The engineering team is about to push a capacity to 100% on purpose for a four-hour performance test.

**What the agent does.** Set **Suppress Finding Until** the test's end time. Detection and Auto-Scale stay quiet for exactly that window and resume on their own — nobody has to remember to turn them back on.

**Where to set it up.** [Notifications](../operations/notifications.md)

---

## A good fit when… and not yet when…

| A good fit | Not yet a fit |
|---|---|
| You run Fabric **F-SKU** capacities (F2 through F8192) | You use only trial capacities or Power BI Premium **P-SKUs** |
| You want automation *with* explicit guardrails | You cannot deploy a small set of resources to an Azure subscription |
| You have (or can install) the Capacity Metrics App | You need to monitor capacities in a different tenant from the one running the agent |
| You route alerts by ownership — distribution lists, subject-line rules | You cannot send email from your tenant (findings still appear in the agent's screens) |

Not sure? Email **CustomerSuccess@MAQSoftware.com** and describe your environment.

---

*See also: [What it does](what-it-does.md) · [Key features](key-features.md) · [FAQ](faq.md)*
