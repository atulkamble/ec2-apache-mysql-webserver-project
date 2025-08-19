# 📦 EC2 Apache2 MySQL Web App Deployment

🚀 Deployed a **full-stack PHP web application** on AWS by integrating an **EC2 instance**, **Apache2 web server**, and **MySQL database**. This project demonstrates setting up a scalable web server, configuring a secure database, and deploying a dynamic web application that allows user interaction with persistent storage.

---

## 🔑 **Key Highlights**

* ✅ **Provisioned EC2 Instance** (Ubuntu) with proper security groups (HTTP, HTTPS, SSH, MySQL).
* ✅ Installed and configured **Apache2, PHP, and MySQL** on the instance.
* ✅ Secured and initialized a **MySQL database** with a `users` table for dynamic data storage.
* ✅ Built a **PHP web app** with a simple UI to add and display users from the MySQL database.
* ✅ Ensured persistence by connecting **frontend forms → backend PHP logic → MySQL database**.
* ✅ Validated deployment via browser using EC2 public IP.

---

## ⚙️ **Tech Stack**

* **AWS EC2** (Compute)
* **Apache2** (Web Server)
* **MySQL** (Database)
* **PHP** (Application Layer)
* **Linux (Ubuntu)**

---

## 📸 **Project Flow**

1. **EC2 Setup** → Launch Ubuntu instance + configure security groups
2. **Web Server & DB Installation** → Apache2, PHP, MySQL installation & configuration
3. **Database Setup** → Create database, user, and `users` table
4. **App Deployment** → Deploy `index.php` with forms and database connectivity
5. **Testing** → Add users & fetch results in real-time from the MySQL database

---

## 🎯 **Outcome**

This project showcases **end-to-end deployment** of a dynamic PHP application on AWS with full **LAMP stack integration**. It highlights **cloud infrastructure skills, server setup, database management, and web app deployment** — all on a scalable and secure environment.


## 📦 Project Title: EC2 Apache2 MySQL Web App Deployment

Full project with an **EC2 instance**, **Apache2 server**, and **MySQL database** involves setting up a web server, a database, and a web application that interacts with the database.

## 📌 Step 1: Set Up an EC2 Instance

1. **Log in to AWS Console**

   - Go to the [AWS Management Console](https://aws.amazon.com/console/).
   - Navigate to the **EC2** service.

2. **Launch an EC2 Instance**

   - Click **Launch Instance**.
   - Choose an Amazon Machine Image (AMI), such as **Ubuntu**.
   - Select an instance type (e.g., `t2.micro` for free tier).
   - Configure instance details (default settings are fine).
   - Add storage (default 8GB is sufficient).
   - Add tags (optional).
   - Configure the security group:
     - Allow **HTTP (80)**, **HTTPS (443)**, **SSH (22)**, and **MySQL (3306)**.
   - Review and launch the instance.
   - Create and download a key pair (`webserver.pem`) for SSH access.

3. **Connect to the EC2 Instance**

   ```bash
   cd Downloads
   chmod 400 webserver.pem
   ssh -i "webserver.pem" ubuntu@<your-ec2-public-ip>
   ```

## 📌 Step 2: Install Apache2, PHP, and MySQL

1. **Update the System**

   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo -i passwd
   ```

2. **Install PHP**

   ```bash
   sudo apt install php libapache2-mod-php php-mysql -y
   ```

3. **Install Apache2**

   ```bash
   sudo apt install apache2 -y
   ```

4. **Install MySQL**

   ```bash
   sudo apt install mysql-server -y
   mysql --version
   ```

5. **Start and Enable Services**

   ```bash
   sudo systemctl start apache2
   sudo systemctl enable apache2
   sudo systemctl status apache2

   sudo systemctl start mysql
   sudo systemctl enable mysql
   sudo systemctl status mysql
   ```

6. **Secure MySQL Installation**

   ```bash
   sudo mysql_secure_installation
   ```

7. **Verify Apache Installation**

   * Open browser: `http://<your-ec2-public-ip>`
   * You should see Apache's default landing page.

## 📌 Step 3: Set Up MySQL Database

1. **Log in to MySQL**

   ```bash
   sudo mysql -u root -p
   ```

2. **Create a Database and User**

   ```sql
   CREATE DATABASE myproject;
   CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypassword';
   GRANT ALL PRIVILEGES ON myproject.* TO 'myuser'@'localhost';
   FLUSH PRIVILEGES;
   ```

3. **Create a Table**

   ```sql
   USE myproject;
   CREATE TABLE users (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(50) NOT NULL,
       email VARCHAR(100) NOT NULL
   );
   EXIT;
   ```


## 📌 Step 4: Deploy a PHP Web Application

1. **Create Project Directory**

   ```bash
   sudo mkdir /var/www/html/myproject
   sudo chown -R $USER:$USER /var/www/html/myproject
   sudo chmod 755 /var/www/html/
   cd /var/www/html
   sudo rm index.html
   sudo touch index.php
   sudo nano index.php
   ```

2. **Add PHP Code**

   Paste the following code into `index.php`:

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

       $conn = new mysqli($servername, $username, $password, $dbname);

       if ($conn->connect_error) {
           die("Connection failed: " . $conn->connect_error);
       }

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

3. **Restart Apache**

   ```bash
   sudo systemctl restart apache2
   ```

4. **Test Application**

   * Visit `http://<your-ec2-public-ip>` in a browser.
   * Use the form to add users and view the user list.


## 📌 Step 5: Clean Up

* **Stop/Terminate EC2 Instance** when the project is complete to avoid charges.


## ✅ Conclusion

This project demonstrates how to set up an **EC2 instance**, install **Apache2** and **MySQL**, and deploy a **PHP web application** connected to a database.

