# Notifications

Notification preferences are configured individually for every finding, monitoring, and auto-scaling category via the right-hand configuration pane.

## Available Settings
* **Suppress Finding:** Applicable only for Alerts and Insights. A toggle that temporarily disables the actual detection and generation of findings for the selected capacity.
* **Suppress Notification:** Applicable only for Auto-Scale, Real Time Monitoring, and F-SKU Schedules. A toggle that pauses agent notifications (such as alerts that a Real Time Monitoring or Auto-Scale window has started/ended, or that an F-SKU schedule action has been executed) without stopping the underlying action itself for the selected capacity.
* **Suppress Finding Until / Suppress Alerts Until:** Specifies the exact date and time until the finding generation or notifications remain suppressed.
* **Communication Channel:** Check the box (e.g., "Send me an email") to enable delivery.
* **Email:** A comma-separated list of email addresses that will receive the notifications.
* **Email Subject:** A customizable subject line for the generated emails.