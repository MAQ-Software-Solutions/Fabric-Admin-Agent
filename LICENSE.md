Fabric Admin Agent
Copyright (c) 2026 MAQ Software. All rights reserved.

The Fabric Admin Agent, including its software, the Azure Resource Manager
templates, and the documentation in this repository, is proprietary to MAQ
Software. Your use of it is governed by:

  - MAQ Software Terms of Service
    https://maqsoftware.com/terms-of-service/

  - The terms of the Fabric Admin Agent offer on Microsoft Marketplace
    https://marketplace.microsoft.com/en-us/product/maqsoftware.fabricadminagent

  - MAQ Software Privacy Statement
    https://maqsoftware.com/privacy-statement/

No rights are granted except as set out in those terms.


BILLING
-------

Billed by MAQ Software through Microsoft Marketplace

  The Fabric Admin Agent is a Microsoft Marketplace SaaS offer with a single
  metered plan. There is no recurring license fee. You pay only for the
  capacity hours you use.

    Plan:               Fabric Admin Agent Plan
    Billing dimension:  Capacity Hour
    Unit of measure:    One hour during which one Fabric capacity is
                        onboarded to the agent
    Rate:               One flat hourly rate for every capacity, regardless
                        of its SKU or region. Current pricing is published
                        on the Marketplace listing above.

  How usage is counted

  - A capacity accrues capacity hours from the time it is onboarded to the
    agent until it is offboarded. Time is measured to the second, so 30
    minutes counts as 0.5 capacity hours. If a capacity is onboarded and
    offboarded more than once, every period counts.
  - Pausing, resuming, or resizing a capacity does not affect this charge.
    A paused capacity keeps accruing capacity hours while it remains
    onboarded. To stop charges for a capacity, offboard it.
  - Usage from all of your capacities is added together and reported to
    Microsoft Marketplace once an hour, for each UTC clock hour, shortly
    after that hour ends. For example, if one capacity is onboarded for the
    whole hour, a second for 30 minutes, and a third for 15 minutes, that
    hour is reported as 1.75 capacity hours.
  - Charges appear on the Azure invoice for the Azure subscription you chose
    when you purchased the offer.
  - Only time during which your Marketplace subscription is active is
    billed. No usage is metered while it is suspended (for example, after a
    payment failure) or after you cancel it, even when the change happens
    partway through an hour.

Billed by Microsoft to your own subscriptions (not included in the Fabric
Admin Agent price)

  - Microsoft Fabric capacity usage. The agent's Eventstreams, Eventhouse,
    Lakehouse, notebooks, pipelines, and reports run on a Fabric capacity in
    your tenant and consume its Capacity Units.
  - Azure resources deployed to your subscription: a Function App (Flex
    Consumption plan), Storage account, Log Analytics workspace, Application
    Insights, Key Vault, and Automation Account.
  - Optional services you connect: Exchange Online High Volume Email for
    email alerts, and your own Azure OpenAI resource for AI-written insight
    summaries.

When you enable Auto-Scale or F-SKU Schedule, the agent scales, pauses, and
resumes Fabric capacities on your behalf. This changes your Fabric capacity
charges from Microsoft, but not your Fabric Admin Agent charges, because the
capacity-hour rate is the same for every SKU. Auto-Scale never scales above
the maximum SKU you set.


Questions about licensing or billing: CustomerSuccess@MAQSoftware.com
