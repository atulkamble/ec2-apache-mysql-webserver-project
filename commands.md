www.github.com/atulkamble

// aws ec2 | t2 micro | amazon linux 2023 

webserver.pem 

terminal >> powershell 

cd Downloads
chmod 400 webserver.pem
ssh -i "webserver.pem" ec2-user@ec2-54-221-180-88.compute-1.amazonaws.com

Putty - .ppk

EC2 Instance Connect 

//

sudo apt update -y
sudo apt install php -y
sudo apt install php libapache2-mod-php php-mysql -y
sudo -i passwd 

sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
sudo systemctl status apache2

sudo apt install mysql-server -y
mysql --version
sudo mysql -u root -p

OR
sudo mysql 

CREATE DATABASE myproject;
CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON myproject.* TO 'myuser'@'localhost';
FLUSH PRIVILEGES;

use myproject;
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);
EXIT;

sudo chmod 755 /var/www/html/
cd /var/www//html
sudo rm index.html

// check instance-public ip 

http://instance-public-ip
