# Exercise 7: Introduction to Jenkins

Jenkins is an open-source automation server for building, testing, and deploying software. It helps teams automate repetitive tasks and implement continuous integration and continuous delivery (CI/CD). Jobs, builds, pipelines, plugins, and nodes are some of its core concepts.

## Getting Started with Jenkins

### Prerequisites

- Docker Desktop (or another Docker Engine) installed and running.
- Ports **8080** and **50000** available on the host.

### 1. Start Jenkins with Docker

Run this command in PowerShell or a terminal:

```powershell
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

The first run downloads the `jenkins/jenkins:lts` image. Port **8080** serves the Jenkins web interface; port **50000** is available for inbound agent connections.

Check that the container is running:

```powershell
docker ps -a --filter name=jenkins
```

> If a container named `jenkins` already exists, `docker run` will fail because container names must be unique. Start the existing container with `docker start jenkins`, or remove it with `docker rm -f jenkins` if you no longer need it, then run the command above.

### 2. Retrieve the initial administrator password

Before completing the setup wizard, retrieve the initial unlock password with:

```powershell
docker exec jenkins sh -c "cat /var/jenkins_home/secrets/initialAdminPassword"
```

Enter the password in the Jenkins setup wizard at [http://localhost:8080/](http://localhost:8080/). Keep the password private; do not commit it to the repository. Follow the wizard to install plugins and create an administrator account.

### 3. Open Jenkins

Visit [http://localhost:8080/](http://localhost:8080/) to access the Jenkins dashboard.

## Screenshots

### Jenkins setup wizard installing plugins

![Jenkins setup wizard installing plugins](./Screenshot%202026-10-10%20133730.png)

### Jenkins setup completed

![Jenkins setup completed](./Screenshot%202026-10-10%20133738.png)

### Jenkins dashboard

![Jenkins dashboard](./Screenshot%202026-10-10%20133231.png)
