# Load Balancing

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

## Nginx 인스턴스 접근

```sh
docker exec -it slb sh
```

### config 확인

```sh
cd /etc/nginx/conf.d
```

### log 확인

```sh
tail -f /var/log/nginx/access.log
```

## 참조

- [Choosing an NGINX Plus Load‑Balancing Technique](https://www.nginx.com/blog/choosing-nginx-plus-load-balancing-techniques/)
- [Logging Traffic Between NGINX and Upstream Servers at CDN77](https://www.nginx.com/blog/logging-upstream-nginx-traffic-cdn77/)
- [ngx_http_upstream_module](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#var_upstream_addr)
  - Dynamically configurable group with periodic health checks is available as part of our **commercial subscription**.
  - LB 사용 시 Acitve 헬스체크는 NGINX Plus 가입해야 사용할 수 있음.
  - [NGINX Plus Feature: Load Balancing](https://www.nginx.com/products/nginx/load-balancing)
- [HTTP Health Checks](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-health-check/) - NGINX
  - Passive health check는 Open Source도 사용할 수 있다.

    ```nginx
    upstream backend {
      server backend1.example.com;
      server backend2.example.com max_fails=3 fail_timeout=30s; # Passive health check
    }
    ```

  - 위 설정은 upstream 서버에 30초(`fail_timeout`)동안 3번(`max_fails`) 요청을 보내지 못하거나 응답을 받을 수 없다면 30초(`fail_timeout`)동안 사용할 수 없는 서버라고 표시한다.
