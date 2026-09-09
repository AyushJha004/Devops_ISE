# Exercise 2 – Flask App on Kubernetes

Deploying a simple **Flask** web application to a local **Kubernetes** cluster using **Docker** and **Minikube**.

## Project Structure
```
Exercise2/
├── app.py                  # Flask application
├── dockerfile              # Docker image definition
├── flask-deployment.yaml   # Kubernetes Deployment + Service
└── README.md
```

## How It Works

1. A minimal Flask app is built into a Docker image
2. The image is loaded into Minikube's local registry
3. Kubernetes deploys the app as a pod and exposes it via a NodePort service on port `15000`

## Steps to Run

**1. Build the Docker image inside Minikube**
```bash
eval $(minikube docker-env)
docker build -t flask-app:latest .
```

**2. Apply the Kubernetes deployment**
```bash
kubectl apply -f flask-deployment.yaml
```

**3. Access the app**
```bash
minikube service flask-app-service
```

## Screenshots

### Deployment Running
![Deployment](https://github.com/AyushJha004/Devops_ISE/blob/main/Exercise2/Screenshot%202026-09-09%20111140.png?raw=true)

### App in Browser
![App in Browser](https://github.com/AyushJha004/Devops_ISE/blob/main/Exercise2/Screenshot%202026-09-09%20112851.png?raw=true)

## Tech Stack
- [Flask](https://flask.palletsprojects.com/) — Python web framework
- [Docker](https://www.docker.com/) — containerisation
- [Kubernetes](https://kubernetes.io/) — container orchestration
- [Minikube](https://minikube.sigs.k8s.io/) — local Kubernetes cluster
