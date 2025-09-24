# Hands-On Guidance: Azure Monitor + AKS + AIOps

## Prerequisites
- Azure subscription with permission to deploy resources
- An AKS (Azure Kubernetes Service) cluster
- Log Analytics workspace linked to your AKS cluster
- Familiarity with Azure Portal or Azure CLI
- Python & ML background (for custom ML pipeline section)

---

## 1. Use Azure Monitor’s Built-In AIOps Capabilities

### Step 1: Ensure AKS Diagnostics Are Sending Data to Log Analytics
- In Azure Portal, go to your AKS resource.
- Under **Monitoring**, ensure diagnostics are enabled and connected to your Log Analytics workspace.

### Step 2: Explore Built-In AIOps Features
- Go to **Azure Monitor** > **Logs**.
- Use **Log Analytics** to query AKS metrics/logs (e.g., KubePodInventory, InsightsMetrics).
- Navigate to **Monitor > Alerts > Alert rules**.
    - View **Dynamic Thresholds** (AIOps-powered anomaly detection).
    - Create a new alert rule. When defining conditions, select **Dynamic threshold** for supported metrics.
- Try **Smart Detection** (e.g., metric anomalies in Application Insights).
    - If you have App Insights enabled for your AKS workloads, review **Failures** and **Performance** tabs for detected anomalies.

### Step 3: Review Insights and Recommendations
- In **Azure Monitor > Insights > Containers**, check for health, performance, and anomaly alerts.
- Review recommendations or incident root cause suggestions surfaced by AIOps.

---

## 2. Create Your Own ML Pipeline to Analyze Azure Monitor Logs

### Step 1: Export Log Data
- In Azure Monitor Logs, run Kusto queries to extract relevant data (e.g., CPU anomalies, pod restarts).
- Export results:
    - Use the **Export** button to download as CSV.
    - Or automate with **Azure Data Factory**, **Logic Apps**, or **Azure Synapse** to move data to Blob Storage or other destinations.

### Step 2: Prepare Your ML Environment
- Set up an **Azure Machine Learning Workspace**.
- Upload exported log data to AML datastore.

### Step 3: Build & Train Your ML Model
- Use Azure ML studio or Jupyter notebooks.
- Example pipeline steps:
    1. **Data Ingestion:** Load logs from CSV/Blob/Datastore.
    2. **Preprocessing:** Clean data, extract features (timestamps, resource IDs, metric values).
    3. **Modeling:** Choose anomaly detection, forecasting, or classification algorithms (e.g., Isolation Forest, Prophet, LSTM).
    4. **Training:** Train on historical data.
    5. **Evaluation:** Assess accuracy, precision, recall, etc.

### Step 4: Operationalize & Act on Insights
- Deploy ML model as an endpoint or batch job.
- Integrate with Azure Monitor via Logic Apps or Azure Functions:
    - Automatically trigger notifications or remediation when anomalies are detected.
    - Write results back to Log Analytics or Azure Monitor for dashboards.

### Step 5: Visualize and Automate
- Use Power BI or Azure Monitor Workbooks to visualize custom ML insights.
- Set up automation for periodic data extraction, scoring, and response.

---

## Additional Resources
- [Azure Monitor AIOps documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/aiops/aiops-machine-learning)
- [Kusto Query Language (KQL) basics](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/)
- [Azure Machine Learning Quickstart](https://learn.microsoft.com/en-us/azure/machine-learning/quickstart-create-resources)
- [Automate with Logic Apps](https://learn.microsoft.com/en-us/azure/logic-apps/logic-apps-overview)

---

## Summary Table

| Approach                      | What You Do                              | Outcome                                |
|-------------------------------|------------------------------------------|----------------------------------------|
| Built-in AIOps (Azure Monitor)| Use portal features, dynamic thresholds  | Out-of-box anomaly detection, insights |
| Custom ML Pipeline            | Export data, build ML model, automate    | Advanced analysis, custom actions      |
