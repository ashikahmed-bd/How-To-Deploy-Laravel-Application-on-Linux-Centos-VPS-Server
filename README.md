# How-To-Deploy-Laravel-Application-on-Linux-Centos-VPS-Server

# Deploy Laravel 11 Application on CentOS VPS

## Prerequisites
1. A CentOS VPS server with root or sudo privileges.
2. A Laravel 11 application ready to be deployed.
3. Domain pointing to the server (optional but recommended).
4. Basic knowledge of SSH and server management.

---

## Step 1: Update the Server
```bash
sudo yum update -y
sudo yum install -y epel-release


**Deploying a Laravel application on a Linux CentOS VPS involves several steps, including installing required software, configuring your server, and deploying the application. Here's a detailed guide:**

>1. Update and Secure Your Server
Update the system:
bash
Copy code
sudo yum update -y
Install EPEL repository:
bash
Copy code
sudo yum install epel-release -y
(Optional) Set up a firewall:
bash
Copy code
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
2. Install Required Software
2.1. Install Apache or Nginx
For Apache:

bash
Copy code
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
For Nginx:

bash
Copy code
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
2.2. Install PHP
Laravel requires PHP 8.0+:

bash
Copy code
sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum-config-manager --enable remi-php81
sudo yum install php php-cli php-common php-mysql php-zip php-mbstring php-xml php-bcmath php-curl php-pear -y
2.3. Install MySQL
bash
Copy code
sudo yum install mariadb-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
Secure MySQL installation:

bash
Copy code
sudo mysql_secure_installation
2.4. Install Composer
Laravel requires Composer for dependency management:

bash
Copy code
sudo yum install curl -y
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
3. Configure Your Laravel Application
3.1. Upload Your Laravel Project
Use FTP, SFTP, or SCP to upload your Laravel project to the server, typically in /var/www/html or /home/youruser/laravel.
3.2. Set Correct Permissions
bash
Copy code
sudo chown -R apache:apache /path-to-your-laravel-project
sudo chmod -R 775 /path-to-your-laravel-project/storage /path-to-your-laravel-project/bootstrap/cache
3.3. Set Up .env File
Configure your .env file for database credentials and other environment settings:
plaintext
Copy code
APP_ENV=production
APP_KEY=base64:...
APP_DEBUG=false
APP_URL=http://your-domain.com

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_user
DB_PASSWORD=your_password
3.4. Run Artisan Commands
Navigate to your Laravel project directory:
bash
Copy code
cd /path-to-your-laravel-project
Install dependencies:
bash
Copy code
composer install --optimize-autoloader --no-dev
Generate application key:
bash
Copy code
php artisan key:generate
Run database migrations:
bash
Copy code
php artisan migrate --force
Cache configuration:
bash
Copy code
php artisan config:cache
php artisan route:cache
4. Configure the Web Server
4.1. Apache Configuration
Create a virtual host file for your Laravel application:

bash
Copy code
sudo nano /etc/httpd/conf.d/laravel.conf
Add the following content:

apache
Copy code
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot "/path-to-your-laravel-project/public"

    <Directory "/path-to-your-laravel-project/public">
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog "/var/log/httpd/laravel_error.log"
    CustomLog "/var/log/httpd/laravel_access.log" combined
</VirtualHost>
Enable mod_rewrite for Laravel routing:

bash
Copy code
sudo sed -i 's/AllowOverride None/AllowOverride All/g' /etc/httpd/conf/httpd.conf
Restart Apache:

bash
Copy code
sudo systemctl restart httpd
4.2. Nginx Configuration
Create an Nginx configuration file:

bash
Copy code
sudo nano /etc/nginx/conf.d/laravel.conf
Add the following content:

nginx
Copy code
server {
    listen 80;
    server_name your-domain.com;
    root /path-to-your-laravel-project/public;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ~ /\.ht {
        deny all;
    }
}
Test the configuration:

bash
Copy code
sudo nginx -t
Restart Nginx:

bash
Copy code
sudo systemctl restart nginx
5. Optional: Set Up SSL
Use Let's Encrypt for a free SSL certificate:

bash
Copy code
sudo yum install certbot python3-certbot-apache -y
sudo certbot --apache
For Nginx:

bash
Copy code
sudo yum install certbot python3-certbot-nginx -y
sudo certbot --nginx
6. Verify Deployment
Visit your domain in a browser to check if the Laravel application loads correctly.
Monitor logs for errors:
Apache: /var/log/httpd/error_log
Nginx: /var/log/nginx/error.log
Laravel: /path-to-your-laravel-project/storage/logs/laravel.log
Your Laravel application should now be live on your CentOS VPS server.
