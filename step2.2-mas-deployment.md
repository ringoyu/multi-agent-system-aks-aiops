# Step 2.2 Deploy MAS to AKS – Secrets, Deployment, Verification

Deploying MAS to AKS, including environment secret creation, actual deployment, and verification steps.

---

## **E. Set Up Environment Variables as Kubernetes Secret**

### 1. Edit the `.env` file
- Copy `.env.example` to `.env`
- Fill in all required settings:
  - Azure OpenAI endpoint and key (if using Azure OpenAI)
  - Azure Monitor workspace ID/key
  - Any other required config from the MAS repo README

### 2. Create the Kubernetes secret
Open your terminal in the MAS repo folder and run:
```bash
kubectl create secret generic mas-env-secret --from-env-file=.env
```
- This secret provides your MAS pod with configuration securely.

---

## **F. Deploy MAS to AKS**

### 1. Ensure `kubectl` is connected to your AKS cluster
```bash
kubectl config get-contexts
kubectl get nodes
```
- You should see your AKS cluster and both node pools (agentpool and userpool).

### 2. Apply the manifests
```bash
kubectl apply -f mas-deployment.yaml
kubectl apply -f mas-service.yaml
```

### 3. Verify your MAS deployment
```bash
kubectl get pods
kubectl get svc mas-app-service
```
- When the pod status is `Running` and the service shows an `EXTERNAL-IP`, MAS is live and reachable.

### 4. (Optional) Confirm MAS is running on the user node pool
```bash
kubectl get pods -o wide
```
- The `NODE` column should show your MAS pod scheduled on the user pool.

---

## **G. Next Steps**

- MAS is now deployed and running in your AKS cluster, on the user node pool!
- The service is exposed via a public IP (see `EXTERNAL-IP` from `kubectl get svc`), which you’ll use for alerts/webhooks.

---

## **Troubleshooting Tips**

- If your pod is not running, check logs:
  ```bash
  kubectl logs deployment/mas-app
  ```
- If the service doesn't get an external IP, wait a few minutes—sometimes AKS takes a bit to provision.
- Make sure your `nodeSelector` value in `mas-deployment.yaml` matches your actual user node pool name (see Azure Portal → AKS → Node pools).

---

**Ready for Step 3 (set up alert and trigger MAS)?**
