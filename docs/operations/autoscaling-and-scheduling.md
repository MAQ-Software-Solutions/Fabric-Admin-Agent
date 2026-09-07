> **Prerequisites**
>
> Before configuring Real Time Monitoring, Auto-Scale, or F-SKU Schedules, ensure you have access to the Azure Resource Group that contains the deployed Automation Account used by the workload item. If the required permissions are managed through Azure Privileged Identity Management (PIM), activate the appropriate role before creating, modifying, or deleting monitoring and scheduling configurations.

# Autoscaling & Scheduling

Configure automated operational boundaries, real-time monitoring windows, and scheduled scaling actions for your capacities.

## Real Time Monitoring
Set times for the agent to perform real-time monitoring.
1. Navigate to **Configuration > Capacity Monitoring Agent > Real Time Monitoring** and enable the capacity.
2. Configure the **Real Time Monitoring Schedule**:
   * **Start Date / End Date:** The calendar window for monitoring.
   * **Real Time Monitoring Frequency (Days):** Operating days of the week.
   * **Daily Start Time (UTC) / Daily End Time (UTC):** The daily time window.
3. Configure notification settings in the Real Time Monitoring Notifications section and click **Save**. Refer to [notifications.md](notifications.md) for notification configuration details.

![Real Time Monitoring](../assets/images/operations/real_time_monitoring.png)

## Auto-Scale
Set times for the agent to perform auto-scaling and configure F-SKU limits.
1. Navigate to **Configuration > Capacity Monitoring Agent > Auto-Scale**  and enable the capacity.
2. Configure **Auto-Scaling Limits**:
   * **Max F-SKU Auto-Scale Limit:** F-SKU will not auto-scale above this limit.
   * **Min F-SKU Auto-Scale Limit:** F-SKU will not auto-scale below this limit.
3. Configure the **Auto-Scaling Schedule** (Start Date, End Date, Frequency, and Daily Time Window).
4. Configure notification settings in the Auto-Scaling Notifications section and click **Save**. Refer to [notifications.md](notifications.md) for notification configuration details.

![Auto-Scale](../assets/images/operations/auto_scale.png)

## F-SKU Schedule
Configure daily times for when capacity is paused, resumed, or scaled.
1. Navigate to **Configuration > Capacity Monitoring Agent > F-SKU Schedule** and enable the capacity.
2. Select **Schedule New Action** and create Pause, Resume, Upscale, or Downscale schedules.
3. Choose the recurrence (**One-Time, Daily, or Weekly**), Date, Start Time, and Target F-SKU.
4. Review the generated summary (e.g., "Scale up to F32 on Monday, September 7 at 7:00 AM").
5. Click **Update Schedule** and then **Save**.
![Schedule Action](../assets/images/operations/f-sku_schedule.png)
6. Review all configured F-SKU schedules for the capacity in the calendar view. The calendar displays each scheduled action and visualizes the capacity state between actions, showing the timeline up to the start time of the next configured schedule.
![F-SKU Schedule Calendar](../assets/images/operations/f-sku_schedule_calendar.png)
7. Configure notification settings in the F-SKU Schedule Notifications section and click **Save**. Refer to [notifications.md](notifications.md) for notification configuration details.
8. To remove an individual scheduled action, select the **bin icon** on the corresponding schedule in the calendar.
![F-SKU Schedule Delete](../assets/images/operations/f-sku_schedule_delete.png)
9. To remove all scheduled actions for the capacity, select **Clear All Actions**.
![F-SKU Schedule Clear All](../assets/images/operations/f-sku_schedule_clear_all.png)