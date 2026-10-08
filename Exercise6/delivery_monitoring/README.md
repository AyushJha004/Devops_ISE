# Exercise 6: Real-Time Operations Monitoring and Alerting

This exercise builds a small quick-commerce delivery simulator and monitors it
with Prometheus and Grafana. Docker Compose starts the application, Prometheus,
and Grafana together. The Grafana data source and dashboard are provisioned
automatically, and Prometheus evaluates alerts for pending deliveries and
delivery time.

## Architecture

```text
Delivery metrics app (:8000/metrics)
              |
              | scraped every 5 seconds
              v
Prometheus (:9090) <--- Grafana (:3000)
       |
       +-- evaluates delivery alert rules
```

The services communicate over the private Docker Compose network. Prometheus
scrapes `delivery-metrics:8000`, and Grafana queries `http://prometheus:9090`.
These service names work inside Compose; no host IP or host networking
configuration is needed.

## Requirements

- Docker Desktop with Docker Compose v2, running and ready.
- Host ports `3000`, `8000`, and `9090` available.
- Jenkins is optional and only required for the pipeline exercise.

## Start the stack

In PowerShell, change to this directory and build/start the services:

```powershell
cd "D:\Uni\4th Year\Devops\Exercise6\delivery_monitoring"
docker compose up --build -d
docker compose ps
```

The first start downloads the Python, Prometheus, and Grafana images. Wait for
all three containers to be running; the metrics app has a health check, and
Prometheus waits for it before starting.

| Service | URL | What to check |
| --- | --- | --- |
| Application metrics | <http://localhost:8000/metrics> | Prometheus text metrics, including the delivery metrics below |
| Prometheus | <http://localhost:9090> | Query metrics, check targets under **Status > Targets**, and view alerts |
| Grafana | <http://localhost:3000> | Open the provisioned **Delivery Monitoring** dashboard |

Grafana's initial credentials are `admin` / `admin`. Change the default
password when prompted. The Prometheus data source is named **Prometheus**,
uses the internal URL `http://prometheus:9090`, and is the default data source.
It is provisioned from configuration, so edit its file rather than the Grafana
UI if you need to change it.

### Screenshots

The data source settings show the provisioned Prometheus connection:

![Grafana Prometheus data source configuration](../Screenshot%202026-10-08%20215854.png)

The dashboard displays all four delivery metrics with live data:

![Grafana Delivery Monitoring dashboard](../Screenshot%202026-10-08%20220340.png)

Prometheus target health confirms both the delivery service and Prometheus
scrape targets are up:

![Prometheus scrape targets healthy](../Screenshot%202026-10-08%20221931.png)

## Metrics and dashboard

The Python application generates a new sample every second. Prometheus scrapes
it every five seconds. The dashboard's four time-series panels display:

| Metric | Type | Description |
| --- | --- | --- |
| `total_deliveries` | Gauge | Current simulated total (pending + on-the-way + delivered) |
| `pending_deliveries` | Gauge | Current pending delivery count |
| `on_the_way_deliveries` | Gauge | Current deliveries on the way |
| `average_delivery_time_sum` and `average_delivery_time_count` | Summary | Cumulative observed delivery-time sum and sample count |

The average delivery time panel and PromQL expression use
`average_delivery_time_sum / average_delivery_time_count`. This is the average
over observations since the application started; restarting the app resets
the Summary observations.

The simulator chooses pending deliveries from 10 to 20, on-the-way deliveries
from 5 to 20, delivered orders from 30 to 70, and a delivery time from 15 to
45 seconds.

Useful Prometheus queries:

```promql
total_deliveries
pending_deliveries
on_the_way_deliveries
average_delivery_time_sum / average_delivery_time_count
```

## Alerts

Prometheus evaluates the rules in `alert_rules.yml` every five seconds. An
alert must remain above its threshold for 15 seconds before it fires.

| Alert | Condition | Severity |
| --- | --- | --- |
| `HighPendingDeliveries` | `pending_deliveries > 10` for 15 seconds | warning |
| `HighAverageDeliveryTime` | Average delivery time greater than 30 seconds for 15 seconds | critical |

Because the simulator intentionally generates pending counts above 10 most of
the time, `HighPendingDeliveries` is expected to fire during normal simulation.
The average-time alert depends on the cumulative mean and may fire as that mean
changes.

## Configuration and files

```text
delivery_monitoring/
├── delivery_metrics.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── prometheus.yml
├── alert_rules.yml
├── Jenkinsfile
└── grafana/
    ├── dashboards/delivery-monitoring.json
    └── provisioning/
        ├── dashboards/dashboards.yml
        └── datasources/prometheus.yml
```

- `delivery_metrics.py` simulates deliveries and exposes `/metrics`.
- `docker-compose.yml` builds the app image and runs all services.
- `prometheus.yml` defines scrape targets and alert-rule loading.
- `alert_rules.yml` defines the delivery alerts.
- `grafana/provisioning/` automatically configures the data source and
  dashboard provider.
- `grafana/dashboards/delivery-monitoring.json` defines the four-panel
  dashboard.
- `Jenkinsfile` validates, builds, and deploys the Compose stack.

## Verify and troubleshoot

Check the metrics endpoint and current container state:

```powershell
curl.exe http://localhost:8000/metrics
docker compose ps
docker compose logs --tail 100
```

In Prometheus, open **Status > Targets** and confirm that both
`delivery_service` and `prometheus` are **UP**. Check the **Alerts** page to
see alert state and firing alerts. In Grafana, open the **Delivery
Monitoring** dashboard and confirm all four panels contain data.

To follow service logs while the stack runs:

```powershell
docker compose logs -f
```

Stop the services but retain Grafana's saved data:

```powershell
docker compose down
```

To also delete the persisted Grafana volume, use
`docker compose down --volumes`. This permanently removes Grafana's saved
state, including any dashboard or settings changes made in the UI.

## Jenkins pipeline

The `Jenkinsfile` can be used by a Jenkins Pipeline job. If the repository
root is the parent `Exercise6` directory, set **Script Path** to
`delivery_monitoring/Jenkinsfile`; if `delivery_monitoring` itself is the
repository root, use `Jenkinsfile`. The pipeline detects either checkout
layout, validates Compose configuration, builds the metrics image, and runs
`docker compose up --detach`.

The Jenkins agent must be able to run Docker Engine and the Docker Compose
plugin. The target Docker daemon must be able to pull the required images and
bind ports `3000`, `8000`, and `9090`.
