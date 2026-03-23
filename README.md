## admin login

- username: admin
- password: admin
---
## Modify Pom.xml
- **Add these Dependencies**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```
---
## Modify application.properties
```
management.endpoints.web.exposure.include=health,info,prometheus
management.endpoint.prometheus.enabled=true
management.metrics.export.prometheus.enabled=true
management.endpoint.health.show-details=always
```
## Fetching Spring Application Metrix
- **Verify Metrix Are Fetching or Not**
- Open in browser: `http://<APP_SERVER_IP>:8085/actuator/prometheus`
---
- **Update Prometheus Config** 
```yml
- job_name: 'spring-boot-app'
  metrics_path: '/actuator/prometheus'
  static_configs:
    - targets: ['<APP_SERVER_IP>:8085']
```
---
## Prometheus DashBoard Operations
- **Test queries**
1. Check app status
```promql
up{job="spring-boot-app"}
```
2. Total HTTP requests
```promql
http_server_requests_seconds_count
```
3. Request rate 
```promql
rate(http_server_requests_seconds_count[1m])
```
4. JVM memory usage
```promql
jvm_memory_used_bytes
```
5. Thread count
```promql
jvm_threads_live_threads
```
---
## Add node monitor by using node-exporter
- After installing node-exporter it looks like this\
```yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'appserver'
    static_configs:
      - targets: ['13.233.167.180:9100']
  - job_name: 'spring-boot-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['13.233.167.180:8085']
```
---
## Grafana dashboard config for Spring App
- Need top Create new dashboard to observer by writing promql
---
# 🥇 1. Requests Per Second (Traffic)

```promql
sum(rate(http_server_requests_seconds_count{job="spring-boot-app", uri!="/actuator/prometheus"}[1m]))
```

👉 Shows:

* How many users are hitting your app right now
* Traffic spikes

---

# 🥈 2. Active Requests (Live Users)

```promql
sum(http_server_requests_active_seconds_count{job="spring-boot-app"})
```

👉 Shows:

* Real-time concurrent users
* Current load on your app

---

# 🥉 3. Average Response Time

```promql
sum(rate(http_server_requests_seconds_sum{job="spring-boot-app"}[1m]))
/
sum(rate(http_server_requests_seconds_count{job="spring-boot-app"}[1m]))
```

👉 Shows:

* How fast your APIs respond
* Detects slow system

---

# 🏅 4. Error Rate

```promql
sum(rate(http_server_requests_seconds_count{job="spring-boot-app", status!~"2.."}[1m]))
```

👉 Shows:

* Failures (4xx + 5xx)
* Critical for production monitoring

---

# 🎖 5. JVM Memory Usage (Heap)

```promql
sum(jvm_memory_used_bytes{area="heap", job="spring-boot-app"})
```

👉 Shows:

* Memory consumption
* Detect memory leaks

---

# 🔥 BONUS (Highly Recommended — Pro Level)

## 95th Percentile Latency

```promql
histogram_quantile(0.95,
  sum(rate(http_server_requests_seconds_bucket{job="spring-boot-app"}[5m])) by (le)
)
```
---

*Script and Project Designed and Developed by sak_shetty*