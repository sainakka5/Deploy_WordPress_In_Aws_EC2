Building a WordPress Website in EC2

WordPress is a widely-used, open-source content management system (CMS) that powers millions of websites worldwide, ranging from personal blogs to complex e-commerce sites. Its user-friendly interface, extensive plugin ecosystem, and customizable themes make it a popular choice for individuals and businesses alike. In this task, we will build a WordPress host on an Ubuntu AWS EC2 instance, configure it to create a functional two-page website, and explore the capabilities of this versatile platform. This process will involve setting up a LAMP stack (Linux, Apache, MySQL, PHP), configuring WordPress, and creating website content, providing a hands-on experience with both AWS and WordPress.

WordPress Server Requirements:
Server requirements involve several elements: RAM, database version, processing power, disk space, and the web server used.
Here’s a quick look into the standard recommendations for a server when running WordPress:

Web server: Nginx or Apache with mod-rewrite module
Disk space: At least 1 GB
PHP: Version 8.0 or higher
Database: MySQL 5.015 or higher (An alternative is MariaDB, version 10.1 or higher)
RAM (Random Access Memory): At least 512 MB
CPU (Central Processing Unit): At least 1.0 GHz
Support for HTTPS

Launch an EC2 Instance :
•	Log in to the AWS Management Console.
•	Navigate to the EC2 Dashboard and click "Launch Instance".
•	Choose an Amazon Machine Image (AMI) - Select "Ubuntu Server 20.04 LTS (HVM), SSD Volume Type".
•	Choose an Instance Type (e.g., t2.micro which is free tier eligible).
•	Configure Instance Details, Add Storage, and configure security groups to allow HTTP (port 80) and SSH (port 22) access.
•	Review and launch the instance, creating or selecting a key pair for SSH access.

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/b82d0e71-0d3e-4f1d-9601-46a5fec63fb4)


Here it is created a EC2 instance with my resources successfully, from this Public IP Address I want to connect with this server using Local Terminal.

    ssh -i .\sainivesh.pem ubuntu@54.226.182.55

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/aa777866-98ad-470f-9c13-4bffa6be4365)


Update the package list and install Apache:
By giving the below commands, we can update our server and install the Apache2 Automatically. Apache2 is works as the webserver for WORDPRESS Applications.

    sudo apt update
    sudo apt install apache2 -y

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/b0852e08-de13-42b2-b831-2fb24b3118c4)


Install MySQL:

For database software, WordPress functions best with MySQL version 5.6+, So I installed latest Version.
MySQL is a DBMS (database management system) used by WordPress to store information. WordPress MySQL plays a critical part in WordPress's storage of user information, posts, comments, settings, and much more. Without MySQL, a WordPress website cannot function.

    sudo apt install mysql-server -y

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/b56a4414-cb0c-44b1-a1d2-67840724392b)


Secure MySQL:
The “mysql_secure_installation” script is a security tool provided by MySQL to enhance the security of your MySQL server. Running this script helps you set a root password, remove anonymous users, disallow remote root logins, remove the test database, and reload privilege tables. By following these steps, you can ensure that your MySQL server is more secure and less vulnerable to unauthorized access and potential attacks.
   sudo mysql_secure_installation
   
![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/47a69b13-9aae-43df-90e6-92a9d603c39e)

 
Install PHP and required modules:

Installing PHP along with libapache2-mod-php and php-mysql is crucial for setting up a LAMP (Linux, Apache, MySQL, PHP) stack, which is a common environment for hosting web applications. This setup allows your web server to handle dynamic content and interact with databases, making it possible to run a wide variety of web applications, including WordPress, which relies on PHP and MySQL.
The command “sudo apt install php libapache2-mod-php php-mysql -y” is used to install PHP and the necessary modules required to run PHP applications with an Apache web server and MySQL database. Here’s a breakdown of the components and their uses:

    sudo apt install php libapache2-mod-php php-mysql -y

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/8992c6c9-6073-4c51-877a-269541cf91e2)


Create a MySQL Database and User for WordPress :

Creating a MySQL database and user for WordPress involves logging into MySQL and executing specific SQL commands. First, you log in to MySQL using sudo mysql. Then, you create a database named WordPress to store WordPress data. You also create a MySQL user, WordPress user, with a secure password. By granting all privileges on the WordPress database to this user, you ensure that WordPress can fully manage its own data. Finally, FLUSH PRIVILEGES reloads the grant tables, applying the changes immediately. This setup isolates the WordPress database and user, enhancing security and manageability.
sudo mysql    #commad to Log in to MySQL

