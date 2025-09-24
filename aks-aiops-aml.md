# Hands-On Guide: Using Azure Machine Learning with AKS Log Data from Azure Monitor

## Scenario
You want to build a machine learning pipeline using Azure Machine Learning (AML) to analyze AKS log data captured in Azure Monitor Logs (Log Analytics).

---

## Step 1: Prepare Data in Log Analytics

- Query AKS logs in Azure Monitor Logs (Log Analytics) using Kusto Query Language (KQL).
    - Example: Find pod restarts in last 7 days
      ```kusto
      KubePodInventory
      | where ContainerID == ""
      | summarize RestartCount = count() by Name, bin(TimeGenerated, 1h)
      ```
- Export results as CSV:
    - In Log Analytics, run your KQL query.
    - Click **Export** > **CSV**.

---

## Step 2: Create/Access Azure Machine Learning Workspace

- In Azure Portal, create an **Azure Machine Learning workspace**.
- Launch **AML Studio** (studio.azureml.net).

---

## Step 3: Upload Data to AML

- In AML Studio, go to **Data > +Create > Data asset**.
- Upload the CSV file exported from Log Analytics.

---

## Step 4: Build and Train Your ML Model

### Option 1: Use AML Designer (No-Code)

- Go to **Designer** in AML Studio.
- Drag **Data Input** node for your CSV.
- Add Data Transformation steps (e.g., Clean Missing Data, Select Columns).
- Add **Train Model** node (choose algorithm: e.g., Anomaly Detection > Isolation Forest).
- Split data, train, and evaluate.

### Option 2: Use Notebooks (Python Code)

- In AML Studio, go to **Notebooks** > Start a new notebook.
- Example code:
    ```python
    import pandas as pd
    from azureml.core import Workspace, Dataset
    from sklearn.ensemble import IsolationForest

    ws = Workspace.from_config()
    dataset = Dataset.get_by_name(ws, 'aks-logs')
    df = dataset.to_pandas_dataframe()

    # Feature engineering
    X = df[['RestartCount', 'TimeGenerated']]

    # Model training
    clf = IsolationForest()
    clf.fit(X)

    # Predict anomalies
    df['anomaly'] = clf.predict(X)
    ```

---

## Step 5: Deploy the Model

- In AML Studio, register the trained model.
- Create an **inference pipeline** (real-time endpoint or batch).
- Deploy as a web service (e.g., Azure Container Instance or AKS cluster).

---

## Step 6: Operationalize & Integrate

- Set up Logic Apps or Azure Functions to call your AML endpoint with new AKS log data.
- Automatically trigger alerts or remediation if anomalies are detected.

---

## Step 7: Visualize Results

- Write predictions back to Log Analytics or visualize in Power BI/Azure Workbooks.

---

## Extra Resources

- [Azure Machine Learning Studio Docs](https://learn.microsoft.com/en-us/azure/machine-learning/)
- [Data Prep for AML](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-create-register-datasets)
- [Deploying ML Models](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-deploy-and-where)

---

## Summary

This workflow explicitly uses Azure Machine Learning for building, training, and deploying a model based on AKS logs. The integration with Azure Monitor/Log Analytics is in the data export & operationalization steps.

Let me know if you want a full notebook template, or guidance for a specific anomaly detection algorithm!