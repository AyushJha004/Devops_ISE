# Exercise 3: Scaling Flask App on Single Node using ReplicaSets

## Overview
Deploying a Flash Sale Flask app on Minikube using Kubernetes ReplicaSets to demonstrate scaling, self-healing, and pod distribution.

## Files
- `ex3-flash-sale.py` - Flask application with `/`, `/buy`, and `/health` endpoints
- `Dockerfile` - Container image definition using Python 3.11-slim and Gunicorn
- `flashsale-replicaset.yaml` - Kubernetes ReplicaSet (3 replicas) and NodePort Service

---

## Step 1: Build Docker Image and Load into Minikube

```cmd
docker build -t flashsale:1.0 .
minikube image load flashsale:1.0
```

![Build and Load Image](Screenshot%202026-10-06%20121134.png)

---

## Step 2: Apply ReplicaSet Configuration

```cmd
kubectl apply -f flashsale-replicaset.yaml
```

![Apply ReplicaSet](Screenshot%202026-10-06%20123102.png)

---

## Step 3: Verify Pods and ReplicaSet (3 Replicas)

```cmd
kubectl get pods
kubectl get rs
```

![Verify Pods and RS](Screenshot%202026-10-06%20123432.png)

---

## Step 4: Scale ReplicaSet to 5 Replicas

```cmd
kubectl scale rs flashsale-rs --replicas=5
kubectl get pods
```

![Scale to 5 Replicas](Screenshot%202026-10-06%20123705.png)

---

## Step 5: Delete a Pod (Self-Healing)

```cmd
kubectl delete pod <pod-name>
kubectl get pods
```

![Self-Healing](Screenshot%202026-10-06%20123804.png)

---

## Step 6: Access the App via Minikube Service

```cmd
minikube service flashsale-svc --url
```

![Service URL](Screenshot%202026-10-06%20124045.png)

---

## Step 7: Test Endpoints

```cmd
curl http://127.0.0.1:<port>/
curl http://127.0.0.1:<port>/buy?user=alice
curl http://127.0.0.1:<port>/health
```

![Testing Endpoints](Screenshot%202026-10-06%20124118.png)