I had Created a database and user, and grant all privileges as below :

    CREATE DATABASE wordpress;
    CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
    GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/eebad610-2daf-460d-bae2-3a6cecb8ca82)
 

Install WordPress :
Download WordPress Application from the official website in tar format and extract the application and Move to the Apache software running path  /var/www/html.

Command:

    cd /tmp
    wget https://wordpress.org/latest.tar.gz
    tar -xzf latest.tar.gz
    sudo mv wordpress /var/www/html/      # it will move all files in wordpress to /var/www/html 

Set the correct permissions:

Setting the correct permissions for the WordPress files involves two commands. First, sudo chown -R www-data:www-data /var/www/html/wordpress changes the ownership of the WordPress directory and its contents to the www-data user and group, which is the default user that Apache runs as. This ensures that the web server can read and write to these files. Second, sudo chmod -R 755 /var/www/html/wordpress sets the permissions so that the owner has full read, write, and execute permissions, while others have read and execute permissions. This combination ensures that WordPress operates smoothly and securely.

    sudo chown -R www-data:www-data /var/www/html/wordpress
    sudo chmod -R 755 /var/www/html/wordpress

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/7519f35d-4520-42a4-a133-316a55a103eb)


Create the WordPress configuration file:

cd /var/www/html/wordpress
        
    sudo cp wp-config-sample.php wp-config.php

Edit the WordPress configuration file to add the database details I had given in mysql properly.

    sudo nano wp-config.php

Update the following lines in that file as shown below,

    define('DB_NAME', 'wordpress');
    define('DB_USER', 'wordpressuser');
    define('DB_PASSWORD', 'password');
    define('DB_HOST', 'localhost');

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/dcba47c1-1d94-49d7-8b70-0ddb4170f31e)
 

Restart Apache:
 It will restart the service Apache , It will update all details of WordPress to Apache and make a webserver running with WordPress Application.
# it is the default page in Apache , if we don’t remove it will show the content of this file.

    sudo rm /var/www/html/index.html  
    sudo systemctl restart apache2

After that open a web browser and enter the IP Address of this Instance running with WordPress Application and Enter the search button, we will see the Word Press application setup page which is Successfully running.

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/795240ed-95a6-465b-acb3-5f3c4a981348)

 
Follow the on-screen instructions to complete the WordPress installation, setting up your site title, username, password, and email.

 
![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/2fe3488c-6c6a-4119-8136-f7b52c41d6cc)

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/e175ef3e-a713-4ae7-8e49-64daaf9474bf)
 

After giving the information, username and password again login into the WordPress website

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/e8d95c46-968e-4228-bd59-dcac5ced3704)


Here we successfully login into the Word Press Web Application as shown in the Screenshot.
 
![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/44eb8c7c-6aa3-4375-8080-a0b4f200c095)

Now create a new page by clicking on the Add New Page button.
 
![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/43b7ffc7-4c5b-4f0d-9fed-dbd753e3dacd)


Here we can select different types of teams to create webpages select one and write the content and Paste Pictures to that content.

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/69bb6838-5964-4e35-b7c0-1e82899e24b9)
 

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/6ab2e4ef-4cef-412f-af73-a02405966922)
 

Here I created 3 pages of website as shown in the figure, now it is ready to upload and publish the page. So now click on the page publish option.

 ![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/e9118822-14a9-4f07-9aa2-08411fdb7fdf)


After publishing it will give the page Address or Url of it as shown below screenshot, copy it and paste it on a new tab in a browser.

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/a10b63ca-c290-4377-9589-4b4f5cb87e44)
 

After pasting the URL of the page I created it is successfully loaded and Appeared as shown in the below Screenshot.

![image](https://github.com/sainakka5/Deploy_WordPress_In_Aws_EC2/assets/136338958/c5f6d482-6c8d-4346-a896-0246d662085e)
 

Conclusions:
I successfully completed the task of setting up a WordPress website on an AWS EC2 instance. I launched the instance, connected to it via SSH, installed the LAMP stack, and configured MySQL for WordPress. After installing WordPress, I created a two-page website, setting up the homepage and a contact page. Throughout the process, I took screenshots of each significant step, ensuring to include a slice of my desktop showing a timestamp. Finally, after completing all tasks and taking the necessary screenshots, I terminated the EC2 instance.
