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
---
*Script and Project Designed and Developed by sak_shetty*