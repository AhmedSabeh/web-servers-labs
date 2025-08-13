Lab 4: Serving Static Content and Assets
Task 4.1: Create Website Structure
We’ll create a dedicated folder for our new site.

bash
Copy
Edit
sudo mkdir -p /var/www/mysite/{css,js,images}
Task 4.2: Create JavaScript File
bash
Copy
Edit
sudo tee /var/www/mysite/js/script.js > /dev/null <<EOF
document.addEventListener('DOMContentLoaded', function() {
    alert('JavaScript is loaded!');
});
EOF
Task 4.3: Create Main HTML Page
bash
Copy
Edit
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
Task 4.4: Configure Nginx for the New Site
Create a new server block:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/mysite
Add:

nginx
Copy
Edit
server {
    listen 8081;
    server_name mysite.local;

    root /var/www/mysite;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
Task 4.5: Enable the Site
bash
Copy
Edit
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
Test in browser:

cpp
Copy
Edit
http://<server-ip>:8081/
If on local machine, add to /etc/hosts:

bash
Copy
Edit
sudo nano /etc/hosts
Add:

lua
Copy
Edit
127.0.0.1 mysite.local
