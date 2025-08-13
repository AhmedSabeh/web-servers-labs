# Lab 8 — Nginx Secure Site Configuration

## Overview
This lab focuses on configuring an **Nginx virtual host** with advanced security features, including:
- Security HTTP headers
- Rate limiting
- IP-based access control
- Custom port binding
- Verification with `curl`

---

## Objectives
1. Create a secure Nginx virtual host.
2. Apply industry-standard security headers.
3. Restrict access to sensitive paths using IP allowlisting.
4. Implement request rate limiting.
5. Verify functionality and security with `curl`.

---

## Configuration Details

-    **Virtual Host File**  
`/etc/nginx/sites-available/secure-site`

```
server {
    listen 8087;
    server_name secure.local;

    root /var/www/secure-site;
    index index.html;

    # Security Headers
    add_header X-Content-Type-Options "nosniff";
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

    # Rate Limiting
    limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;
    location / {
        limit_req zone=mylimit burst=20 nodelay;
        try_files $uri $uri/ =404;
    }

    # Restrict /admin to specific IPs
    location /admin {
        allow 192.168.1.0/24;
        deny all;
        try_files $uri $uri/ =404;
    }

    # Error Pages
    error_page 403 /error/403.html;
    error_page 404 /error/404.html;

    location /error/ {
        internal;
    }
}
```
-    # Steps Performed

1. Create Web Root & Test Files
```
sudo mkdir -p /var/www/secure-site/admin /var/www/secure-site/error
echo "Secure Site" | sudo tee /var/www/secure-site/index.html
echo "Admin Area" | sudo tee /var/www/secure-site/admin/index.html
echo "403 Forbidden" | sudo tee /var/www/secure-site/error/403.html
echo "404 Not Found" | sudo tee /var/www/secure-site/error/404.html
```
2. Enable Site & Test
```
sudo ln -s /etc/nginx/sites-available/secure-site /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
3. Verify with curl

-    Check headers on main site
```curl -I http://secure.local:8087```

- <img width="1110" height="494" alt="Screenshot (185)" src="https://github.com/user-attachments/assets/c429daa8-9f79-4899-86c1-4fdd228d291e" />


-    Check admin path
```
curl -I http://secure.local:8087/admin
curl -I http://secure.local:8087/admin/
```

- <img width="1103" height="492" alt="Screenshot (183)" src="https://github.com/user-attachments/assets/5f7370c3-78f3-4d32-8b3a-cd3811b5a51b" />

- <img width="1114" height="503" alt="Screenshot (186)" src="https://github.com/user-attachments/assets/b7712b2d-5027-4e7a-a558-a48cd8938f45" />

-    Expected results:

        *    200 OK for / and /admin/ (if allowed IP)

        *    403 Forbidden for /admin from disallowed IP

        *    Security headers present in all responses

-    Verification Output

-    *    Main site
```
HTTP/1.1 200 OK
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

-    *    Admin (allowed IP)
```

HTTP/1.1 200 OK

```

-    *    Admin (denied IP)

```
HTTP/1.1 403 Forbidden
```

