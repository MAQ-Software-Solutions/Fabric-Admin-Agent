# Known Limitations

## Capacity Off Behavior
* When a capacity is turned off, the Eventstream also turns off automatically.
* The Eventstream **must be turned on manually** after the capacity is re-enabled.

## Telemetry Propagation Delay After Scaling
* Following an automated or scheduled scale action, Microsoft Fabric telemetry continues to report the pre-scaling SKU tier for approximately 2 to 4 reporting cycles (60–120 seconds).
* The Fabric Admin Agent incorporates a built-in **3-minute stale-SKU guardrail** that suppresses pre-scale telemetry during this cooldown window to prevent duplicate or oscillating scale commands.

## SKU Scaling Boundaries
* **Minimum Floor (F2):** F2 is the smallest available Fabric capacity SKU. When a capacity reaches F2, downscale recommendations and automated actions are dropped as no lower tier exists.
* **Maximum Ceiling (F8192):** F8192 is the largest single Fabric capacity tier. When a capacity reaches F8192, upscale recommendations are dropped.
* **Incremental Single-Step Scaling:** Autoscaling transitions one SKU tier at a time (e.g., F32 $\rightarrow$ F64). It does not perform multi-step jumps in a single cycle, ensuring controlled and predictable cost expansion.

## Dependency on Capacity Metrics App Refresh
* Historical AI batch insights (such as F-SKU schedule recommendations and workspace reallocation) depend on data from the official Microsoft Fabric Capacity Metrics App. If the metrics app semantic model refresh is delayed, batch insight pipelines will not reflect recent item-level telemetry, though real-time Eventstream monitoring continues unaffected.