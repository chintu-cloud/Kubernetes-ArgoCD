## 🏗️ Architecture

<img width="1310" height="583" alt="argocd" src="https://github.com/user-attachments/assets/e414408e-ce7e-4ab9-86d5-9ebeaa4c232f" />



Below is a **stylish, clean, professional `README.md`** with icons, sections, code blocks, and outputs.
You can **directly paste this into GitHub** — nothing is missing.

---
## 🖥️ Lunch EC2 - 
```
     name : client
     instance type : t3.micro
     networking : default
     security group : default 
```     
---
## 🚀 Connect EC2 -
```
    Attach IAM role -
        name : EC2-ECR
        policy : Adminstration access 
```       
---
## 📌 Architecture Overview

```
GitHub Repo  ───▶  ArgoCD  ───▶  Kubernetes (EKS)
                    │
               Auto Sync
               Self Heal
```

---

## 🧱 Prerequisites

✔ EKS Cluster running
✔ kubectl configured
✔ Internet access from cluster
✔ GitHub repository with Kubernetes manifests

---
# 📘 Kubernetes on AWS EKS – Setup Guide

This README documents the steps to install `kubectl`, configure AWS CLI, connect to an EKS cluster, and verify nodes.

---

## 🔧 Step 1: Install `kubectl`


```bash
# Download the latest stable release:
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Provide execution permission:
chmod +x kubectl

# Move binary to system path:
mv kubectl /usr/local/bin/kubectl


# Verify installation:
kubectl version --client
```
---

Output:
```
Client Version: v1.34.3
Kustomize Version: v5.7.1
```

---

## 🔧 Step 2: Configure AWS CLI

Run:
```bash
aws configure
```

Provide credentials:
```
AWS Access Key ID [None]: <YOUR_AWS_ACCESS_KEY>
AWS Secret Access Key [None]: <YOUR_AWS_SECRET_KEY>
Default region name [None]: us-east-1
Default output format [None]: json
```

---

## 🔧 Step 3: Update kubeconfig for EKS

Connect to your cluster:
```bash
aws eks update-kubeconfig --region us-east-1 --name cloud
```

Output:
```
Added new context arn:aws:eks:us-east-1:<ACCOUNT_ID>:cluster/cloud to /root/.kube/config
```

---

## 🔧 Step 4: Verify Cluster Nodes

Check nodes:
```bash
kubectl get nodes
```

Output:
```
NAME                           STATUS   ROLES    AGE     VERSION
ip-172-31-74-99.ec2.internal   Ready    <none>   6m40s   v1.34.2-eks-ecaa3a6
ip-172-31-8-15.ec2.internal    Ready    <none>   6m37s   v1.34.2-eks-ecaa3a6
```

---


## 📁 Step 1: Create ArgoCD Namespace

```bash
kubectl create namespace argocd
```

**Output**

```text
namespace/argocd created
```

---

## ⚙️ Step 2: Install ArgoCD

```bash
kubectl apply -n argocd -f \
https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**Output (partial)**

```text
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller created
service/argocd-server created
```

---

## 🔍 Step 3: Verify ArgoCD Components

```bash
kubectl get all -n argocd
```

**Output**

```text
pod/argocd-server-9dc66fd74-f884l       1/1   Running
pod/argocd-repo-server-6c857c79ff       1/1   Running
pod/argocd-application-controller-0     1/1   Running
```

---

## 🌐 Step 4: Expose ArgoCD Using NodePort

```bash
kubectl edit svc argocd-server -n argocd
```

Change:

```yaml
spec:
  type: NodePort
