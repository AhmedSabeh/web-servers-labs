Lab 2: Installation and File System Exploration
Task 2.1: Verify Installation Directories
Nginx’s main directories can vary depending on your OS, but you can check with:

bash
Copy
Edit
whereis nginx
Typical important locations:

Configuration files: /etc/nginx/

Binary: /usr/sbin/nginx

Logs: /var/log/nginx/

Default web root: /var/www/html/

Task 2.2: Examine Default Configuration
The main config file is:

bash
Copy
Edit
sudo nano /etc/nginx/nginx.conf
Key sections to note:

user → The user Nginx runs as

worker_processes → Number of worker processes

http block → Global HTTP settings

include /etc/nginx/sites-enabled/*; → Loads virtual host configs

Task 2.3: Check Log Files
Nginx keeps access logs and error logs.

bash
Copy
Edit
sudo ls -l /var/log/nginx/
Common log files:

access.log → All incoming requests

error.log → Errors & warnings

View logs:

bash
Copy
Edit
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
Task 2.4: Locate Document Root
The document root is where your website files live. Check in the default server block config:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/default
Look for:

nginx
Copy
Edit
root /var/www/html;
This means Nginx will serve files from /var/www/html.
