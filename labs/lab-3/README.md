## Lab 3: Basic Configuration Modification
-    Task 3.1: Create a Custom HTML Page
        *    We’ll replace the default Nginx page with our own.
```
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
```
Task 3.2: Test Configuration Syntax
        *    Before applying changes, always check syntax:
```
sudo nginx -t
```

If OK, reload Nginx:

```
sudo systemctl reload nginx
```
-    Task 3.3: View Your Custom Page
        *    Open your browser and go to:
```
http://localhost:2000
```
-    You should see your custom HTML content instead of the default Nginx welcome page.

-    Task 3.4: Modify Default Port
        *    Let’s change Nginx to listen on port 2000 instead of 80.

        *    Edit the default config:
```
sudo nano /etc/nginx/sites-available/default
```
-    Change:
```
listen 80 default_server;
listen [::]:80 default_server;
```
-    to:
```
listen 2000 default_server;
listen [::]:2000 default_server;
```
<img width="1221" height="88" alt="Screenshot (162)" src="https://github.com/user-attachments/assets/e8611845-6e23-4765-8f18-0e1fcaf97dbd" />

-    Save & exit.

-    Test and reload:
```
sudo nginx -t
sudo systemctl reload nginx
```
-    Access on the new port:
```
curl http:localhost:2000/
```
<img width="983" height="592" alt="Screenshot (161)" src="https://github.com/user-attachments/assets/af32bfa1-3f94-4aa7-8696-d3670b39ff61" />
