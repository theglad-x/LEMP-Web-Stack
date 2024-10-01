# WEB STACK IMPLEMENTATION (LEMP STACK)
The LEMP web stack is a collection of software used to serve dynamic websites and web applications. It stands for:

- **Linux:** The operating system on which the stack runs.
- **Nginx (pronounced "Engine-X"):** A web server that handles HTTP requests, known for its performance and efficient handling of concurrent connections.
- **MySQL:** The relational database management system used for storing and managing website data.
- **PHP:** The server-side scripting language used for processing dynamic content.

Together, these components form a powerful and efficient stack for hosting and managing web applications.

# Implementation Steps
This step-by-step guide walks through implementing a LEMP (Linux, Nginx, MySQL, PHP) stack on an AWS EC2 instance. It covers everything from launching an instance and setting up necessary services to connecting PHP to MySQL and creating a working web application.

# Step 1 - Launch EC2 Instance

  1. Lanuch an Ec2 instance
![AWS](https://github.com/user-attachments/assets/f66c4226-e685-4b43-95bc-c6f81ac2f29f)
  3. Create SSH key pair to access the instance
  4. Configure security group to allow port 80(HTTP) from anywhere and port 22 (SSH) from host machine IP
  5. Create VPC for networking
  6. Confirm that the instance is running
  7. Connect to the instance using SSH

# Step 2 - Install Nginx Web Server 
  1. Update web server package index
     ```
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
    
     ```
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
      ```
      sudo apt install mysql-server
      ```
  4. Enable and verify that MySQL is successfully installed and running
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
     ``` bash
     sudo nano /etc/nginx/sites-available/projectLEMP
     ```
  4. create a new blank file using any preferred a text editor and paste the following configuration:

     ```
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
  5. Link the configuration file to the config file from Nginx's `sites-enabled` to activate it
     ```bash
     sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
     ```
  6. Test the configuration for syntax error
     ```bash
     sudo nginx -t
     ```
   7. Disable default Nginx host that is currently configured to listen on port 80
      ```bash
      sudo unlink /etc/nginx/sites-enabled/default
      ```
  8. Reload Nginx to apply changes
     ```bash
     sudo systemctl reload nginx
     ```
9. The website is now active, but the project root directory `/var/www/projectLEMP/` is still empty. `index.html` file is creaated to test that to test that the new server block is works as expected.
    ```bash
    sudo echo 'This is LEMP Web Stack Project' > /var/www/projectLEMP/index.html
    ```
10. Acess the serve from the browser using your public IP address or domain name
    ```bash
    http://your_domain_or_IP_address
    ```
   ![projectLEMP](https://github.com/user-attachments/assets/a95ab880-8175-4f64-b94e-2eebcffd4402)

## Step 6 - Testing PHP with Nginx
The LEMP stack is now fully configured. To test that nginx is able to handle nginx `.php` files off PHP processor. 
  1. Create PHP script file in the project root directory, open the file in text editor.
     ```bash
     nano /var/www/projectLEMP/info.php
     ```
  Type or paste the following lines into the new file. This is valid PHP code that will return information about your server
  ```bash
      <?php
      phpinfo();
      ?>
  ```
  2. Access the page on the browser using the IP address or domain name
     ```bash
     http://<public-ip>/info.php
     ```
![php](https://github.com/user-attachments/assets/807ec18a-8e9a-42d4-b37a-ecd57c11bad2)

## Step 6 - Retrieving data from MySQL database with PHP
  1. connect to the MySQL console using the root account
     ```bash
     sudo mysql -u root -p
     ```
  2. To create a new database, run the following command from your MySQL console
     ```bash
     CREATE DATABASE example_database;
     ```
  3. Create a new user and grant the user full privilage on the database created
     ```bash
     CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
      ```

     ```bash
     GRANT ALL ON example_database.* TO 'example_user'@'%';
      ```
  4. Exit the nysql shell with
     ```bash
     exit
     ```
  5. Test if the new user created has the proper permissions by logging in to the mysql console again
     ```bash
     mysql -u example_user -p
     ```
  6. Confirm the user's access to the `example_database` database
     ```bash
     SHOW DATABASES;
     ```
![database](https://github.com/user-attachments/assets/b942f202-d95c-4453-b0bb-dba7db81c2f6)

7. Create a test table named `todo_list` From the MySQL console, run the following statement:
      ```bash
    mysql> CREATE TABLE steghub_DB.todo_list (
    mysql> item_id INT AUTO_INCREMENT,
    mysql> content VARCHAR(255),
    mysql> PRIMARY KEY(item_id)
    );
   ```
8. Insert a few rows of content in the test table
     ```bash
    INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
    ```
 9. Confirm that the data was successfully saved
     ```bash
     mysql> SELECT * FROM example_database.todo_list;
     ```
![todo_list](https://github.com/user-attachments/assets/c9aae217-7192-4038-bd92-f0d392de4622)

  10. After confirming that you have valid data in your test table, you can exit the MySQL console
      ```bash
      exit
      ```
  11. Create a PHP srip that will connect to MySQL and query the content. Create a new PHP file in the poject root directory using any preferred text editor .
      ```bash
          nano /var/www/projectLEMP/todo_list.php
      ```
12. Copy this content into the `todo_list` php script
  
      ```base
      <?php
      $user = "example_user";
      $password = "password";
      $database = "example_database";
      $table = "todo_list";
      
      try {
        $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
        echo "<h2>TODO</h2><ol>"; 
        foreach($db->query("SELECT content FROM $table") as $row) {
          echo "<li>" . $row['content'] . "</li>";
        }
        echo "</ol>";
      } catch (PDOException $e) {
          print "Error!: " . $e->getMessage() . "<br/>";
          die();
      }
  
  13. Access this page in the browser by using the domain or public IP adresesss followed by `/todo_list.php`
      ``` bash
          http://<public-ip>/todo_list.php
       ```
      
## Conclusion:
The implementation of the LEMP web stack (Linux, Nginx, MySQL, PHP) on AWS EC2 provides a powerful, scalable, and efficient environment for hosting dynamic web applications. By following each step, from launching the EC2 instance to configuring Nginx to serve PHP and connecting MySQL to handle data, I created a robust web infrastructure that can support modern web development needs.

This project highlights the process of configuring a secure and optimized server using open-source technologies, allowing seamless interaction between the server, database, and dynamic content. The successful retrieval of data from the MySQL database via PHP scripts demonstrated the full functionality of the LEMP stack.

This project has provided me with hands-on experience with cloud infrastructure (AWS), server setup, and web development technologies, which are essential in modern DevOps and software development environments. The skills and knowledge gained here are critical for deploying scalable, secure, and high-performance web applications.
