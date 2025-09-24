# Hands-On Guide: Using Azure Machine Learning with AKS Log Data from Azure Monitor

Build a machine learning pipeline using Azure Machine Learning (AML) to analyze AKS log data captured in Azure Monitor Logs (Log Analytics).

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

### Option 1: Traditional ML (e.g., Isolation Forest)

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

### Option 2: Use Azure OpenAI GPT-4.1 to Generate Proactive Summary Reports

#### **Prerequisites**
- You have an Azure OpenAI resource deployed with GPT-4.1 model.
- You have your API key and endpoint.

#### **Workflow**
1. **Prepare Data for Summarization**
    - Select relevant logs/metrics (e.g., last 24h, anomalies, performance spikes).
    - Convert to a readable format (plain text, CSV, or structured summary).

2. **Call Azure OpenAI GPT-4.1 Model**

    ```python
    import os
    import openai
    import pandas as pd

    # Load AKS log data
    df = pd.read_csv("aks_logs_metrics.csv")  # Or from AML Dataset
    log_text = df.head(50).to_string()  # Limit rows for prompt size

    # Prepare the prompt for GPT-4.1
    prompt = f"""
    You are an expert Azure cloud operations analyst. Based on the following AKS log and metric data, generate a proactive summary report. Highlight anomalies, potential issues, and recommended actions.

    AKS Logs and Metrics:
    {log_text}
    """

    # Azure OpenAI API setup
    openai.api_type = "azure"
    openai.api_base = "https://<your-openai-resource>.openai.azure.com/"
    openai.api_version = "2023-07-01-preview"
    openai.api_key = os.getenv("AZURE_OPENAI_API_KEY")

    # Request GPT-4.1 summary
    response = openai.ChatCompletion.create(
        engine="gpt-4-1",  # Use your deployment name
        messages=[
            {"role": "system", "content": "You are a cloud operations expert."},
            {"role": "user", "content": prompt}
        ],
        max_tokens=1000,
        temperature=0.3
    )

    print(response['choices'][0]['message']['content'])
    ```

#### **Tips:**
- You can automate this to run daily or on-demand (e.g., Azure Functions).
- Adjust the `df.head(50)` to fit API prompt limits.
- The summary will include anomalies, trends, and recommendations directly from GPT-4.1.

---

## Example Output

> **Proactive AKS Summary Report**  
> - Detected 3 pods with abnormal restart rates in the last 24 hours.  
> - CPU usage spike observed in node aks-nodepool1 at 03:00 UTC; recommend scaling out or checking running workloads.  
> - No critical failures detected; overall system health is good.  
> - Recommended actions: Investigate pod restarts in deployment “web-backend”, monitor node resource utilization, and consider periodic scaling.

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


