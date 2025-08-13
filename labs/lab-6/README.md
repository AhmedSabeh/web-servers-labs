### Lab 6: Setting Up Reverse Proxy
---
-    Task 6.1: Create a Simple Backend Server
        *    We’ll use Python’s built-in HTTP server as our backend.
```
mkdir ~/backend-server
cd ~/backend-server
echo "<h1>Hello from Backend Server</h1>" > index.html
```
-    Task 6.2: Start the Backend Server
```
python3 -m http.server 5000
```
This starts a backend server on port 5000. Keep this terminal open (or run with nohup).

-    Task 6.3: Configure Nginx Reverse Proxy
        *    We’ll create a new Nginx config that listens on port 8085 and forwards requests to the backend server.
```
sudo nano /etc/nginx/sites-available/reverse-proxy
```
Add:
```
server {
    listen 8085;
    server_name proxy.local;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
-    Task 6.4: Enable and Test the Proxy
```
sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
sudo nano /etc/hosts
```
Add:
```
127.0.0.1 proxy.local
```
Test config & reload:
```
sudo nginx -t
sudo systemctl reload nginx
```
Test in browser:
```
http://proxy.local:8085/
```
You should see Hello from Backend Server coming through Nginx.
<img width="1059" height="588" alt="Screenshot (169)" src="https://github.com/user-attachments/assets/62ac07ee-f23e-4ce7-b9a0-a44e9ec28364" />


-    Task 6.5: Advanced Proxy Configuration
        *    We can add caching and timeouts:
```
location / {
    proxy_pass http://127.0.0.1:5000;
    proxy_cache my_cache;
    proxy_cache_valid 200 1m;
    proxy_read_timeout 30s;
    proxy_connect_timeout 5s;
}
```
Also, add in http {} block in /etc/nginx/nginx.conf:
```
proxy_cache_path /tmp/nginx_cache levels=1:2 keys_zone=my_cache:10m inactive=60m;
```
-    Task 6.6: Test Advanced Features
        *    Stop your backend server and refresh the page — cached responses will still appear for 1 minute and then disappear.
<img width="1063" height="592" alt="Screenshot (170)" src="https://github.com/user-attachments/assets/53230eb5-d2d5-408e-9a14-0aa258146710" />
        *    Change backend content and reload to see how caching affects updates.
