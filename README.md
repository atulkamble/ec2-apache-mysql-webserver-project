Creating a full project with an **EC2 instance**, **Apache2 server**, and **MySQL database** involves setting up a web server, a database, and a web application that interacts with the database. Below is a step-by-step guide to building this project, including all necessary code.

---

## **Step 1: Set Up an EC2 Instance**

1. **Log in to AWS Console**:
   - Go to the [AWS Management Console](https://aws.amazon.com/console/).
   - Navigate to the **EC2** service.

2. **Launch an EC2 Instance**:
   - Click **Launch Instance**.
   - Choose an Amazon Machine Image (AMI), such as **Amazon Linux 2** or **Ubuntu**.
   - Select an instance type (e.g., `t2.micro` for free tier).
   - Configure instance details (default settings are fine for this project).
   - Add storage (default 8GB is sufficient).
   - Add tags (optional).
   - Configure the security group:
     - Add rules to allow HTTP (port 80), HTTPS (port 443), SSH (port 22), and MySQL (port 3306) traffic.
   - Review and launch the instance.
   - Create and download a key pair (`.pem` file) to access the instance via SSH.

3. **Connect to the EC2 Instance**:
   - Use SSH to connect to your instance:
     ```bash
     ssh -i /path/to/your-key.pem ec2-user@your-ec2-public-ip
     ```
   - Replace `ec2-user` with `ubuntu` if you're using an Ubuntu AMI.

---

## **Step 2: Install Apache2 and MySQL**

1. **Update the System**:
   ```bash
   sudo yum update -y  # For Amazon Linux
   sudo apt update && sudo apt upgrade -y  # For Ubuntu
   ```

2. **Install Apache2**:
   ```bash
   sudo yum install httpd -y  # For Amazon Linux
   sudo apt install apache2 -y  # For Ubuntu
   ```

3. **Install MySQL**:
   ``
   sudo wget https://dev.mysql.com/get/mysql80-community-release-el7-5.noarch.rpm
   sudo rpm -ivh mysql80-community-release-el7-5.noarch.rpm`
   
   sudo dnf install mariadb105
   mysql --version
   sudo yum install mysql-server -y  # For Amazon Linux
   sudo apt install mysql-server -y  # For Ubuntu
   ```

5. **Start and Enable Apache2 and MySQL**:
   ```bash
   sudo systemctl start httpd  # For Amazon Linux
   sudo systemctl start apache2  # For Ubuntu
   sudo systemctl start mysqld  # For Amazon Linux
   sudo systemctl start mysql  # For Ubuntu

   sudo systemctl enable httpd  # For Amazon Linux
   sudo systemctl enable apache2  # For Ubuntu
   sudo systemctl enable mysqld  # For Amazon Linux
   sudo systemctl enable mysql  # For Ubuntu
   ```

6. **Secure MySQL Installation**:
   - Run the MySQL security script:
     ```bash
     sudo mysql_secure_installation
     ```
   - Follow the prompts to set a root password and secure the installation.

7. **Verify Apache2 and MySQL Installation**:
   - Open your browser and navigate to your EC2 instance's public IP address:
     ```
     http://<your-ec2-public-ip>
     ```
   - You should see the Apache2 default landing page.

---

## **Step 3: Set Up MySQL Database**

1. **Log in to MySQL**:
   ```bash
   sudo mysql -u root -p
   ```

2. **Create a Database and User**:
   ```sql
   CREATE DATABASE myproject;
   CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypassword';
   GRANT ALL PRIVILEGES ON myproject.* TO 'myuser'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

3. **Create a Table**:
   - Log in as the new user:
     ```bash
     mysql -u myuser -p
     ```
   - Use the database and create a table:
     ```sql
     USE myproject;
     CREATE TABLE users (
         id INT AUTO_INCREMENT PRIMARY KEY,
         name VARCHAR(50) NOT NULL,
         email VARCHAR(100) NOT NULL
     );
     EXIT;
     ```

---

## **Step 4: Deploy a PHP Web Application**

1. **Install PHP**:
   ```bash
   sudo yum install php php-mysqlnd -y  # For Amazon Linux
   sudo apt install php libapache2-mod-php php-mysql -y  # For Ubuntu
   ```

2. **Create a Project Directory**:
   ```bash
   sudo mkdir /var/www/html/myproject
   sudo chown -R $USER:$USER /var/www/html/myproject
   cd /var/www/html/myproject
   ```

3. **Create a PHP File**:
   - Create an `index.php` file:
     ```bash
     nano index.php
     ```
   - Add the following PHP code:
     ```php
     <!DOCTYPE html>
     <html lang="en">
     <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0">
         <title>My EC2 Project</title>
     </head>
     <body>
         <h1>Welcome to My EC2 Web Server!</h1>
         <p>This is a simple PHP application connected to MySQL.</p>

         <h2>Add User</h2>
         <form method="POST" action="">
             Name: <input type="text" name="name" required><br>
             Email: <input type="email" name="email" required><br>
             <input type="submit" name="submit" value="Add User">
         </form>

         <h2>User List</h2>
         <?php
         $servername = "localhost";
         $username = "myuser";
         $password = "mypassword";
         $dbname = "myproject";

         // Create connection
         $conn = new mysqli($servername, $username, $password, $dbname);

         // Check connection
         if ($conn->connect_error) {
             die("Connection failed: " . $conn->connect_error);
         }

         // Insert data
         if (isset($_POST['submit'])) {
             $name = $_POST['name'];
             $email = $_POST['email'];

             $sql = "INSERT INTO users (name, email) VALUES ('$name', '$email')";
             if ($conn->query($sql) === TRUE) {
                 echo "<p>User added successfully!</p>";
             } else {
                 echo "<p>Error: " . $sql . "<br>" . $conn->error . "</p>";
             }
         }

         // Fetch data
         $sql = "SELECT id, name, email FROM users";
         $result = $conn->query($sql);

         if ($result->num_rows > 0) {
             echo "<ul>";
             while ($row = $result->fetch_assoc()) {
                 echo "<li>" . $row["name"] . " - " . $row["email"] . "</li>";
             }
             echo "</ul>";
         } else {
             echo "<p>No users found.</p>";
         }

         $conn->close();
         ?>
     </body>
     </html>
     ```

4. **Restart Apache2**:
   ```bash
   sudo systemctl restart httpd  # For Amazon Linux
   sudo systemctl restart apache2  # For Ubuntu
   ```

5. **Verify the Web Application**:
   - Open your browser and navigate to:
     ```
     http://<your-ec2-public-ip>/myproject
     ```
   - You should see the PHP application with a form to add users and a list of users.

---

## **Step 5: (Optional) Automate Deployment with a Script**

1. **Create a Deployment Script**:
   - Create a file called `deploy.sh`:
     ```bash
     nano deploy.sh
     ```
   - Add the following code:
     ```bash
     #!/bin/bash

     # Update the system
     sudo yum update -y

     # Install Apache2, MySQL, and PHP
     sudo yum install httpd mysql-server php php-mysqlnd -y

     # Start and enable services
     sudo systemctl start httpd
     sudo systemctl start mysqld
     sudo systemctl enable httpd
     sudo systemctl enable mysqld

     # Secure MySQL installation
     sudo mysql_secure_installation

     # Create project directory
     sudo mkdir -p /var/www/html/myproject
     sudo chown -R $USER:$USER /var/www/html/myproject

     # Deploy PHP file
     echo '<!DOCTYPE html>
     <html lang="en">
     <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0">
         <title>My EC2 Project</title>
     </head>
     <body>
         <h1>Welcome to My EC2 Web Server!</h1>
         <p>This is a simple PHP application connected to MySQL.</p>

         <h2>Add User</h2>
         <form method="POST" action="">
             Name: <input type="text" name="name" required><br>
             Email: <input type="email" name="email" required><br>
             <input type="submit" name="submit" value="Add User">
         </form>

         <h2>User List</h2>
         <?php
         $servername = "localhost";
         $username = "myuser";
         $password = "mypassword";
         $dbname = "myproject";

         // Create connection
         $conn = new mysqli($servername, $username, $password, $dbname);

         // Check connection
         if ($conn->connect_error) {
             die("Connection failed: " . $conn->connect_error);
         }

         // Insert data
         if (isset($_POST['submit'])) {
             $name = $_POST['name'];
             $email = $_POST['email'];

             $sql = "INSERT INTO users (name, email) VALUES ('$name', '$email')";
             if ($conn->query($sql) === TRUE) {
                 echo "<p>User added successfully!</p>";
             } else {
                 echo "<p>Error: " . $sql . "<br>" . $conn->error . "</p>";
             }
         }

         // Fetch data
         $sql = "SELECT id, name, email FROM users";
         $result = $conn->query($sql);

         if ($result->num_rows > 0) {
             echo "<ul>";
             while ($row = $result->fetch_assoc()) {
                 echo "<li>" . $row["name"] . " - " . $row["email"] . "</li>";
             }
             echo "</ul>";
         } else {
             echo "<p>No users found.</p>";
         }

         $conn->close();
         ?>
     </body>
     </html>' > /var/www/html/myproject/index.php

     # Restart Apache2
     sudo systemctl restart httpd
     ```

2. **Make the Script Executable**:
   ```bash
   chmod +x deploy.sh
   ```

3. **Run the Script**:
   ```bash
   ./deploy.sh
   ```

---

## **Step 6: Clean Up**

- **Stop or Terminate the EC2 Instance**:
  - If you're done with the project, stop or terminate the EC2 instance to avoid unnecessary charges.

---

This project demonstrates how to set up an EC2 instance, install and configure Apache2 and MySQL, and deploy a PHP web application that interacts with the database. You can expand this project by adding user authentication, a backend framework, or CI/CD pipelines for automated deployments.
