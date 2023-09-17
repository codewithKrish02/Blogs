---
title: Host Wordpress website to AWS
date: 2023-09-16 12:00:00 -5000
categories: [AWS, Worpress, Website]
tags: [aws, website, wordpress, development, hosting, ec2, apache, mysql]     # TAG names should always be lowercase
---

We will begin by selecting a virtual computer (EC2 instance) on AWS and configuring it with Apache, which serves as the web server. Additionally, we'll set up a MySQL database to support our web application.

Once our server and database configurations are complete, we will proceed to install WordPress. It's important to note that this WordPress installation won't require any coding skills; instead, we can easily create and customize our website using WordPress's user-friendly interface. Subsequently, we will deploy the WordPress website on AWS.


## Creating an EC2 instance:

Open your AWS Console and head to EC2 instance to create a new instance with name `Server_wordpress`. I will be chosing a Ubuntu Operating and dont change any default configurations related to the Instance type. Its fine to use the `t2.micro`.

Now, we will create a key-pair, This is used whenever you are trying to login to the instance via ssh. Give a name for you key-pair `Server_keypair` and the type of keypair to be `RSA` and the format of the private key file to be `.pem`. Once you create your key pair it will download your .pem file.

> Note: Make sure not to share your private key with anyone. Once if it gets compromised, they will be getting access to your account.

We also need to configure you Network settings. Here you need to enable the following things:
- Allow HTTPS traffic from the internet
- Allow HTTP traffic from the internet

Now you are good to go for lauching your instance.


## Creating an Elastic IP address:
I must acquire an Elastic IP address for my EC2 instance, as the instance generates a different IP address each time it is restarted. This consistency is essential for DNS recognition of my website.

Here are the steps to create an Elastic IP address:

1. Navigate to the EC2 Dashboard and locate the section labeled "Elastic IPs." Click on this section to access the Elastic IP creation page.

2. Once on the Elastic IP page, proceed to allocate a new Elastic IP address by clicking the appropriate button. You can leave the default configuration settings unchanged and simply click on "Allocate."

3. After successfully allocating the Elastic IP, you will have a new static IP address. The next step is to associate it with your EC2 instance. Select the newly allocated IP, go to the "Actions" menu, and click on "Associate Elastic IP address."

4. In the association process, you'll need to select your instance, named "Server_wordpress," and confirm the association.

By following these steps, you will have obtained a freshly created static IP address that can be used for connecting to your instance via SSH.


## Login to EC2 instance via SSH:

For Mac users, utilizing the built-in Terminal is an option, whereas Windows users require external software like Putty, MTputty, or MobaXterm. Below is the procedure tailored for Mac users:

To get started, right-click on the instance and select "Connect." Then, navigate to the SSH Client tab and follow these steps:

1. Launch the SSH Client.

2. Locate your private key file, which in this case is the key used to launch the instance, known as `Server_keypair.pem`.

3. To ensure proper access to the file and make sure the key isn't publicly visible, execute the following commands:
   - chmod 400 `Server_keypair.pem`
   - chmod 600 `Server_keypair.pem`

4. Now, establish a connection with your instance using the following command:
   - ssh -i server_keypair.pem 'username@public_dns' (The necessary public DNS information will be provided on the same page).

For Windows users employing MobaXterm, configuration is straightforward. You can simply attach your private key and input all the required details such as the username and host address, which is the IP address, to gain access and log in.


## Installing Apache Server to the Instance:
To set up an Apache web server on this instance, run the command below:
> sudo apt install apache2

To confirm whether the server is successfully installed or not, you can open your EC2 instance in a web browser using the following link:

>http://yourelasticip

Clicking on the provided link will take you to your instance and display the Apache Default page if the installation was successful.

To enable PHP on your server and establish a connection to MySQL, you'll need to install PHP and the PHP MySQL connector. Use the following command:
     
> sudo apt install php libapache2-mod-php php-mysql
     
To set up the MySQL database management system, which is essential for storing data for various applications, including WordPress, run the following command:
    
> sudo apt install mysql-server

To enhance security, it's recommended to change the MySQL authentication plugin for the root user to MySQL native password. This command allows you to access the MySQL command line:
>sudo mysql -u root
     
Then, execute the following command to make the change:
     
>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'your_strong_password';

Replace `'your_strong_password'` with the desired strong password.

