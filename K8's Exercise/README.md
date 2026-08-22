# Kubernetes Exercise

A hands-on Kubernetes exercise using **Minikube** on a local machine, covering installation, configuration and running pods with an Nginx deployment.

## Steps Covered

### 1. Installing Chocolatey
Chocolatey is used as the Windows package manager to install Minikube and kubectl.

![Installing Choco](https://github.com/AyushJha004/Devops_ISE/blob/branch1/K8's%20Exercise/Installing%20Choco.png?raw=true)

### 2. Install Check
Verifying that all required tools (kubectl, Minikube) are correctly installed.

![Install Check](https://github.com/AyushJha004/Devops_ISE/blob/branch1/K8's%20Exercise/Install%20check.png?raw=true)

### 3. Starting Minikube
Initialising a local Kubernetes cluster using `minikube start`.

![Minikube Start](https://github.com/AyushJha004/Devops_ISE/blob/branch1/K8's%20Exercise/Minikube%20start.png?raw=true)

### 4. Configuration
Checking the cluster configuration and context with `kubectl config view`.

![Config](https://github.com/AyushJha004/Devops_ISE/blob/branch1/K8's%20Exercise/config.png?raw=true)

### 5. Running Pods
Deploying and listing running pods inside the cluster.

![Pods](https://github.com/AyushJha004/Devops_ISE/blob/branch1/K8's%20Exercise/Pods.png?raw=true)

### 6. Nginx on Desktop
Accessing the Nginx service through the Minikube tunnel in the browser.

![Nginx Desktop](https://github.com/AyushJha004/Devops_ISE/blob/branch1/K8's%20Exercise/Ngx%20desktop.png?raw=true)

## Tools Used
- [Minikube](https://minikube.sigs.k8s.io/) — local Kubernetes cluster
- [kubectl](https://kubernetes.io/docs/reference/kubectl/) — Kubernetes CLI
- [Chocolatey](https://chocolatey.org/) — Windows package manager
- Nginx — sample deployment to verify the cluster is working
