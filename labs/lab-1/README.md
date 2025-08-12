## Lab 1: Environment Setup and Basic Exploration
-  Task 1.1: Check if Nginx is Installed
   *  Run this command to check if Nginx is installed and get its version:
```
nginx -v
```
<img width="828" height="200" alt="Screenshot (155)" src="https://github.com/user-attachments/assets/4c935922-d9ba-4c26-af06-7e1098da90cb" />

   *  If you get command not found, it’s not installed yet.

-  Task 1.2: Install Nginx (if needed)
   *  For Ubuntu/Debian systems:
```
sudo apt update
sudo apt install nginx -y
```
   *  For CentOS/RHEL:
```
sudo yum install epel-release -y
sudo yum install nginx -y
```
   *  Start and enable Nginx service:
```
sudo systemctl start nginx
sudo systemctl enable nginx
```
-  Task 1.3: Basic Nginx Commands

   *  Start Nginx:
```
sudo systemctl start nginx
```
   *  Stop Nginx:
```
sudo systemctl stop nginx
```
   *  Restart Nginx:
```
sudo systemctl restart nginx
```
   *  Reload Nginx configuration without downtime:
```
sudo systemctl reload nginx
```
   *  Check Nginx status:
```
sudo systemctl status nginx
```
   *  Test Nginx configuration syntax:
```
sudo nginx -t
```
-  Task 1.4: Test Basic Installation
   *  Open your browser and navigate to:

      http://localhost/
   *  Or if on a remote server:
```
curl http:localhost:80
```
<img width="990" height="589" alt="Screenshot (154)" src="https://github.com/user-attachments/assets/5dc21e17-1375-4548-b253-b080b620eab5" />
