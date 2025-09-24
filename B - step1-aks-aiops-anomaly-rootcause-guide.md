# Find Anomalies and Root Cause Analyses in Azure Monitor (AKS, Built-In AIOps features)

## 1. Check AKS Data is Sent to Log Analytics

- In Azure Portal, go to your **AKS cluster**.
- Under **Monitoring**, ensure "Insights" and "Log Analytics workspace" are enabled.
- If not, enable the integration.

---

## 2. View Container Insights (AKS)

- Go to your AKS cluster in Azure Portal.
- In the left menu, select **Insights** (under Monitoring).
- You'll land on **Container Insights** – this is the main dashboard for AKS health and anomalies.

### What you’ll see:
- **Health Overview**: Node/pod status and issues.
- **Performance Charts**: CPU/memory, with potential anomaly highlights.
- **Alerts**: Recent alerts, including those triggered by dynamic thresholds (AIOps).

**Screenshot Guidance:**  
- From AKS resource > Monitoring > Insights > Containers

---

## 3. Explore Alerts and Anomalies

- In the **Container Insights** page, scroll to the **Alerts** section.
- Look for alerts labeled as "**Dynamic threshold**" or "**Anomaly detected**".
    - These are powered by AIOps (machine learning on metric baselines).
- Click on alert rows for details:  
    - See anomaly graphs, expected vs. actual values.
    - Root cause suggestions may appear if the system detected correlated events.

**Screenshot Guidance:**  
- AKS > Insights > Alerts
- Alert details > “Dynamic threshold” badge

---

## 4. Investigate Smart Detection (Application Insights)

> If your AKS workloads use **Application Insights** (for .NET/Java apps), you get additional AIOps features:

- Go to your **Application Insights** resource in Azure Portal.
- In the left menu, select **Failures** or **Performance**.
- Look for banners or entries labeled **Smart Detection** or **Anomaly**.
    - These will describe detected issues, correlated events, and sometimes surface root cause analysis.

**Screenshot Guidance:**  
- Application Insights > Failures > Smart Detection
- Application Insights > Performance > Anomaly Detection

---

## 5. Root Cause Analysis (RCA)

- For major alerts, Azure Monitor may surface **Root Cause Analysis** sections directly in the alert details.
- If available, you’ll see:
    - **Summary** of issue and impact
    - **Suspected cause** (e.g., deployment event, spike, resource exhaustion)
    - **Linked events** (e.g., correlated pod failures, node drain events)

**Screenshot Guidance:**  
- AKS > Insights > Alerts > Click an alert > Look for RCA section

---

## 6. Use Log Analytics for Custom Investigation

- Go to **Azure Monitor** > **Logs**.
- Run KQL queries on tables like `KubePodInventory`, `InsightsMetrics`, `AzureDiagnostics` to find patterns.
- Use built-in anomaly detection functions in KQL, e.g.:
    ```kusto
    InsightsMetrics
    | where Namespace == "container.azm.ms.kubelet" and Name == "cpuUsageNanoCores"
    | summarize avg(CpuUsage)=avg(Total) by bin(TimeGenerated, 1h)
    | extend anomaly = iff(avg(CpuUsage) > threshold, 1, 0)
    ```
- Visualize results or set up alert rules.

---

## 7. Documentation & Help

- [Container Insights Overview](https://learn.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-overview)
- [Anomaly Detection in Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-dynamic-thresholds)
- [Smart Detection in App Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/proactive-diagnostics)
- [Root Cause Analysis in Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-root-cause-analysis)

---

## Tip

If you don’t see anomalies or RCA sections, make sure:
- You have enough data flowing (AKS is active, logs/metrics are ingested).
- You’ve waited long enough for Monitor to analyze and baseline metrics (sometimes a few hours/days).

| Feature          | Where to Find         | Description                                 |
|------------------|----------------------|---------------------------------------------|
| Container Insights| AKS > Insights       | Health, performance, alerts, anomalies      |
| Dynamic Thresholds| Alerts section       | ML-based anomaly alerts                     |
| Smart Detection   | App Insights         | Smart issue detection, sometimes RCA        |
| Root Cause Analysis| Alert details       | Impact summary, suspected cause, links      |
---



