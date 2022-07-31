# Load Balancing

## httpd.conf

```httpd.conf
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so
LoadModule slotmem_shm_module modules/mod_slotmem_shm.so


<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{BALANCER_WORKER_ROUTE}e\"" common
</IfModule>

ProxyHCExpr ok234 {%{REQUEST_STATUS} =~ /^[234]/}

<Proxy "balancer://markruler-cluster">
    BalancerMember "http://app1:5001" route=app1 hcmethod=HEAD hcexpr=ok234 hcinterval=2
    BalancerMember "http://app2:5002" route=app2 hcmethod=HEAD hcexpr=ok234 hcinterval=2
    BalancerMember "http://app3:5003" route=app3 hcmethod=HEAD hcexpr=ok234 hcinterval=2
</Proxy>

ProxyPass        "/" "balancer://markruler-cluster"
ProxyPassReverse "/" "balancer://markruler-cluster"
```

## 실행

```sh
docker-compose up
```

```sh
# header
curl localhost:8080 -I

# header + body
curl localhost:8080 -i
```

```sh
# BALANCER_WORKER_ROUTE 변수를 사용하면 선택된 route를 확인할 수 있다.
192.168.208.1 - - [18/Jul/2022:02:12:33 +0000] "GET / HTTP/1.1" 200 29 "app1"
192.168.208.1 - - [18/Jul/2022:02:12:34 +0000] "GET / HTTP/1.1" 200 29 "app2"
192.168.208.1 - - [18/Jul/2022:02:12:34 +0000] "GET / HTTP/1.1" 200 29 "app3"
```

### Health Check 테스트

```sh
docker rm -f app3
```

![Proxy Error](../../images/httpd/httpd-proxy-error.png)

*앱을 강제 종료하고 헬스체크하는 사이에 접근 시도할 경우 Proxy Error*

- TCP Proxy도 제공되지만 웹 서비스 사용자 입장에선 HTTP 응답이 중요하다.

## 인스턴스 접근

```sh
docker exec -it slb sh
```

## 참조

- [Apache Module mod_proxy](https://httpd.apache.org/docs/trunk/mod/mod_proxy.html)
  - [Apache Module mod_proxy_http](https://httpd.apache.org/docs/2.4/mod/mod_proxy_http.html)
  - [Apache Module mod_proxy_balancer](https://httpd.apache.org/docs/2.4/mod/mod_proxy_balancer.html)
  - Load balancer scheduler algorithms
    - [mod_lbmethod_byrequests](https://httpd.apache.org/docs/trunk/mod/mod_lbmethod_byrequests.html): Request Counting
    - [mod_lbmethod_bytraffic](https://httpd.apache.org/docs/trunk/mod/mod_lbmethod_bytraffic.html): Weighted Traffic Counting
    - [mod_lbmethod_bybusyness](https://httpd.apache.org/docs/trunk/mod/mod_lbmethod_bybusyness.html): Pending Request Counting
    - [mod_lbmethod_heartbeat](https://httpd.apache.org/docs/trunk/mod/mod_lbmethod_heartbeat.html): Heartbeat Traffic Counting
  - [mod_proxy_hcheck](https://httpd.apache.org/docs/2.4/mod/mod_proxy_hcheck.html)
    - [mod_watchdog](https://httpd.apache.org/docs/2.4/mod/mod_watchdog.html)
- [mod_slotmem_shm](https://httpd.apache.org/docs/trunk/mod/mod_slotmem_shm.html) : Slot-based shared memory provider
