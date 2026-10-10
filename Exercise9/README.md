# Jenkins multi-stage Python pipeline

This repository contains a small Flask application and a Jenkins Declarative
Pipeline that builds, tests, deploys, starts, and checks it.

The exercise's original Flask 2.1.2 pin does not start on Python 3.14, so this
sample pins Flask 3.1.3 instead.

## Run locally

From the repository root, create a virtual environment, install the
application's dependencies, and run its unit tests. On Windows PowerShell:

```powershell
cd python-flask-app
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m unittest discover -s . -p "test_*.py" -v
```

On Linux or macOS, use `.venv/bin/python` in place of
`.\.venv\Scripts\python.exe`. Start the application with that Python executable
and `app.py`:

```powershell
.\.venv\Scripts\python.exe app.py
```

Then visit <http://127.0.0.1:5000/>. Set the `PORT` environment variable to
change the listening port.

## Run in Jenkins

Configure a Jenkins Pipeline job to use **Pipeline script from SCM**, select
Git, and enter this repository's URL. The Jenkins agent must have Git, Python
3, and the `venv` module available. Build Now runs the `Jenkinsfile` stages:

1. **Build** creates a virtual environment and installs requirements.
2. **Test** runs the unit tests.
3. **Deploy** copies the app to `python-app-deploy` in the Jenkins workspace.
4. **Run Application** starts the deployed app on port 5000.
5. **Test Application** checks the live HTTP response and the pipeline stops
   the app during post actions.

The `Jenkinsfile` uses Unix shell steps (`sh`), so the Jenkins agent must provide
a Linux/Unix-like shell, Git, Python 3, and the `venv` module. For a
containerized Jenkins setup, provide Python 3 and `python3-venv` on the agent
(or use a Python-enabled agent image); installing packages interactively inside
the Jenkins controller is not required.

## Screenshots

The screenshots below document the Flask application, creating the Jenkins
Pipeline job, and its configuration.

### Flask application source

![Flask application source in VS Code](./Screenshot%202026-10-10%20153009.png)

### Creating the Jenkins Pipeline job

![Creating the Exercise9-Pipeline Jenkins job](./Screenshot%202026-10-10%20182039.png)

### Jenkins Pipeline job configuration

![Exercise9-Pipeline Jenkins configuration](./Screenshot%202026-10-10%20182110.png)
