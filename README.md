## WEB STACK IMPLEMENTATION (LEMP STACK)

## Implementation Steps

# Step 1 - Launch EC2 Instance

  1. Lanuch an Ec2 instance
![AWS](https://github.com/user-attachments/assets/f66c4226-e685-4b43-95bc-c6f81ac2f29f)
  2. Create SSH key pair to access the instance
  3. Configure security group to allow port 80(HTTP) from anywhere and port 22 (SSH) from host machine IP
  4. Create VPC for networking
  5. Confirm that the instance is running
![instance](https://github.com/user-attachments/assets/c3225621-7d1c-43e1-a775-9916658e79f7)
  6. Connect to the instance using SSH

# Step 2 - Install Nginx Web Server 
   1 Update web erver package index
  ```bash
  sudo apt update
  ```
  2. Install nginx
   ```bash
   sudo apt install nginx
   ```
  3. Enable and verify that nginx is running
   ```bash
   sudo systemctl enable nginx
   sudo systemctl status nginx
   ```
![status_nginx](https://github.com/user-attachments/assets/3dfb35a6-086b-4bac-9f15-74a2d6d555a4)
    4. Test nginx access locally
   ```bash
   curl http://localhost:80
   ```
![curl](https://github.com/user-attachments/assets/b430adef-01ff-4b2a-b2b3-202673bc9ec9)
    5. Test nginx server response to requests from the internet
    ```
    http://<public-IP-Addres>:80
    ```
![nginx](https://github.com/user-attachments/assets/ff8da47c-03d2-4d6e-89ba-eb326fc3a875)

# Step 3 - Install mysql
  1. Install mysql-server
  ```bash
    sudo apt install mysql-server
  ```
  2. Enable and verify that MySQL is successfully installed and running
     ```bash
     sudo systemctl enable --now mysql
     sudo systemctl status mysql
     ```
![status_mysql](https://github.com/user-attachments/assets/ea8dfbfa-617e-45af-9204-2a078f7c9147)

  3. Log in to mysql console
     ```bash
     sudo mysql
     ```
![mysql](https://github.com/user-attachments/assets/71c0c2e9-b46b-4ab1-8254-a17978c07fef)

  4. Set password for the root user, using mysql_native_password
       ```bash
       ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
       ```
  5. Exit mysql shell
     ```bash
     exit
     ```
  6. Start the interactive script to remove some insercure default settings and lock down access to mysql database system.
     ```bash
     sudo mysql_secure_installation
     ```
  7. Test mysql console login with the password
     ```bash
     sudo mysql -p
     ```
  8. Exit mysql console
     ```bash
     exit
     ```
# Step 4 - Install PHP
  1. Install PHP to process code and generate dynamic content for the web server. Install `php-fpm` which tells Nginx to pass PHP requests to the software for processing nd `php-mysql` a PHP module that allows PHP to communicate with MySQL databases.
     ```bash
     sudo apt install php-fpm php-mysql
     ```
  2. confirm PHP version
     ```bash
     php -v
     ```
# Step 5 - Configure Nginx to Use PHP processor
  1. Create root web directory for **your_domain** using
     ```bash
     sudo mkdir /var/www/projectLEMP
     ```
  2. Assign ownership of the directory with the $USER environment variable, which will reference the current system user
    ```bash
      sudo chown -R $USER:$USER /var/www/projectLEMP
    ```
  3. Open a new configuration file in nginx's `sites-available` directory
    ```bash
      sudo nano /etc/nginx/sites-available/projectLEMP
    ```
  5. create a new blank file using any preferred a text editor and paste the following configuration:
     ```bash
    # /etc/nginx/sites-available/projectLEMP

    server {
        listen 80;
        server_name projectLEMP www.projectLEMP;
        root /var/www/projectLEMP;

        index index.html index.htm index.php;

        location / {
          try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
         }

        location ~ /\.ht {
            deny all;
        }
      }
6. 
