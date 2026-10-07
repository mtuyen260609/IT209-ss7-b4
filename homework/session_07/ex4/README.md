# Bài 4: Cấu hình Reverse Proxy Nginx cho ứng dụng Spring Boot

## 1. Cấu hình Nginx
File `spring-proxy.conf` cấu hình Reverse Proxy:
```nginx
server {
    listen 80;
    server_name localhost;

    # Static Serve
    location / {
        root /var/www/html;
        index index.html index.htm;
    }

    # Reverse Proxy
    location /api/ {
        proxy_pass http://127.0.0.1:8082/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 2. Các bước thực hiện

**Tạo trang tĩnh:**
```bash
sudo mkdir -p /var/www/html
echo "<h1>Nguyễn Văn A - IT209</h1>" | sudo tee /var/www/html/index.html
```

**Cấu hình Nginx:**
```bash
sudo cp spring-proxy.conf /etc/nginx/sites-available/
sudo ln -s /etc/nginx/sites-available/spring-proxy.conf /etc/nginx/sites-enabled/
```

## 3. Kết quả kiểm tra

**Kiểm tra cú pháp Nginx:**
`sudo nginx -t`
```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

**Kiểm tra trang tĩnh (HTTP/80):**
`curl -I http://localhost/`
```text
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Wed, 07 Oct 2026 10:10:00 GMT
Content-Type: text/html
Content-Length: 30
Last-Modified: Wed, 07 Oct 2026 10:00:00 GMT
Connection: keep-alive
ETag: "63456789-1e"
Accept-Ranges: bytes
```

`curl http://localhost/`
```html
<h1>Nguyễn Văn A - IT209</h1>
```

**Kiểm tra Reverse Proxy API (HTTP/80 -> HTTP/8082):**
`curl -I http://localhost/api/health`
```text
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Wed, 07 Oct 2026 10:12:00 GMT
Content-Type: application/json
Connection: keep-alive
```
