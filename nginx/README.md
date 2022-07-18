# Nginx

- Reverse proxy
- Load balancer
- Mail proxy
- HTTP cache

## Configuration

```sh
# 초기 구성 파일을 받아서 설정
docker run --rm nginx:1.23.0 cat /etc/nginx/nginx.conf > nginx.conf.example
```

## Testing config

```sh
nginx -t
```

## 참조

- [NGINX vs. Apache: Our View of a Decade-Old Question](https://www.nginx.com/blog/nginx-vs-apache-our-view/)
- [Inside NGINX: How We Designed for Performance & Scale](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)
- [Dynamic Modules Development](https://youtu.be/KinJfCQ-WzY) - Ruslan Ermilov, NGINX, Inc.
