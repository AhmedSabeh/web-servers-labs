### Lab 5: Creating Multiple Virtual Hosts
-    Task 5.1: Create Multiple Website Directories
        *    We’ll make three sites: company, blog, and portfolio.

```
sudo mkdir -p /var/www/{company-site,blog-site,portfolio-site}
```
-    Task 5.2: Create Content for Company Site
```
sudo tee /var/www/company-site/index.html > /dev/null <<EOF
<!DOCTYPE html>
<html>
<head><title>Company</title></head>
<body>
    <h1>Welcome to Our Company</h1>
    <p>We deliver amazing services!</p>
</body>
</html>
EOF
```
-    Task 5.3: Create Content for Blog Site
```
sudo tee /var/www/blog-site/index.html > /dev/null <<EOF
<!DOCTYPE html>
<html>
<head><title>Blog</title></head>
<body>
    <h1>Our Blog</h1>
    <p>Latest updates and articles.</p>
</body>
</html>
EOF
```
-    Task 5.4: Create Content for Portfolio Site
```
sudo tee /var/www/portfolio-site/index.html > /dev/null <<EOF
<!DOCTYPE html>
<html>
<head><title>Portfolio</title></head>
<body>
    <h1>My Portfolio</h1>
    <p>Here are my projects.</p>
</body>
</html>
EOF
```
-    Task 5.5: Create Server Block Configurations
        *    Company site:
```
sudo nano /etc/nginx/sites-available/company
```
```
server {
    listen 8082;
    server_name company.local;

    root /var/www/company-site;
    index index.html;
}
```
-    Blog site:
```
sudo nano /etc/nginx/sites-available/blog
```
```
server {
    listen 8083;
    server_name blog.local;

    root /var/www/blog-site;
    index index.html;
}
```
-    Portfolio site:
```
sudo nano /etc/nginx/sites-available/portfolio
```
```
server {
    listen 8084;
    server_name portfolio.local;

    root /var/www/portfolio-site;
    index index.html;
}
```
-    Task 5.6: Enable All Sites
```
sudo ln -s /etc/nginx/sites-available/company /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/blog /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/portfolio /etc/nginx/sites-enabled/
```
-    Task 5.7: Add Domain Names to Hosts File
```
sudo nano /etc/hosts
```
-    Add:
```
127.0.0.1 company.local
127.0.0.1 blog.local
127.0.0.1 portfolio.local
```
-    Task 5.8: Test All Virtual Hosts
```
sudo nginx -t
sudo systemctl reload nginx
```
-    Now test in your browser:

<img width="1066" height="584" alt="Screenshot (166)" src="https://github.com/user-attachments/assets/9ded66c3-7a80-47b6-9f6e-3a299b7f68f0" />

---
```
     http://company.local:8082/
```
---
<img width="1061" height="590" alt="Screenshot (167)" src="https://github.com/user-attachments/assets/74c4d2d4-b161-43f9-9d47-0b99a79ecd48" />

---
```
     http://blog.local:8083/
```
---    

<img width="1065" height="589" alt="Screenshot (168)" src="https://github.com/user-attachments/assets/541d27ee-380b-4d8d-8dd2-132cab04c612" />

```
     http://portfolio.local:8084/
```
---
