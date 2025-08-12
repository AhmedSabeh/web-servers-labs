## Lab 2: Installation and File System Exploration
-  Task 2.1: Verify Installation Directories
Nginx’s main directories can vary depending on your OS, but you can check with:
```
whereis nginx
```
<img width="1023" height="311" alt="Screenshot (156)" src="https://github.com/user-attachments/assets/c09e30f7-6278-42e4-aa07-7db7dcdd42a1" />

Typical important locations:

-  Configuration files: /etc/nginx/

-  Binary: /usr/sbin/nginx

-  Logs: /var/log/nginx/

-  Default web root: /var/www/html/

Task 2.2: Examine Default Configuration
-  The main config file is:
```
sudo nano /etc/nginx/nginx.conf
```
-  Key sections to note:

    *  user → The user Nginx runs as

    *  worker_processes → Number of worker processes

    *  http block → Global HTTP settings

    *  include /etc/nginx/sites-enabled/*; → Loads virtual host configs

Task 2.3: Check Log Files
-  Nginx keeps access logs and error logs.
```
sudo ls -l /var/log/nginx/
```
<img width="1023" height="218" alt="Screenshot (157)" src="https://github.com/user-attachments/assets/c226e92d-4129-4421-aa74-0cdfbabf4e93" />

-  Common log files:

    *  access.log → All incoming requests

    *  error.log → Errors & warnings

    *  View logs:
```
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```
<img width="1221" height="176" alt="Screenshot (158)" src="https://github.com/user-attachments/assets/4a6b9121-c4df-4d5b-b8b9-c1bcc89af14f" />

Task 2.4: Locate Document Root
-  The document root is where your website files live. Check in the default server block config:
```
sudo nano /etc/nginx/sites-available/default
```
-  Look for:
```
root /usr/share/nginx/html;
```
-  This means Nginx will serve files from /usr/share/nginx/html.
<img width="1120" height="435" alt="Screenshot (159)" src="https://github.com/user-attachments/assets/d121ac1c-89b9-42f6-97b3-7e8af6619097" />
