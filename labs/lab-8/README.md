Lab 8: Implementing Basic Security
Task 8.1: Configure Security Headers
Edit your main Nginx config or a specific site config (e.g., /etc/nginx/sites-available/load-balancer).

Inside the server block, add:

nginx
Copy
Edit
add_header X-Content-Type-Options nosniff;
add_header X-Frame-Options SAMEORIGIN;
add_header X-XSS-Protection "1; mode=block";
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
These help prevent:

MIME sniffing attacks

Clickjacking

XSS

Weak HTTPS configurations

Task 8.2: Configure Rate Limiting in Main Config
In /etc/nginx/nginx.conf inside the http {} block:

nginx
Copy
Edit
limit_req_zone $binary_remote_addr zone=one:10m rate=5r/s;
Then inside your server block:

nginx
Copy
Edit
location / {
    limit_req zone=one burst=10 nodelay;
    proxy_pass http://backend_servers;
}
This limits clients to 5 requests per second, with bursts up to 10.

Task 8.3: Create Secure Website Content
bash
Copy
Edit
sudo mkdir -p /var/www/secure-site
sudo tee /var/www/secure-site/index.html > /dev/null <<EOF
<h1>Secure Site</h1>
<p>This site is protected with security headers, rate limiting, and IP controls.</p>
EOF
Task 8.4: Enable and Test Security Configuration
Create config /etc/nginx/sites-available/secure-site:

nginx
Copy
Edit
server {
    listen 8087;
    server_name secure.local;

    root /var/www/secure-site;
    index index.html;

    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options SAMEORIGIN;
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    location / {
        limit_req zone=one burst=10 nodelay;
    }
}
Task 8.5: Test Security Headers
bash
Copy
Edit
curl -I http://secure.local:8087/
Look for the added security headers in the output.

Task 8.6: Test IP Access Control
Inside the server block:

nginx
Copy
Edit
location /admin {
    allow 192.168.1.100;  # Replace with your IP
    deny all;
}
Task 8.7: Test Rate Limiting
Run:

bash
Copy
Edit
ab -n 20 -c 10 http://secure.local:8087/
You should see 503 Service Temporarily Unavailable when exceeding limits.

Task 8.8: Create Security Monitoring Script
Example basic check:

bash
Copy
Edit
#!/bin/bash
LOG=/var/log/nginx/access.log
if grep -q "403" $LOG; then
    echo "ALERT: Forbidden access detected on $(date)" >> /var/log/nginx/security_alerts.log
fi
Make executable:

bash
Copy
Edit
chmod +x /usr/local/bin/nginx-security-check.sh
Schedule with cron to run every minute.


