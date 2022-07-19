# Load Balancing

## 실행

```sh
docker-compose up
```

```sh
# header
curl localhost -I

# header + body
curl localhost -i
```

## Health Check 테스트

```sh
docker rm -f app2

curl localhost
```

```sh
HTTP/1.1 502 Bad Gateway
Date: Tue, 19 Jul 2022 01:10:22 GMT
Content-Length: 11
Content-Type: text/plain; charset=utf-8

Bad Gateway%
```

`app2` 라는 이름으로 재실행해도 연결하지 않는다.
Service Discovery가 없기 때문에 다시 찾지 못하는 것이다.
endpoint를 IP로 입력하면 헬스체크를 통해 자동으로 다시 연결할 수 있다.

```toml
[http.services]
[http.services.my-service.loadBalancer]
[http.services.my-service.loadBalancer.healthCheck]
path = "/health"
scheme = "http"
interval = "1s"
timeout = "2s"
[[http.services.my-service.loadBalancer.servers]]
url = "http://app1:5001"
[[http.services.my-service.loadBalancer.servers]]
url = "http://192.168.0.148:5002"
[[http.services.my-service.loadBalancer.servers]]
url = "http://192.168.0.148:5003"
```

```sh
docker run -d --rm -p 5002:5002 -e PORT=5002 --name app2 markruler/nodejs-hello-world
```

## 인스턴스 접근

```sh
docker exec -it slb sh
```
