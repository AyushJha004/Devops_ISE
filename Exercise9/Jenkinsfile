pipeline {
    agent any

    environment {
        PORT = '5000'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Creating a virtual environment and installing dependencies...'
                dir('python-flask-app') {
                    sh 'python3 -m venv .venv'
                    sh '.venv/bin/python -m pip install -r requirements.txt'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                dir('python-flask-app') {
                    sh '.venv/bin/python -m unittest discover -s . -p "test_*.py" -v'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Copying the application to the mock deployment directory...'
                sh '''
                    set -eu
                    mkdir -p "$WORKSPACE/python-app-deploy"
                    cp "$WORKSPACE/python-flask-app/app.py" "$WORKSPACE/python-app-deploy/"
                    cp "$WORKSPACE/python-flask-app/requirements.txt" "$WORKSPACE/python-app-deploy/"
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Starting the deployed application...'
                sh '''
                    set -eu
                    nohup "$WORKSPACE/python-flask-app/.venv/bin/python" \
                        "$WORKSPACE/python-app-deploy/app.py" \
                        > "$WORKSPACE/python-app-deploy/app.log" 2>&1 &
                    echo $! > "$WORKSPACE/python-app-deploy/app.pid"
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Checking the running application over HTTP...'
                dir('python-flask-app') {
                    sh '''
                        .venv/bin/python -c '
import os
import time
from urllib.request import urlopen

url = "http://127.0.0.1:" + os.environ["PORT"] + "/"
deadline = time.monotonic() + 15
last_error = None
while time.monotonic() < deadline:
    try:
        with urlopen(url, timeout=1) as response:
            body = response.read().decode("utf-8")
            if response.status != 200 or body != "Hello, Jenkins Multi-Stage Pipeline!":
                raise AssertionError(f"Unexpected response: {response.status} {body!r}")
            print(f"Application responded successfully: {body}")
            break
    except OSError as error:
        last_error = error
        time.sleep(0.25)
else:
    raise RuntimeError(f"Application did not become ready at {url}: {last_error!r}")
'
                    '''
                }
            }
        }
    }

    post {
        always {
            sh '''
                if [ -f "$WORKSPACE/python-app-deploy/app.pid" ]; then
                    app_pid=$(cat "$WORKSPACE/python-app-deploy/app.pid")
                    if kill -0 "$app_pid" 2>/dev/null; then
                        kill "$app_pid"
                    fi
                fi
            '''
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