As WordPress requires its own user to interact with the database, create a ne   w user with the following command:
     
> CREATE USER 'wp_user'@localhost IDENTIFIED BY 'your_strong_password';
     
Replace `'your_strong_password'` with a secure password.

WordPress needs a separate database to store its data. Create this database using the following command:

> CREATE DATABASE wp;

To allow the newly created 'wp_user' to manage the 'wp' database, grant all privileges with this command:

> GRANT ALL PRIVILEGES ON wp.* TO 'wp_user'@localhost;
     
To exit the MySQL command line after making the necessary changes and configurations, simply type:
     
>exit;
     

These steps collectively prepare your server for hosting WordPress by installing necessary software components and configuring MySQL for secure and efficient operation.


## Install Wordpress:
Certainly, here's the entire process with descriptions that you can copy and follow:

1. **Navigate to the Temporary Directory:**
   - Change your current directory to the `/tmp` directory in your shell, where you'll temporarily store the WordPress installation package.

   ```
   cd /tmp
   ```

2. **Download the WordPress Package:**
   - Download the WordPress installation package using the `wget` command from the provided link. This retrieves the necessary files to install WordPress on your server.

   ```
   wget https://wordpress.org/wordpress-6.3.1.tar.gz
   ```

3. **Unzip and Move WordPress Files:**
   - Unzip the downloaded WordPress package and move its contents to the root directory of the Apache web server. Placing them in `/var/www/html/` allows you to serve the WordPress site from the webserver's document root.

   ```
   tar -xvf yourfilename.tar.gz
   sudo mv wordpress/ /var/www/html/
   ```

4. **Access the WordPress Installation Screen:**
   - Open a web browser and go to the following URL to initiate the WordPress installation process:

   ```
   http://static_ipaddress/wordpress
   ```

   This URL takes you to the WordPress setup screen.

5. **Configure Database Settings:**
   - On the WordPress installation screen, configure the database settings to connect WordPress with the MySQL database you created earlier.
   - **Database Name**: Specify the name of the database, typically `wp`.
   - **Username**: Choose a username, for example, `wp_database`.
   - **Password**: Enter a strong password (consider using the one used when creating the MySQL database for consistency).
   - **Database Host**: The default value, "localhost," is usually correct.
   - **Table Prefix**: Keep it as "wp_" (default).

6. **Submit the Form:**
   - After entering these database configuration details, submit the form. WordPress will validate the settings and proceed with the installation process.

By following these steps, you'll install WordPress on your server and connect it to the MySQL database, allowing you to start building and customizing your website.

Here are the instructions for dealing with any errors encountered during the database creation process in WordPress:

Note: If you encounter any error while creating the database regarding the 'wp-config.php' simply copy the properties which it has given to you, and navigate to the WordPress folder.

```
> cd /var/www/html/wordpress/
> nano wp-config.php
```
Paste all those properties into this file, save, and exit.
After following these steps, you should be able to log in to your WordPress dashboard and configure your settings as needed.

After successfully installing WordPress, follow these steps to configure it:

1. **Enter the Following Details:**
   - Site Title: Blog Site
   - Username: codewithKrish
   - Password: Make sure to set a strong password for security.
   - Email: Provide your email address.
   
   Proceed with the WordPress installation.

2. **Access the WordPress Sample Page:**
   - After installation, navigate to the following URL to view the sample page provided by WordPress, confirming that the installation was successful:
   
   ```
   your_ip_address/wordpress
   ```

3. **Customize Your Website:**
   - To start customizing your website, log in to your WordPress account using the credentials you just created:
   
   ```
   your_ip_address/wp-login.php
   ```

Currently, your homepage is accessed by entering your IP address followed by "/wordpress." However, I prefer to have my homepage load automatically when I enter my IP address without the "/wordpress" extension. Let's adjust this configuration.

## Changing the document root location:

In the terminal, navigate to the specified directory:

```
cd /etc/apache2/sites-available/
```

List the contents of the directory using the `ls` command.

You will find a file named `000-default.conf`. Open this file with elevated permissions using the following command:

```
sudo nano 000-defualt.conf
```

Within this file, you should modify the document root to point to `/var/www/html/wordpress`. After making this change, restart the Apache web server to apply the configuration adjustment:

```
sudo systemctl restart apache2
```

By reloading the page now, you should observe the WordPress homepage when you enter your IP address directly in your web browser.

