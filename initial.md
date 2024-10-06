### Typical microservice configuration
Let's imagine that this project contains multiple microservices, but we will focus on 2. One backend and one frontend.
Their current configuration for dev and prod is below. Try to spot the difference :)

`payment-backend application config for dev`
```yaml
name: payment-backend

server:
  port: 80
  context: /api
  
payment-gateway: http://gateway-mock.local

database:
  type: Postgres
  pool-size: 10
  url: jdbc:postgres://10.10.10.10:5432/payments
  
monitoring:
  base-path: /monitoring
  endpoints: info, health, prometheus
```

`payment-frontend application config for dev`
```yaml
name: payment-frontend

server:
  port: 80
  minThreads: 10
  maxThreads: 100
  
payment-backend: 
  host: http://payment-backend.local
  path: /api
  timeoutMs: 180000

monitoring:
  base-path: /monitoring
  endpoints: info, health, ready, prometheus
```

`payment-backend deploy config for dev`
```yaml
image: "payment-backend:latest"

replicas: 1

ingress:
  host: http://payment-backend.local

probes:
  health: /monitoring/health
  ready: /monitoring/ready
```

`payment-frontend deploy config for dev`
```yaml
image: "payment-frontend:latest"

replicas: 1

ingress:
  host: http://payments.local

probes:
  health: /monitoring/health
  ready: /monitoring/ready
```

`payment-backend log config`
```xml
<configuration>
    <appender class="ch.qos.logback.core.FileAppender">
        <file>logs/payment-backend.log</file>
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %-5level %logger{15} %msg %n</pattern>
        </encoder>
    </appender>
</configuration>
```

`payment-frontend log config`
```xml
<configuration>
    <appender class="ch.qos.logback.core.FileAppender">
        <file>logs/payment-frontend.log</file>
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %-5level %logger{15} %msg %n</pattern>
        </encoder>
    </appender>
</configuration>
```

`payment-backend application config for prod`
```yaml
name: payment-backend

server:
  port: 80
  context: /api
  
payment-gateway: https://payment-gateway.com

database:
  type: Postgres
  pool-size: 50
  url: jdbc:postgres://20.20.20.20:5432/payments
  
monitoring:
  base-path: /monitoring
  endpoints: info, health, prometheus
```

`payment-frontend application config for prod`
```yaml
name: payment-frontend

server:
  port: 80
  minThreads: 10
  maxThreads: 100
  
payment-backend: 
  host: http://payment-backend.local
  path: /api
  timeoutMs: 180000

monitoring:
  base-path: /monitoring
  endpoints: info, health, ready, prometheus
```

`payment-backend deploy config for prod`
```yaml
image: "payment-backend:1.5"

replicas: 2

ingress:
  host: http://payment-backend.local

probes:
  health: /monitoring/health
  ready: /monitoring/ready
```

`payment-frontend deploy config for prod`
```yaml
image: "payment-frontend:2.1"

replicas: 3

ingress:
  host: https://payments.example.com

probes:
  health: /monitoring/health
  ready: /monitoring/ready
```

As you can see a lot of copy-paste, some environment specific values, almost same log templates and clear dependencies between services. 
Not let's see how Microconfig can help with all of this:

[See individual features in action](features.md)

[See all features working together](https://github.com/microconfig/microconfig-quickstart)