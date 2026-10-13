# Exercise 10: Multi-node Kubernetes Services

This exercise deploys two Flask applications to a three-node Minikube cluster:

- **Product Catalog** serves sample products at `GET /products` with two
  replicas.
- **Shopping Cart** serves the cart at `GET /cart` and adds items with
  `POST /cart`, with three replicas.

Both Deployments require replicas to run on different nodes using pod
anti-affinity. The shopping-cart Deployment uses `maxSurge: 0` and
`maxUnavailable: 1` so a rolling update can proceed on a three-node cluster
without trying to schedule a fourth pod.

## Prerequisites

- Docker Desktop running with the Linux container engine.
- Minikube installed.
- Enough Docker Desktop memory for the Kubernetes nodes and application pods.

Run the commands below in **PowerShell**. Open separate VS Code terminals where
indicated, and leave the port-forward terminals running while accessing the
services.

## Build the application images

From the repository root:

```powershell
Set-Location .\Exercise10
docker build -f Dockerfile.product -t product-catalog:latest .
docker build -f Dockerfile.shopping -t shopping-cart:latest .
```

## Start the cluster and deploy

For a first-time setup, create the three-node cluster:

```powershell
minikube start --nodes=3 -p devops-multinode --driver=docker --memory=1800 --cpus=2
```

On later runs, start the existing profile instead:

```powershell
minikube start -p devops-multinode
```

Confirm the nodes are ready:

```powershell
minikube -p devops-multinode kubectl -- get nodes
```

Load the locally built images into Minikube and apply the Deployments and
Services:

```powershell
minikube -p devops-multinode image load product-catalog:latest
minikube -p devops-multinode image load shopping-cart:latest

minikube -p devops-multinode kubectl -- apply -f product_catalog_deployment.yaml -f product_catalog_service.yaml -f shopping_cart_deployment.yaml -f shopping_cart_service.yaml
```

Wait for both Deployments, then check replica placement:

```powershell
minikube -p devops-multinode kubectl -- rollout status deployment/product-catalog
minikube -p devops-multinode kubectl -- rollout status deployment/shopping-cart
minikube -p devops-multinode kubectl -- get pods -o wide
```

Expected result: two `product-catalog` pods on separate nodes and three
`shopping-cart` pods, one per node.

## Access the services

Use `kubectl port-forward` in two separate PowerShell terminals. Keep both
commands running while using the endpoints:

```powershell
minikube -p devops-multinode kubectl -- port-forward service/product-catalog-service 18081:80
```

```powershell
minikube -p devops-multinode kubectl -- port-forward service/shopping-cart-service 18082:80
```

Open these URLs in a browser:

- Product catalog: <http://127.0.0.1:18081/products>
- Shopping cart: <http://127.0.0.1:18082/cart>

If a local port is already in use, stop its existing port-forward with
**Ctrl+C**, or choose another local port in the command and URL.

To add an item to the cart from a third PowerShell terminal:

```powershell
Invoke-RestMethod -Method Post -Uri 'http://127.0.0.1:18082/cart' -ContentType 'application/json' -Body '{"id":1,"name":"Laptop","quantity":1}'
```

The cart is held in application memory, so its contents are not persistent and
may reset when its pod restarts.

## Screenshots

The screenshots below document the successful three-node deployment and
service responses.

### Nodes and application pods

![All three nodes Ready and all five application pods Running](./Screenshot%202026-10-13%20173116.png)

### Shopping cart endpoint

![Shopping cart endpoint returning an empty cart](./Screenshot%202026-10-13%20174005.png)

### Product catalog endpoint

![Product catalog endpoint returning the sample products](./Screenshot%202026-10-13%20174053.png)
