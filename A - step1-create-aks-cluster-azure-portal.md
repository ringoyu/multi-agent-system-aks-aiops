# Step 1: Create an AKS Cluster via Azure Portal

This guide walks you through creating an Azure Kubernetes Service (AKS) cluster using the Azure Portal—the easiest and most visual method. No command line or VS Code required!

---

## 1. Open the Azure Portal
- Go to [https://portal.azure.com](https://portal.azure.com)
- Log in with your Azure account.

---

## 2. Create a Resource Group (if you don’t have one already)
A resource group is a container for your Azure resources.

1. In the left sidebar, click **Resource groups**.
2. Click **+ Create** at the top.
3. Fill out:
   - **Subscription**: (choose your subscription)
   - **Resource group name**: e.g., `mas-demo-rg`
   - **Region**: (choose a region close to you, e.g., `East US`)
4. Click **Review + create**, then **Create**.

---

## 3. Create an AKS Cluster

1. In the Azure Portal search bar, type **Kubernetes services** and select it.
2. Click **+ Create** > **Add Kubernetes cluster**.
3. Fill out the **Basics** tab:
   - **Subscription**: (your subscription)
   - **Resource group**: select the one you created (`mas-demo-rg`)
   - **Kubernetes cluster name**: e.g., `mas-demo-aks`
   - **Region**: (same as your resource group)
   - **Availability zones**: (leave default for demo)
   - **Kubernetes version**: (use default or latest)
   - **Node size**: (default is fine for demos, e.g., Standard_DS2_v2)
   - **Node count**: 1 or 2 (default is fine for demo)
4. Click **Next: Node pools** (leave default).
5. Click **Next: Access**:
   - **Authentication method**: leave default (System-assigned managed identity)
   - **RBAC**: enabled (default)
   - **Local accounts**: enabled (default)
6. Click **Next: Networking** (leave defaults for demo).
7. Click **Next: Integrations**:
   - **Monitoring**: Enable (default; creates Log Analytics workspace automatically)
8. Click **Next: Tags** (optional).
9. Click **Review + create**.
10. Review settings, then click **Create**.
*create Azure container registry and Azure monitor here is recommonded.
<img width="1416" height="562" alt="image" src="https://github.com/user-attachments/assets/b924db1f-f639-4039-aa57-c31ce3dea263" />
<img width="1040" height="774" alt="image" src="https://github.com/user-attachments/assets/0854228f-4027-40b8-b516-227fc48bcae8" />
<img width="926" height="480" alt="image" src="https://github.com/user-attachments/assets/39fc6c74-0e80-4955-85f4-74cec7ef0bcf" />


*Note: This will take a few minutes to deploy!*

---

## 4. Wait for Deployment to Complete

- You’ll see a progress bar.
- When finished, click **Go to resource** to open your AKS cluster overview.

---

## 5. (Optional) Install kubectl locally for advanced access

If you want to manage your cluster via command line later:
- Download/install [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- Use Azure Cloud Shell (built-in terminal in Azure Portal, upper right corner) for easy access—no local install needed.

---

## 6. Your AKS Cluster is Ready!

- You can now deploy containers/apps into this cluster.
- The next step will be building/pushing your MAS Docker image and deploying it to AKS.

---

**Now you are Ready for Step 2: Build and Deploy MAS to AKS!
