### Lab 4: Serving Static Content and Assets

-    Task 4.1: Create Website Structure
        *    We’ll create a dedicated folder for our new site.
```
sudo mkdir -p /var/www/mysite/{css,js,images}
```
-    Task 4.2: Create JavaScript File

```
sudo tee /var/www/mysite/js/script.js > /dev/null <<EOF
document.addEventListener('DOMContentLoaded', function() {
    alert('JavaScript is loaded!');
});
EOF
```
-    Task 4.3: Create Main HTML Page
```
sudo tee /var/www/mysite/index.html > /dev/null <<EOF
<!DOCTYPE html>
<html>
<head>
    <title>My Static Website</title>
    <link rel="stylesheet" href="/css/style.css">
    <script src="/js/script.js"></script>
</head>
<body>
    <h1>Hello from My Static Website!</h1>
    <img src="/images/sample.png" alt="Sample Image">
</body>
</html>
EOF
```
-    Task 4.4: Configure Nginx for the New Site
        *    Create a new server block:
```
sudo nano /etc/nginx/sites-available/mysite
```
-    Add:

```
server {
    listen 8081;
    server_name mysite.local;

    root /var/www/mysite;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
-    Task 4.5: Enable the Site
```
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
-    Test in browser:

```
http://<server-ip>:8081/
```
-    If on local machine, add to /etc/hosts:
```
sudo nano /etc/hosts
```
-    Add:
```
127.0.0.1 mysite.local
```
