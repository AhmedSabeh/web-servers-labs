Lab 5: Creating Multiple Virtual Hosts
Task 5.1: Create Multiple Website Directories
We’ll make three sites: company, blog, and portfolio.

bash
Copy
Edit
sudo mkdir -p /var/www/{company-site,blog-site,portfolio-site}
Task 5.2: Create Content for Company Site
bash
Copy
Edit
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
Task 5.3: Create Content for Blog Site
bash
Copy
Edit
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
Task 5.4: Create Content for Portfolio Site
bash
Copy
Edit
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
Task 5.5: Create Server Block Configurations
Company site:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/company
nginx
Copy
Edit
server {
    listen 8082;
    server_name company.local;

    root /var/www/company-site;
    index index.html;
}
Blog site:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/blog
nginx
Copy
Edit
server {
    listen 8083;
    server_name blog.local;

    root /var/www/blog-site;
    index index.html;
}
Portfolio site:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/portfolio
nginx
Copy
Edit
server {
    listen 8084;
    server_name portfolio.local;

    root /var/www/portfolio-site;
    index index.html;
}
Task 5.6: Enable All Sites
bash
Copy
Edit
sudo ln -s /etc/nginx/sites-available/company /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/blog /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/portfolio /etc/nginx/sites-enabled/
Task 5.7: Add Domain Names to Hosts File
bash
Copy
Edit
sudo nano /etc/hosts
Add:

lua
Copy
Edit
127.0.0.1 company.local
127.0.0.1 blog.local
127.0.0.1 portfolio.local
Task 5.8: Test All Virtual Hosts
bash
Copy
Edit
sudo nginx -t
sudo systemctl reload nginx
Now test in your browser:

arduino
Copy
Edit
http://company.local:8082/
http://blog.local:8083/
http://portfolio.local:8084/
