# Kubernetes Exercise

A hands-on Kubernetes exercise using **Minikube** on a local machine, covering installation, configuration and running pods with an Nginx deployment.

## Steps Covered

### 1. Installing Chocolatey
Chocolatey is used as the Windows package manager to install Minikube and kubectl.

![Installing Choco](Installing%20Choco.png)

### 2. Install Check
Verifying that all required tools (kubectl, Minikube) are correctly installed.

![Install Check](Install%20check.png)

### 3. Starting Minikube
Initialising a local Kubernetes cluster using `minikube start`.

![Minikube Start](Minikube%20start.png)

### 4. Configuration
Checking the cluster configuration and context with `kubectl config view`.

![Config](config.png)

### 5. Running Pods
Deploying and listing running pods inside the cluster.

![Pods](Pods.png)

### 6. Nginx on Desktop
Accessing the Nginx service through the Minikube tunnel in the browser.

![Nginx Desktop](Ngx%20desktop.png)

## Tools Used
- [Minikube](https://minikube.sigs.k8s.io/) — local Kubernetes cluster
- [kubectl](https://kubernetes.io/docs/reference/kubectl/) — Kubernetes CLI
- [Chocolatey](https://chocolatey.org/) — Windows package manager
- Nginx — sample deployment to verify the cluster is working
