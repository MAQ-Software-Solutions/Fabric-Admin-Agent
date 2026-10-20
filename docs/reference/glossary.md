# Glossary

* **Azure Automation Account**: An Azure resource that hosts runbooks, schedules, and managed identities to execute scheduled operational actions such as capacity pause, capacity resume, and capacity F-SKU scaling.
* **Azure Function App**: The core compute resource that hosts APIs for capacity operations, notification services, and automation workflows.
* **Capacity Metrics App**: A Microsoft Fabric application whose semantic model provides historical capacity utilization data, required as a prerequisite for capacity monitoring.
* **Capacity Overview Events**: A real-time telemetry stream native to Microsoft Fabric that provides live Capacity Unit (CU) consumption metrics. It serves as the primary data source for the agent.
* **CU (Capacity Unit)**: The measurement of compute power in Microsoft Fabric. 
* **Eventstream**: A Fabric artifact that ingests, transforms, and routes real-time data streams. 
* **F-SKU**: The Fabric Capacity Stock Keeping Unit (e.g., F2, F64, F8192) that dictates the maximum compute bounds of a capacity.
* **High Volume Email (HVE)**: A dedicated Microsoft 365 Exchange account type designed for application-based, internal tenant mass email delivery.
* **Idle Capacity**: A real-time Alert finding triggered when a capacity's CU usage stays below a configured threshold percentage for a specified consecutive duration. Resolves to a downscale suggestion.
* **KQL Database / Eventhouse**: The Azure Data Explorer-based analytical database in Fabric used to store and query the streamed capacity telemetry and findings.
* **Managed Identity**: A system-assigned Azure identity used by the Function App and Automation Account to securely access Key Vault secrets and Fabric APIs without manual credential management.
* **Materialized View**: A KQL Database feature used in this workload (e.g., `SustainedLoadDetection_MV`) to continuously roll up capacity utilization into 1-minute bins for idle load detection.
* **Session**: A logical grouping of consecutive detection windows (e.g., consecutive idle windows or high-pressure windows) used to deduplicate findings and prevent alert fatigue.
* **Throttling Risk**: A real-time Alert finding triggered when capacity consumption patterns (delay, rejection, and overage metrics) indicate an impending risk of interactive or background throttling. Resolves to an upscale suggestion.
* **Update Policy**: A KQL feature that acts as a real-time detection pipeline, automatically triggering functions (e.g., `CalculateCapacityUtilization()`) to transform data as new rows land in source tables.
* **Workload**: A specialized Microsoft Fabric application or architecture (in this context, the Fabric Admin Agent itself) that extends Fabric's native capabilities.