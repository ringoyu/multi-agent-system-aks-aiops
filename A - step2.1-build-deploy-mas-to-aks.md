# Step 2: Build and Deploy MAS to AKS (Detailed Guide)

This guide will walk you through building the MAS Docker image, pushing it to Azure Container Registry (ACR), and deploying it to your AKS cluster—specifically targeting the user node pool.

---

## **A. Prepare Your Environment**

### 1. **Prerequisites:**
- **Azure Portal** ([portal.azure.com](https://portal.azure.com)) (for ACR, AKS, secrets, monitoring)
- **Local Terminal** (Command Prompt, PowerShell, or Mac/Linux Terminal. VS Code is recommended)
- **Docker Desktop** installed and running
- **Git** installed
- **kubectl** installed ([how to install](https://kubernetes.io/docs/tasks/tools/))

### 2. **Clone the MAS repo**
If not done yet, open your terminal and run:
```bash
git clone https://github.com/Azure-Samples/agentic-aiops-semantic-kernel.git
cd agentic-aiops-semantic-kernel
```

---

## **B. Build the MAS Docker Image**

### 1. **Open your terminal in the MAS repo folder.**

### 2. **Build the Docker image**
```bash
docker build -t mas-app:latest .
```
- This uses the Dockerfile in the repo to build the MAS application image.

---

## **C. Push the Image to Azure Container Registry (ACR)**

### 1. **Find your ACR login server name**
- In Azure Portal, go to **Container registries** → select your ACR (e.g., `masdemoregistry`)
- Copy the **Login server** (e.g., `masdemoregistry.azurecr.io`)

### 2. **Login to your ACR from your terminal**
```bash
az acr login --name masdemoregistry
```

### 3. **Tag your Docker image for ACR**
```bash
docker tag mas-app:latest masdemoregistry.azurecr.io/mas-app:latest
```

### 4. **Push the image to ACR**
```bash
docker push masdemoregistry.azurecr.io/mas-app:latest
```

---

## **D. Prepare Kubernetes Manifests**

### 1. **Create the Deployment manifest (`mas-deployment.yaml`)(`mas-service.yaml`)**

````yaml name=mas-deployment.yaml
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
      nodeSelector:
        agentpool: userpool           # Ensures deployment goes to the user node pool. Replace 'userpool' if your pool is named differently.
      containers:
      - name: mas-app
        image: masdemoregistry.azurecr.io/mas-app:latest
        envFrom:
        - secretRef:
            name: mas-env-secret
        ports:
        - containerPort: 8080
