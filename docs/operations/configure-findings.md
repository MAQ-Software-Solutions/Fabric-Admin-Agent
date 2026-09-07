# Configure Findings

For each onboarded capacity, you can configure monitoring thresholds and AI insights through the **Capacity Monitoring Agent** section in the **Configuration** tab. Findings are categorized into **Alerts** and **Insights**.

## Alerts
Alerts generate immediate findings based on configured thresholds.

### Throttling Risk
1. Navigate to **Configuration > Capacity Monitoring Agent > Throttling Risk**.
2. Select the onboarded capacity.
3. Configure the **Sensitivity Level**: The detection sensitivity level for anomaly findings. This determines how aggressively the agent flags potential throttling events based on consumption patterns.
4. Configure notification settings in the right pane and click **Save**. Refer to [notifications.md](notifications.md) for notification configuration details.

![Throttling Risk](../assets/images/operations/throttling_risk.png)

### Idle Capacity
1. Navigate to **Configuration > Capacity Monitoring Agent > Idle Capacity**.
2. Select the onboarded capacity.
3. Configure the idle capacity detection parameters:
   * **Threshold (%)**: The maximum capacity utilization percentage required for the capacity to be considered idle.
   * **Duration (mins)**: The approximate number of minutes the capacity must remain below the specified threshold percentage to trigger a finding.
4. Configure notification settings in the right pane and click **Save**. Refer to [notifications.md](notifications.md) for notification configuration details.

![Idle Capacity](../assets/images/operations/idle_capacity.png)

## Insights
Insights provide AI-driven analysis and recommendations for capacity management. Navigate to the respective section, enable the capacity, and configure the notification settings as required:

* **Capacity Throttling:** Configure AI-powered capacity throttling insights for this capacity.
* **F-SKU Schedule Recommendation:** AI recommendations for optimal F-SKU scheduling actions.
* **AI Powered Capacity Allocation:** Recommendations on workspace allocation across capacities.
* **AI Recommendation for Capacity:** Overall sizing and scaling recommendations.

Refer to [notifications.md](notifications.md) for notification configuration details.

![Capacity Throttling](../assets/images/operations/capacity_throttling.png)