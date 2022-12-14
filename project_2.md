 
 # WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS
---

**Table of Content**

- [**Provisioning an Ubuntu Serve in AWS EC2 Instance.**](#provisioning-an-ubuntu-serve-in-aws-ec2-instance)

- [**Inatalling the Ngninx Web Server**](#inatalling-the-ngninx-web-server)

- [**Installing MYSQL**](#installing-mysql)

- [**Installing PHP**](#installing-php)

- [**Configuring Nginx to use PHP Processor**](#configuring-nginx-to-use-php-processor)

- [**Testing  PHP With Nginx**](#testing-php-with-nginx)

- [**Retriving DATA from MYSQL Database with PHP**](#retriving-data-from-mysql-database-with-php)

- [**Links**](#links)

-----
---


## **Provisioning an Ubuntu Serve in AWS EC2 Instance.**

---

1. AWS account setup and Provisioning an Ubuntu Server*

    ![ubuntu](./images-project2/Ubuntu.PNG)

    Added a LEMP EC2 instance

    ![EC2](./images-project2/Lemp-EC2.PNG)


1. Connecting to your EC2 Instance**

    ssh into the Ec2 instance: `ssh -i "private_key.pem" ubuntu@Public DNS`


    ![ssh](./images-project2/ssh.PNG)


[Back to the top](#web-stack-implementation-lemp-stack-in-aws)

---
---

## **Inatalling the Ngninx Web Server**
---

```
sudo apt update
sudo apt install nginx
```


To verify that nginx was successfully installed:

`sudo systemctl status nginx`

![inginx-satatus](./images-project2/nginx-satatus.PNG)

Updating the security group Configuration by openning port 80

![security-group](./images-project2/Security-group.PNG)


Checking the access locally in our Ubuntu shell,:
```py
 curl http://localhost:80
or

 curl http://127.0.0.1:80
```

Retrieving the  Public IP 


`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

![puplic-ip](./images-project2/public-ip.PNG)

Testing Nginx server response to requests from the Internet:

`http://<Public-IP-Address>:80`

![Index.htlm](./images-project2\Index.htlm.PNG)

[Back to the top](#web-stack-implementation-lemp-stack-in-aws)

---
---
## **Installing MYSQL**

---
Again, use ‘apt’ to acquire and install this software:

`$ sudo apt install mysql-server`

When the installation is finished, log in to the MySQL console by typing:

`$ sudo mysql`


![Mysql](./images-project2/Mysql.PNG)


Run the security script:

`$ sudo mysql_secure_installation`

Testing the security setting by runing:

`$ sudo mysql -p`

![Mysql](./images-project2/Mysql-test.PNG)

[Back to the top](#web-stack-implementation-lemp-stack-in-aws)

---
---
## **Installing PHP**

---
Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server

 *  we will need to install 2 php packages:
 * `php-mysql`, a PHP module that allows PHP to communicate with MySQL-based databases
  
  * `php-fpm` , which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing 
  
  To install these 2 packages at once, run:

`sudo apt install php-fpm php-mysql`

Once the installation is finished, you can run the following command to confirm your PHP version:

`php -v`

![php](./images-project2/PHP-V.PNG)

[Back to the top](#web-stack-implementation-lemp-stack-in-aws)

---
---
## **Configuring Nginx to use PHP Processor**
---

### **Create the root web directory for your_domain as follows**


  1_ Create the directory for projectlemp using ‘mkdir’ command as follows:

`sudo mkdir /var/www/projectLEMP`

2_ Assign ownership of the directory with your current system user:

 `sudo chown -R $USER:$USER /var/www/projectLEMP`

 3_ Create and open a new configuration file in Nginx’s **sites-available** directory using `nano` command-line editor

 `sudo nano /etc/nginx/sites-available/projectLEMP`

 This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

```py
 #/etc/nginx/sites-available/projectLEMP

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
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

Confirming with `ls` commande

`sudo ls /etc/nginx/sites-available`


![ConfigFile](./images-project2/nginx-config-file.PNG)


Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

This will tell Nginx to use the configuration next time it is reloaded.

To check the config file for error, run:

`sudo nginx -t` 

![ingin-test](./images-project2/nginx-test.PNG)

Disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

Reload Nginx to apply the changes:

`sudo systemctl reload nginx`


Creating  an index.html file in the web root /var/www/projectlamp directory to test the virtual host.

```py
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```

`http://<Public-IP-Address>:80`


![Index.htlm](./images-project2\Index.htlm.PNG)

[Back to the top](#web-stack-implementation-lemp-stack-in-aws)

---
---

## **Testing  PHP With Nginx**
---

Finally, we will create a `PHP` script to test that PHP is correctly installed and configured on your server.


Create a new file named `index.php` inside your custom web root folder:

`sudo nano /var/www/projectLEMP/info.php`

This will open a blank file. Add the following text, which is valid PHP code, inside the file:

```
<?php
phpinfo();
```

`http://`server_domain_or_IP`/info.php`

![PHP](./images-project2/info-php.png)


[Back to the top](#web-stack-implementation-lemp-stack-in-aws)

---

## **Retriving DATA from MYSQL Database with PHP**
We’ll need to create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP.

Connecting to the MySQL console using the root account:

`sudo mysql`

Creating a new database named : example_database

`mysql> CREATE DATABASE example_database;`

Creating a new user named: example_user

`mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

Granting example_database, permission over the example_database database:

`mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';`

Exit

`mysql> exit`

Testing the new user access:

`mysql -u example_user -p`

`mysql> SHOW DATABASES;`

![database](./images-project2/Tatabase.PNG)

creating a ToDo list

```
CREATE TABLE example_database.todo_list (
mysql>     item_id INT AUTO_INCREMENT,
mysql>     content VARCHAR(255),
mysql>     PRIMARY KEY(item_id)
mysql> );
```

`mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

To confirm that the data was successfully saved to the table, run:

`mysql>  SELECT * FROM example_database.todo_list;`

![todo-list](./images-project2/todo-list.PNG)

mysql> exit

Creating a PHP script ( PHP file) that will connect to MySQL and query for the content:

`nano /var/www/projectLEMP/todo_list.php`

Copy this content into your todo_list.php script:

```php
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
```

Access this page in a web browser

`http://<Public_domain_or_IP>/todo_list.php`


![PHP](./images-project2\TODO_list-PHP.PNG)

[Back to the top](#web-stack-implementation-lemp-stack-in-aws)

---
---

## **Links**
---
[*Install Open ssh Server*](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui)

[*Open ssh key management*](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement?source=recommendations)

[*Markdown guide*](https://www.markdownguide.org/cheat-sheet)

[Windows VS-code Git and windows terminal installation-part1](https://www.youtube.com/watch?v=R-qcpehB5HY&t=0s)

[Windows VS-code Git and windows terminal installation-part2](https://www.youtube.com/watch?v=jsNIlK5s6pI)

[Learn to code](https://www.w3schools.com/)

[Back to the top](#web-stack-implementation-lemp-stack-in-aws)