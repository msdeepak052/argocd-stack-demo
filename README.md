# Argo CD App-of-Apps Project

## **1️⃣ Prerequisites**

* Linux / Mac / Windows with WSL2
* **kubectl** installed
* **Helm** installed (v3+)
* **Git** installed

---

## **2️⃣ Install Helm**

```bash
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

---

## **3️⃣ Minikube Installation (Local Kubernetes Cluster)**

```bash
# Download Minikube (Linux example)
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start Minikube with 4 CPU and 8GB memory
minikube start --cpus=4 --memory=8192
```

Verify:

```bash
kubectl get nodes
```

✅ Should show `minikube` node in `Ready` state.

---

## **4️⃣ Install Argo CD via Helm**

```bash
# Add Argo Helm repo
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

# Create namespace
kubectl create namespace argocd

# Install Argo CD
helm install argocd argo/argo-cd -n argocd

# Verify pods
kubectl get pods -n argocd
```

---

### **Step 5: Access Argo CD UI**

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

* Open browser: **[https://localhost:8080](https://localhost:8080)**
* **Username:** `admin`
* **Password:**

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

---

## **5️⃣ GitHub Repository Setup**

1. Create a repository: `argocd-stack-demo`
2. Clone it locally:

```bash
git clone https://github.com/msdeepak052/argocd-stack-demo.git
cd argocd-stack-demo
```

3. Folder structure:

```
argocd-stack-demo/
│
├── apps/
│   ├── dev/
│   │   ├── frontend.yaml
│   │   ├── backend.yaml
│   │   └── db.yaml
│   ├── prod/
│   │   ├── frontend.yaml
│   │   ├── backend.yaml
│   │   └── db.yaml
│   └── app-of-apps.yaml
│
├── manifests/
│   ├── frontend/
│   │   └── deployment.yaml
│   ├── backend/
│   │   └── deployment.yaml
│   └── db/
│       └── deployment.yaml
│
└── README.md
```


## **9️⃣ Push All Changes to GitHub**

```bash
git add .
git commit -m "Add App-of-Apps setup for dev and prod"
git push origin main
```

---

## **10️⃣ Apply App-of-Apps in Cluster**

```bash
kubectl create namespace prod

kubectl create namespace dev

kubectl apply -f apps/app-of-apps.yaml -n argocd
```

---

## **11️⃣ Verify in Argo CD UI**

* Open **[https://localhost:8080](https://localhost:8080)**
* Login: `admin / <password>`
* You should see:

  * `stack-root` → dev environment apps
  * `stack-prod` → prod environment apps
* Check that child apps are **Synced** and **Healthy**.

<img width="1913" height="1080" alt="image" src="https://github.com/user-attachments/assets/d0870656-def7-4329-9a8b-b334dc87f678" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/36244234-ec75-4393-aec1-fad98ff029c1" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/af49bb56-fcbd-43ca-af3b-e0fb8e656d49" />

<img width="1918" height="1080" alt="image" src="https://github.com/user-attachments/assets/de54e84c-f6cf-4f0d-a4e3-3fbdd7e37871" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9b7d2957-c208-437c-8d4f-e0421db32a4e" />

---

## **12️⃣ Verify in Kubernetes**

```bash
# Dev namespace
kubectl get all -n dev

# Prod namespace
kubectl get all -n prod
```

<img width="926" height="493" alt="image" src="https://github.com/user-attachments/assets/804bcf85-2f10-414e-8de2-00a535b5a909" />

<img width="765" height="385" alt="image" src="https://github.com/user-attachments/assets/55e0739f-453b-4a65-bcba-cfdd7149c918" />

✅ You should see pods for **frontend, backend, db** in both namespaces.

---

## **13️⃣ Next Steps / Enhancements**

* Add **Ingress** for accessing frontend/backend
* Use **Kustomize overlays** for environment-specific config
* Integrate **CI/CD pipeline** → push changes to GitHub → Argo CD auto-syncs
* Extend to **multiple clusters** for production GitOps

---





