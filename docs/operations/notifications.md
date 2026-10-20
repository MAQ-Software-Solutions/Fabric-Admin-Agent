# Notifications

Notification preferences are configured individually for every finding, monitoring, and auto-scaling category via the right-hand configuration pane.

## Available Settings
* **Suppress Finding:** Applicable only for Alerts and Insights. A toggle that temporarily disables the actual detection and generation of findings for the selected capacity.
* **Suppress Notification:** Applicable only for Auto-Scale, Real Time Monitoring, and F-SKU Schedules. A toggle that pauses agent notifications (such as alerts that a Real Time Monitoring or Auto-Scale window has started/ended, or that an F-SKU schedule action has been executed) without stopping the underlying action itself for the selected capacity.
* **Suppress Finding Until / Suppress Alerts Until:** Specifies the exact date and time until the finding generation or notifications remain suppressed.
* **Communication Channel:** Check the box (e.g., "Send me an email") to enable delivery.
* **Email:** A comma-separated list of email addresses that will receive the notifications.
* **Email Subject:** A customizable subject line for the generated emails.

---

## Why Use or Adjust These Settings?

* **Suppress Finding (Muting Anomaly Detection):** Use this setting during planned maintenance windows, major data migrations, historical ETL backfills, or intentional load testing. During such events, running at 100% capacity utilization is deliberate. Suppressing findings prevents the system from generating hundreds of spurious alerts and prevents automated autoscaling from reacting to synthetic test loads.
* **Suppress Notification (Muting Operational Churn):** Use this when automated scaling or scheduled actions (like daily pause/resume) are mature, trusted, and operating smoothly. Muting notifications keeps automated operations running silently in the background while eliminating repetitive operational emails from administrative inboxes. Full execution history is still recorded in the audit logs in KQL DB.
* **Suppress Finding Until / Suppress Alerts Until (Time-Bounded Windows):** Setting an explicit expiration timestamp ensures that monitoring or alerts automatically resume after a scheduled maintenance window ends. This prevents human error where an administrator disables alerts during a deploy and forgets to re-enable them afterward.
* **Targeted Email Recipients:** In multi-team enterprise environments, configuring capacity-specific recipient lists ensures notifications reach the specific engineering or financial stakeholders responsible for that capacity, rather than routing all alerts into a noisy, centralized IT mailbox. We recommend using Microsoft 365 Distribution Lists (e.g., `fabric-ops@contoso.com`) rather than individual emails to ensure continuity across personnel changes.
* **Email Subject Customization:** Adding explicit prefixes or tags (such as `[PROD] [CRITICAL]`, `[FINANCE-CAPACITY]`, or `[DEV-TEST]`) allows email clients to apply automated rules, categorize messages into dedicated folders, or route high-severity alerts into incident management and paging tools such as ServiceNow or PagerDuty.