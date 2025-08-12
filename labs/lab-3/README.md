Lab 3: Basic Configuration Modification
Task 3.1: Create a Custom HTML Page
We’ll replace the default Nginx page with our own.

bash
Copy
Edit
sudo tee /var/www/html/index.html > /dev/null <<EOF
<!DOCTYPE html>
<html>
<head>
    <title>My Custom Nginx Page</title>
</head>
<body>
    <h1>Welcome to My Custom Nginx Server!</h1>
    <p>This page is served by Nginx.</p>
</body>
</html>
EOF
Task 3.2: Test Configuration Syntax
Before applying changes, always check syntax:

bash
Copy
Edit
sudo nginx -t
If OK, reload Nginx:

bash
Copy
Edit
sudo systemctl reload nginx
Task 3.3: View Your Custom Page
Open your browser and go to:

cpp
Copy
Edit
http://<server-ip>/
You should see your custom HTML content instead of the default Nginx welcome page.

Task 3.4: Modify Default Port
Let’s change Nginx to listen on port 2000 instead of 80.

Edit the default config:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/default
Change:

nginx
Copy
Edit
listen 80 default_server;
listen [::]:80 default_server;
to:

nginx
Copy
Edit
listen 2000 default_server;
listen [::]:2000 default_server;
Save & exit.

Test and reload:

bash
Copy
Edit
sudo nginx -t
sudo systemctl reload nginx
Access your page on the new port:

cpp
Copy
Edit
http://<server-ip>:2000/