```

---

## 🔢 Step 5: Get NodePort

```bash
kubectl get svc -n argocd
```

**Output**

```text
argocd-server   NodePort   10.100.101.35   80:30829/TCP,443:30915/TCP
```

---

## 🖥️ Step 6: Get Node Public IP

```bash
kubectl get nodes -o wide
```

**Output**

```text
ip-172-31-16-18.ec2.internal   Ready   54.147.131.253
```

---

## 🔐 Step 7: Access ArgoCD UI

Open browser:

```text
http://54.147.131.253:30829
```

➡ **Advanced → Continue (unsafe)**
<img width="1602" height="839" alt="Screenshot 2025-12-18 140835" src="https://github.com/user-attachments/assets/7fc940bb-d1a4-4531-ada2-262381c99204" />

---

## 🔑 Step 8: Get Admin Password

```bash
kubectl get secret argocd-initial-admin-secret \
-n argocd -o yaml
```

**Output**

```yaml
password: RHp2Vjk2dEZLSVVxUGN0LQ==
```

Decode:

```bash
echo "RHp2Vjk2dEZLSVVxUGN0LQ==" | base64 --decode
```

**Output**

```text
DzvV96tFKIUqPct-
```

---

## 🔓 Step 9: Login to ArgoCD

| Field    | Value              |
| -------- | ------------------ |
| Username | `admin`            |
| Password | `DzvV96tFKIUqPct-` |

---
<img width="1910" height="958" alt="Screenshot 2025-12-18 140949" src="https://github.com/user-attachments/assets/b50e6a2f-ba31-4157-8d75-3f1d18b6476e" />

## 📦 Step 10: Create Application Namespace

```bash
kubectl create namespace cloud
```

**Output**

```text
namespace/cloud created
```

---

## 🆕 Step 11: Create Application in ArgoCD

Click **➕ New App**

### 🔹 General

```text
App Name: chandan
Project: default
Sync Policy: Automatic
```
<img width="1420" height="534" alt="Screenshot 2025-12-18 141601" src="https://github.com/user-attachments/assets/6de802b7-8dc0-49c0-8bb3-2a1c5a5b3ce4" />


### 🔹 Source

```text
Repo URL: https://github.com/chintu-cloud/Kubernetes-ArgoCD.git
Revision: HEAD
Path: ArgoCD-project
```
<img width="1416" height="441" alt="Screenshot 2025-12-18 141537" src="https://github.com/user-attachments/assets/fe4f8a90-0fcf-47bc-a52e-0742bf1d4866" />

### 🔹 Destination

```text
Cluster: https://kubernetes.default.svc
Namespace: cloud
```
<img width="1419" height="360" alt="Screenshot 2025-12-18 141513" src="https://github.com/user-attachments/assets/15d01482-959d-4a3c-b932-0f7c83085aea" />

Click **Create**

---

## 📊 Step 12: Verify Initial Deployment

```bash
kubectl get pods -n cloud
```

**Output**

```text
nginx-deploy-xxxxx   1/1   Running
```
<img width="1916" height="948" alt="Screenshot 2025-12-18 141710" src="https://github.com/user-attachments/assets/731ae45c-700e-4b69-a0ef-377001a22937" />
<img width="1917" height="963" alt="Screenshot 2025-12-18 142005" src="https://github.com/user-attachments/assets/989b501b-a47b-4ab3-911b-6d90cca2096a" />

---

## 🔁 Step 13: GitOps Auto-Sync Demo

Edit `deploy.yml` in GitHub:

```yaml
replicas: 5
```

Commit changes.


---

## 🚀 Step 14: ArgoCD Auto Sync Result

```bash
kubectl get pods -n cloud
```

**Output**

```text
nginx-deploy-1   Running
nginx-deploy-2   Running
nginx-deploy-3   Running
nginx-deploy-4   Running
nginx-deploy-5   Running
```
<img width="1904" height="964" alt="Screenshot 2025-12-18 142244" src="https://github.com/user-attachments/assets/c32fc782-4620-4845-b825-ff32a0a90d21" />
<img width="1884" height="943" alt="Screenshot 2025-12-18 142316" src="https://github.com/user-attachments/assets/4dc505dc-da2f-46a2-8bb3-70fbb6726d38" />
<img width="1917" height="963" alt="Screenshot 2025-12-18 142408" src="https://github.com/user-attachments/assets/0267fcaa-2447-49d1-bc98-1a17c0e05c1d" />



---

## 🩺 Step 15: Self-Healing Demo

Delete a pod manually:

```bash
kubectl delete pod nginx-deploy-3 -n cloud
```

**Output**

```text
pod "nginx-deploy-3" deleted
```

Recheck:

```bash
kubectl get pods -n cloud
```

**Output**

```text
nginx-deploy-3   Running
```

✅ Pod recreated automatically by ArgoCD.
---
## 🆕 Step 16: Create Application in ArgoCD

Click **➕ New App**

### 🔹 General

```text
App Name: chandan
Project: default
Sync Policy: Automatic
```


### 🔹 Source

```text
Repo URL: https://github.com/chintu-cloud/Kubernetes-ArgoCD.git
Revision: HEAD
Path: hotsatr
```

### 🔹 Destination

```text
Cluster: https://kubernetes.default.svc
Namespace: cloud
```
<img width="1521" height="804" alt="image" src="https://github.com/user-attachments/assets/545b81fe-1a8b-4818-9461-bb2dd4f8d2e7" />

---
## 🏁 Total Applications
<img width="1912" height="964" alt="Screenshot 2025-12-18 145146" src="https://github.com/user-attachments/assets/9ed2fcc7-3d27-4c0f-803b-e76f300deaaa" />


---
## 🎯 Key Features Demonstrated

✔ GitOps deployment
✔ Auto synchronization
✔ Continuous reconciliation
✔ Self-healing workloads

---

## 🏁 Conclusion

ArgoCD continuously monitors Git and ensures the Kubernetes cluster **always matches the desired state**.

> **Git is the source of truth. Kubernetes obeys Git.**

---

⭐ **If you like this project, give it a star on GitHub!**
