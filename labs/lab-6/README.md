Lab 6: Setting Up Reverse Proxy
Task 6.1: Create a Simple Backend Server
We’ll use Python’s built-in HTTP server as our backend.

bash
Copy
Edit
mkdir ~/backend-server
cd ~/backend-server
echo "<h1>Hello from Backend Server</h1>" > index.html
Task 6.2: Start the Backend Server
bash
Copy
Edit
python3 -m http.server 5000
This starts a backend server on port 5000. Keep this terminal open (or run with nohup).

Task 6.3: Configure Nginx Reverse Proxy
We’ll create a new Nginx config that listens on port 8085 and forwards requests to the backend server.

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/reverse-proxy
Add:

nginx
Copy
Edit
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
Task 6.4: Enable and Test the Proxy
bash
Copy
Edit
sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
sudo nano /etc/hosts
Add:

lua
Copy
Edit
127.0.0.1 proxy.local
Test config & reload:

bash
Copy
Edit
sudo nginx -t
sudo systemctl reload nginx
Test in browser:

arduino
Copy
Edit
http://proxy.local:8085/
You should see Hello from Backend Server coming through Nginx.

Task 6.5: Advanced Proxy Configuration
We can add caching and timeouts:

nginx
Copy
Edit
location / {
    proxy_pass http://127.0.0.1:5000;
    proxy_cache my_cache;
    proxy_cache_valid 200 1m;
    proxy_read_timeout 30s;
    proxy_connect_timeout 5s;
}
Also, add in http {} block in /etc/nginx/nginx.conf:

nginx
Copy
Edit
proxy_cache_path /tmp/nginx_cache levels=1:2 keys_zone=my_cache:10m inactive=60m;
Task 6.6: Test Advanced Features
Stop your backend server and refresh the page — cached responses may still appear for 1 minute.

Change backend content and reload to see how caching affects updates.


