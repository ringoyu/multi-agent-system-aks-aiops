# How to Set Up and See Dynamic Thresholds / Anomaly Detection in Azure Monitor for AKS

## 1. Go to Azure Monitor > Alerts

1. In Azure Portal, search for **Azure Monitor**.
2. In the left menu, select **Alerts**.
3. Click **+ Create > Alert rule**.

## 2. Select Resource: Your AKS Cluster or Log Analytics Workspace

1. For **resource**, pick your AKS cluster or the Log Analytics workspace connected to AKS.

## 3. Set Condition: Choose a Metric That Supports Dynamic Thresholds

1. Click **Add condition**.
2. In the **signal name** list, choose a supported metric (e.g., CPU usage, memory, pod restart count).
    - Example: `CPU Usage` from InsightsMetrics.
3. In the condition configuration, look for the **Threshold type** option.
4. Select **Dynamic** (instead of Static).
    - You’ll see a note: *“Thresholds are determined dynamically using machine learning.”*

**Screenshot Guidance:**  
- Condition blade > Threshold type dropdown > Choose “Dynamic”

## 4. Complete the Alert Rule

1. Set up the rest of the alert: action group, severity, etc.
2. Save the alert rule.

## 5. Wait for Alerts

- Once set, Azure Monitor uses ML to baseline your metric and will trigger alerts when anomalies are detected.
- Alerts you receive from this rule will say **“Dynamic threshold”** in their details.

---

## To See These Alerts

- Go to **Azure Monitor > Alerts > Manage alert rules**.
    - Find your alert rule. It will show “Dynamic” under threshold type.
- When an alert is triggered, the alert details will include “Dynamic threshold” and usually a link to a graph showing the anomaly.

---

## Application Insights: Smart Detection

- If using Application Insights, failures/performance tabs will show “Smart Detection” badges for anomalies.
- This is **only for workloads with App Insights installed**.

---

## Documentation Reference

- [Dynamic Thresholds in Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-dynamic-thresholds)
- [Supported Metrics](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-dynamic-thresholds#supported-metrics)
- [How to Create Alert Rules](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-create-new-alert-rule)

---

## Troubleshooting

- If you don’t see “Dynamic” as an option, check:
    - Your metric is supported (not all metrics support dynamic thresholds)
    - You’re selecting from the right resource type (AKS/Log Analytics)
    - You’re in the correct Azure region (some features may be region-specific)
    - You have monitoring enabled and data flowing

---

## Summary

**You must create an alert rule using “Dynamic threshold” to see anomaly detection—otherwise, these features won’t appear automatically in AKS dashboards or insights.**
