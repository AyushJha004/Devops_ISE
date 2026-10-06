# Exercise 5: Docker Security with AppArmor and Python

## Objective
Secure Docker containers using AppArmor profiles with Python for enforcement. Apply AppArmor profiles using the Docker SDK and test restricted actions within the container.

## Files
- `app.py` - Flask web application
- `Dockerfile` - Container image definition
- `my-apparmor-profile` - AppArmor profile restricting sensitive access
- `apply_apparmor.py` - Docker SDK script to build and run container with AppArmor
- `test_restricted_actions.py` - Docker SDK script to test restricted actions

---

## Task 1 & 2: Flask App and Docker Image

`app.py` exposes a single `/` route on port 5000, bound to `0.0.0.0`.

```powershell
docker build -t flask-apparmor .
```

![Docker Build](Screenshot%202026-10-06%20135631.png)

---

## Task 3: AppArmor Profile

The profile (`my-apparmor-profile`) restricts:
- Read access to `/etc/**`
- Read/write access to `/var/**`
- Execution of binaries in `/bin/**` and `/usr/bin/**`
- `sys_admin` capability

It allows:
- Network binding on port 5000
- Full access to `/app/**`
- `net_bind_service` capability

### Load and apply on Linux/WSL2:
```bash
sudo cp my-apparmor-profile /etc/apparmor.d/my-apparmor-profile
sudo apparmor_parser -r /etc/apparmor.d/my-apparmor-profile
docker run --security-opt="apparmor=my-apparmor-profile" -p 5000:5000 flask-apparmor
```

---

## Task 4: Apply AppArmor via Docker SDK

```powershell
pip install docker
python apply_apparmor.py
```

![apply_apparmor.py Output](Screenshot%202026-10-06%20135639.png)

Output:
```
Running container with AppArmor profile...
Container started: b38f05f4985e

Inspecting container to verify AppArmor profile...
AppArmor profile applied: ['apparmor=my-apparmor-profile']

Stopping the container...
Container stopped.
```

---

## Task 5: Test Restricted Actions

```powershell
python test_restricted_actions.py
```

![test_restricted_actions.py Output](Screenshot%202026-10-06%20135655.png)

![Restricted Actions Detail](Screenshot%202026-10-06%20135730.png)

---

## Windows vs Linux Behavior

| Action | Windows (Docker Desktop) | Linux (AppArmor enforced) |
|--------|--------------------------|---------------------------|
| `cat /etc/passwd` | Exit Code 0 (allowed) | Exit Code 1 (denied) |
| `/bin/bash` | Exit Code 0 (allowed) | Exit Code 126 (denied) |

> AppArmor is a Linux kernel security module. On Windows, Docker Desktop accepts the `security_opt` flag via the API but does not enforce the profile — the container runs without restrictions. On Linux, the profile is loaded into the kernel via `apparmor_parser` and actively denies the restricted actions.

---

## Windows Note

To fully enforce AppArmor restrictions, run on:
- **WSL2** with Ubuntu and Docker configured to use the WSL2 backend
- A Linux VM or cloud instance (Ubuntu/Debian)
