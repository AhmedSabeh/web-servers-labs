Lab 7: Load Balancing Setup
Task 7.1: Create Multiple Backend Servers
We’ll create three backend servers on ports 5001, 5002, and 5003.

bash
Copy
Edit
mkdir ~/backends
cd ~/backends

# Backend 1
mkdir backend1
echo "<h1>Backend Server 1</h1>" > backend1/index.html

# Backend 2
mkdir backend2
echo "<h1>Backend Server 2</h1>" > backend2/index.html

# Backend 3
mkdir backend3
echo "<h1>Backend Server 3</h1>" > backend3/index.html
Task 7.2: Start All Backend Servers
In three separate terminals (or using nohup):

bash
Copy
Edit
# Terminal 1
cd ~/backends/backend1
python3 -m http.server 5001

# Terminal 2
cd ~/backends/backend2
python3 -m http.server 5002

# Terminal 3
cd ~/backends/backend3
python3 -m http.server 5003
Task 7.3: Configure Round Robin Load Balancing
Create a new Nginx config:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/load-balancer
Add:

nginx
Copy
Edit
upstream backend_servers {
    server 127.0.0.1:5001;
    server 127.0.0.1:5002;
    server 127.0.0.1:5003;
}

server {
    listen 8086;
    server_name lb.local;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
Task 7.4: Test Round Robin Load Balancing
Enable and reload:

bash
Copy
Edit
sudo ln -s /etc/nginx/sites-available/load-balancer /etc/nginx/sites-enabled/
sudo nano /etc/hosts
Add:

lua
Copy
Edit
127.0.0.1 lb.local
Reload:

bash
Copy
Edit
sudo nginx -t
sudo systemctl reload nginx
Test in browser:

arduino
Copy
Edit
http://lb.local:8086/
Refresh multiple times — you should see responses rotate between Backend Server 1, Backend Server 2, and Backend Server 3.

Task 7.5: Configure Weighted Load Balancing
In the upstream block:

nginx
Copy
Edit
upstream backend_servers {
    server 127.0.0.1:5001 weight=3;
    server 127.0.0.1:5002 weight=1;
    server 127.0.0.1:5003 weight=1;
}
This makes Backend Server 1 get more traffic.

Task 7.6: Configure Least Connections Load Balancing
nginx
Copy
Edit
upstream backend_servers {
    least_conn;
    server 127.0.0.1:5001;
    server 127.0.0.1:5002;
    server 127.0.0.1:5003;
}
Task 7.7: Test All Load Balancing Methods
Use Round Robin for equal distribution.

Use Weighted for priority traffic.

Use Least Connections for more even distribution under uneven load.

Task 7.8: Test Failover
Stop one backend server:

bash
Copy
Edit
CTRL+C   # in that backend’s terminal
Refresh the browser — Nginx should skip the down server and still serve responses from the others.


