# Exercise 4: Docker Networking with Multiple Containers

## Objective
Understand Docker networking concepts and configure a multi-container application.

## Architecture
```
        Browser / Host
               │
          localhost:5001
               │
               ▼
       ┌──────────────┐
       │ Flask :5001  │
       │    flask     │
       └──────┬───────┘
              │
       my-bridge-net
      ┌───────┴────────┐
      │                │
      ▼                ▼
┌────────────┐   ┌────────────┐
│ MySQL      │   │ Redis      │
│ mysql:3306 │   │ redis:6379 │
└────────────┘   └────────────┘
```

## Files
- `app.py` - Flask REST API with `/about` endpoint (binds to `0.0.0.0`)
- `requirements.txt` - Pinned Flask 2.0.1 + Werkzeug 2.0.3
- `Dockerfile` - Container image definition using Python 3.9-slim

---

## Part 1: Verify Docker

```powershell
docker --version
docker info
docker ps
```

![Verify Docker](Screenshot%202026-10-06%20131212.png)

---

## Part 2: Create Working Directory

```powershell
mkdir docker-networking-lab
cd docker-networking-lab
```

---

## Part 3: Create the Bridge Network

```powershell
docker network create --driver bridge my-bridge-net
docker network ls
```

![Network Created](Screenshot%202026-10-06%20131320.png)

---

## Part 4: Inspect the Network

```powershell
docker network inspect my-bridge-net
```

Look for `Name`, `Driver`, and `IPAM` subnet/gateway fields.

![Network Inspected](Screenshot%202026-10-06%20131333.png)

---

## Part 5–7: Application Files

`app.py` — Flask binds to `0.0.0.0` so it's reachable from outside the container:
```python
app.run(host='0.0.0.0', port=5001)
```

`requirements.txt` — Werkzeug pinned to avoid Flask 2.0.1 API incompatibility:
```
Flask==2.0.1
Werkzeug==2.0.3
```

---

## Part 8–9: Build and Test Flask Image

```powershell
docker build --no-cache -t flask-api .
docker run -d --name flask-test -p 5001:5001 flask-api
curl.exe http://localhost:5001/about
```

Expected:
```json
{"description": "This is a simple REST API built with Flask.", "name": "Simple REST API", "version": "1.0"}
```

> Use `curl.exe` in PowerShell — `curl` resolves to `Invoke-WebRequest` by default.

![Build and Test Flask](Screenshot%202026-10-06%20132432.png)

---

## Part 10–13: Launch All Three Containers

```powershell
docker run -d --name mysql --network my-bridge-net -e MYSQL_ROOT_PASSWORD=rootpass -e MYSQL_DATABASE=devopsdb mysql:latest
docker run -d --name redis --network my-bridge-net redis:latest
docker run -d --name flask --network my-bridge-net -p 5001:5001 flask-api
docker ps
```

![All Containers Running](Screenshot%202026-10-06%20132443.png)

---

## Part 14: Understanding -p 5001:5001

```
-p HOST_PORT:CONTAINER_PORT
```

Flask needs a published port for host access. MySQL and Redis do **not** need published ports — Flask reaches them internally via container names on `my-bridge-net`.

---

## Part 15–17: Test API and DNS Resolution

```powershell
curl.exe http://localhost:5001/about
docker exec flask getent hosts mysql
docker exec flask getent hosts redis
```

Docker's embedded DNS resolves container names — no hard-coded IPs needed.

![API and DNS Resolution](Screenshot%202026-10-06%20132456.png)

---

## Part 18–19: Test Redis and MySQL

```powershell
docker exec -it redis redis-cli ping
docker exec -it mysql mysql -uroot -prootpass -e "SHOW DATABASES;"
```

Expected: Redis returns `PONG`. MySQL shows `devopsdb`, `information_schema`, `mysql`, `performance_schema`, `sys`.

![Redis Test](Screenshot%202026-10-06%20132504.png)

![MySQL Test](Screenshot%202026-10-06%20132514.png)

---

## Part 21: Final Verification Checklist

| # | Check | Command |
|---|-------|---------|
| 1 | Docker works | `docker info` |
| 2 | Network created | `docker network ls` |
| 3 | Network inspected | `docker network inspect my-bridge-net` |
| 4 | Flask image built | `docker images` |
| 5 | Three containers running | `docker ps` |
| 6 | All on same network | `docker network inspect my-bridge-net` |
| 7 | Flask exposed to host | `curl.exe http://localhost:5001/about` |
| 8 | MySQL DNS resolution | `docker exec flask getent hosts mysql` |
| 9 | Redis DNS resolution | `docker exec flask getent hosts redis` |
| 10 | Cleanup complete | `docker network ls` |

## Cleanup

```powershell
docker stop mysql redis flask
docker rm mysql redis flask
docker network rm my-bridge-net
docker rmi flask-api
```

---

## Reference
[Detailed Lab Guide](https://github.com/SunagP/DevOps-Lab/blob/main/Exercises/4-Docker-Networking.md)
