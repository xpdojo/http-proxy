# HAProxy

## Configuration

```sh
# 초기 구성 파일 없음
# docker run --rm haproxy:2.6.1 cat /usr/local/etc/haproxy/haproxy.cfg > haproxy.cfg.example
```

## HAProxy statistics report

- [Exploring the HAProxy Stats Page](https://www.haproxy.com/blog/exploring-the-haproxy-stats-page/)

```cfg
frontend markruler
  bind *:80
  default_backend apps
  stats uri /stats
  stats refresh 10s
```

- CentOS7에서 80번 포트를 바인딩하려면 에러가 발생한다.

```sh
[NOTICE]   (1) : haproxy version is 2.6.1-f6ca66d
[ALERT]    (1) : Binding [/usr/local/etc/haproxy/haproxy.cfg:29] for frontend proxy: cannot bind socket (Permission denied) for [0.0.0.0:80]
[ALERT]    (1) : [haproxy.main()] Some protocols failed to start their listeners! Exiting.
```

```yaml
# docker-compose.yaml
# 사용자를 root로 지정한다.
user: root
# 아래 권한을 줘도 똑같이 문제가 발생한다.
# https://hub.docker.com/_/haproxy
# sysctls:
#   - net.ipv4.ip_unprivileged_port_start=0
```

![HAProxy Stats](../images/haproxy/haproxy-stats.png)

*http://localhost:8080/stats*

## Reloading config

[Docker Hub 내용](https://hub.docker.com/_/haproxy)에 따르면 아래 명령어를 실행한다.

```sh
docker kill -s HUP my-haproxy
```

```sh
docker-compose kill -s HUP
```

## 참조

- [HAProxy Configuration Basics: Load Balance Your Servers](https://www.haproxy.com/blog/haproxy-configuration-basics-load-balance-your-servers/) - HAProxy
- [How to Enable Health Checks in HAProxy](https://www.haproxy.com/blog/how-to-enable-health-checks-in-haproxy/) - HAProxy
- [Monitoring HAProxy performance metrics](https://www.datadoghq.com/blog/monitoring-haproxy-performance-metrics/) - Datadog
- [Circuit Breaking in HAProxy](https://www.haproxy.com/blog/circuit-breaking-haproxy/) - HAProxy
- [HAProxy Layer 7 Retries and Chaos Engineering](https://www.haproxy.com/blog/haproxy-layer-7-retries-and-chaos-engineering/) - HAProxy
- [Logging](https://www.haproxy.com/documentation/hapee/2-5r1/observability/logging/)
  - [Log with Docker](https://www.haproxy.com/documentation/hapee/2-5r1/observability/logging/log-with-docker/)
  - [Setting Log Levels](https://www.haproxy.com/documentation/hapee/2-5r1/observability/logging/log-level-setting/)
  - [Log Format for Access Logs](https://www.haproxy.com/documentation/hapee/2-5r1/observability/logging/log-formats/)
- [How to Map Domain Names to Backend Server Pools with HAProxy](https://www.haproxy.com/blog/how-to-map-domain-names-to-backend-server-pools-with-haproxy/)
- [Introduction to HAProxy Logging](https://www.haproxy.com/blog/introduction-to-haproxy-logging/)
  - [mminks/haproxy-docker-logging](https://github.com/mminks/haproxy-docker-logging)
  - CentOS 7에서는 동작하는데 Fedora 35에서는 동작하지 않는다. (원인 모름)
    ```sh
    rsyslog startup failure, child did not respond within startup timeout
    ```
