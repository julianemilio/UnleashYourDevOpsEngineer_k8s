# 🚀 Flask API Deployment with Kubernetes and Minikube

This repository contains a simple example of deploying a Python Flask REST API on Kubernetes, specifically using **Minikube** for local testing.

---

## 📁 Project Structure

```
my-k8s-app/
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml

```

---

## 🔧 Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- Docker

---

## 🧱 Kubernetes Basic Commands

```bash
kubectl get pods
kubectl get services
kubectl get deployments
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/sh

kubectl apply -f <file>.yaml
kubectl delete -f <file>.yaml

kubectl describe pod <pod-name>
kubectl get events --sort-by=.metadata.creationTimestamp

kubectl port-forward svc/<service-name> 8080:80
```

---

## 🧪 Minikube Deployment Steps

### 1. Start Minikube

```bash
minikube start
```

### 2. Enable Docker inside Minikube

```bash
eval $(minikube docker-env)
```

### 4. Apply Kubernetes manifests

```bash
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

### 5. Verify deployment

```bash
kubectl get pods
kubectl get services
kubectl get deployments
```

### 6. Access the app

```bash
minikube service hero-service
```

Or get the URL:

```bash
minikube service hero-service --url
```

### 7. Clean up

```bash
kubectl delete -f k8s/
minikube stop
```

---

## ☁️ AKS Deployment (rgUnleash)

### 1. Create the AKS Cluster

```bash
az login

# Create resource group (if not already created)
az group create --name rgUnleash --location eastus

# Add proveedor - studen account
az provider register --namespace Microsoft.ContainerService
az provider show --namespace Microsoft.ContainerService --query "registrationState"

# Create AKS cluster
az aks create   --resource-group rgUnleash   --name hero-aks-cluster   --node-count 1 --generate-ssh-keys --node-vm-size Standard_B2ms
```

### 2. Connect to the AKS Cluster

```bash
az aks get-credentials --resource-group rgUnleash --name hero-aks-cluster
```

### 5. Apply Kubernetes Manifests

```bash
kubectl apply -f k8s/
```

### 6. Access the Application

```bash
kubectl get svc hero-service
```

Copy the external IP and open in browser.

---

## 📦 Kubernetes YAML Files

### `k8s/configmap.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_NAME: "Flask in Kubernetes"
```

### `k8s/deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
        - name: flask-container
          image: myimage:v1
          ports:
            - containerPort: 8000
          envFrom:
            - configMapRef:
                name: app-config
```

### `k8s/service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  type: NodePort
  selector:
    app: flask-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
```

---

Happy coding! 🚀
