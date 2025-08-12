Lab 1: Environment Setup and Basic Exploration
Task 1.1: Check if Nginx is Installed
Run this command to check if Nginx is installed and get its version:

bash
Copy
Edit
nginx -v
If you get an output like nginx version: nginx/1.18.0 (version may differ), it means Nginx is installed.

If you get command not found, it’s not installed yet.

Task 1.2: Install Nginx (if needed)
For Ubuntu/Debian systems:

bash
Copy
Edit
sudo apt update
sudo apt install nginx -y
For CentOS/RHEL:

bash
Copy
Edit
sudo yum install epel-release -y
sudo yum install nginx -y
Start and enable Nginx service:

bash
Copy
Edit
sudo systemctl start nginx
sudo systemctl enable nginx
Task 1.3: Basic Nginx Commands
Start Nginx:

bash
Copy
Edit
sudo systemctl start nginx
Stop Nginx:

bash
Copy
Edit
sudo systemctl stop nginx
Restart Nginx:

bash
Copy
Edit
sudo systemctl restart nginx
Reload Nginx configuration without downtime:

bash
Copy
Edit
sudo systemctl reload nginx
Check Nginx status:

bash
Copy
Edit
sudo systemctl status nginx
Test Nginx configuration syntax:

bash
Copy
Edit
sudo nginx -t
Task 1.4: Test Basic Installation
Open your browser and navigate to:

arduino
Copy
Edit
http://localhost/
Or if on a remote server:

cpp
Copy
Edit
http://<server-ip>/
