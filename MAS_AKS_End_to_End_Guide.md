# MAS (Multi-Agent System) on AKS: End-to-End Guide

This step-by-step guide walks you through deploying the MAS app from [Azure-Samples/agentic-aiops-semantic-kernel](https://github.com/Azure-Samples/agentic-aiops-semantic-kernel) to Azure Kubernetes Service (AKS), wiring up Azure Monitor alerts, and validating the MAS agent’s automated response.

---

## Prerequisites

- Azure subscription
- Azure CLI (`az`)
- Docker CLI (`docker`)
- kubectl (`kubectl`)
- Python 3.12+
- [GitHub repo cloned locally](https://github.com/Azure-Samples/agentic-aiops-semantic-kernel)

---

## 1. Create an AKS Cluster

```bash
# Login to Azure
az login

# Set variables
RESOURCE_GROUP=mas-demo-rg
AKS_NAME=mas-demo-aks
LOCATION=eastus

# Create a resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Create an AKS cluster (basic example; adjust node count and VM size for your needs)
az aks create \
  --resource-group $RESOURCE_GROUP \
  --name $AKS_NAME \
  --node-count 1 \
  --generate-ssh-keys \
  --enable-addons monitoring

# Get AKS credentials for kubectl
az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_NAME
```

---

## 2. Build and Deploy MAS to AKS

### a. Build Docker Image

```bash
cd agentic-aiops-semantic-kernel
docker build -t mas-app:latest .
```

### b. Push Image to Azure Container Registry (ACR)

```bash
# Create an Azure Container Registry
ACR_NAME=masdemoregistry
az acr create --resource-group $RESOURCE_GROUP --name $ACR_NAME --sku Basic

# Log in to your ACR
az acr login --name $ACR_NAME

# Tag the image for ACR
docker tag mas-app:latest $ACR_NAME.azurecr.io/mas-app:latest

# Push the image to ACR
docker push $ACR_NAME.azurecr.io/mas-app:latest
```

### c. Prepare Kubernetes Manifests

Create a file named `mas-deployment.yaml`:

```yaml name=mas-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mas-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mas-app
  template:
    metadata:
      labels:
        app: mas-app
    spec:
      containers:
      - name: mas-app
        image: masdemoregistry.azurecr.io/mas-app:latest # Use your ACR image
        envFrom:
        - secretRef:
            name: mas-env-secret
        ports:
        - containerPort: 8080
```

Create a file named `mas-service.yaml`:

```yaml name=mas-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: mas-app-service
spec:
  type: LoadBalancer
  selector:
    app: mas-app
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

### d. Create Kubernetes Secret for Environment Variables

- Copy `.env.example` to `.env` and fill out required values (OpenAI, Azure Monitor, etc.)
- Create a secret manifest from your `.env` file:

```bash
kubectl create secret generic mas-env-secret --from-env-file=.env
```

### e. Deploy MAS to AKS

```bash
kubectl apply -f mas-deployment.yaml
kubectl apply -f mas-service.yaml
```

- Wait for the external IP to be assigned to your MAS service:
```bash
kubectl get svc mas-app-service
```
- Note the `EXTERNAL-IP` value for later use.

---

## 3. Set Up Azure Monitor Alert to Trigger MAS

### a. Create a Log Analytics Workspace (if not already enabled)

```bash
LOG_ANALYTICS_NAME=mas-logs
az monitor log-analytics workspace create --resource-group $RESOURCE_GROUP --workspace-name $LOG_ANALYTICS_NAME
```

### b. Set Up Alert Rule (example: on AKS node CPU)

- In Azure Portal, go to your AKS cluster → Monitoring → Alerts → Create Alert Rule.
- For "Condition", select a metric (e.g., node CPU high).
- For "Action Group", create a new group:
    - **Choose "Webhook" as the action type**
    - Set the URI to:  
      `http://<EXTERNAL-IP>:8080/alert`
    - For testing, use sample alert payload ([common schema](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-common-schema)).

---

## 4. Test MAS Agent Processing

### a. Send a Sample Alert (Manual Test)

You can trigger MAS locally (or via Postman/curl) using the `/alert` endpoint.

```bash
curl -X POST http://<EXTERNAL-IP>:8080/alert \
  -H "Content-Type: application/json" \
  -d '{
    "schemaId": "AzureMonitorCommonAlertSchema",
    "data": {
      "essentials": {
        "alertId": "sample-id",
        "alertRule": "CPUHigh",
        "severity": "Sev3",
        "signalType": "Metric",
        "monitorCondition": "Fired",
        "monitoringService": "Platform",
        "alertTargetIDs": ["<AKS_RESOURCE_ID>"],
        "originAlertId": "sample-origin-id",
        "firedDateTime": "2025-09-23T12:00:00.000Z",
        "description": "Sample CPU high alert"
      }
    }
  }'
```

### b. Observe the MAS Agent Output

- Check logs in your MAS container for activity:
```bash
kubectl logs deployment/mas-app
```
- The AKS Specialist agent should process the alert and run diagnostic or remediation commands (e.g., using `kubectl`), based on its prompt and logic.

---

## 5. Troubleshooting & Validation

- If you do not see expected behavior:
    - Double-check environment variables and secrets
    - Ensure your MAS pod has network access and correct RBAC/identity permissions
    - Check Azure Monitor alert schema matches what MAS expects

---

## 6. Clean Up Resources

```bash
az group delete --name $RESOURCE_GROUP --yes --no-wait
```

---

## References
- [MAS Repo](https://github.com/Azure-Samples/agentic-aiops-semantic-kernel)
- [Azure Monitor Common Alert Schema](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-common-schema)
- [AKS Documentation](https://learn.microsoft.com/en-us/azure/aks/)
- [Azure Container Registry](https://learn.microsoft.com/en-us/azure/container-registry/)

---

**You now have a fully functional MAS deployment on AKS, with automatic alert-driven responses!**