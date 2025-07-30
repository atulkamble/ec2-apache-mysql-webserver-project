// create rds 
database
admin
Admin123

// create ec2
amazon linux | t2.micro | key.pem 

SG: http-80, https-443, ssh-22, mysql-3306

cd Downloads
sudo yum install git tree httpd php -y
sudo yum update -y 
sudo yum install httpd -y 
sudo yum install php-mysqlnd
sudo systemctl start httpd 
sudo systemctl enable httpd 
sudo chmod 755 /var/www/html 
sudo cd /var/www/html 
sudo git clone https://github.com/atulkamble/ec2-apache-mysql-webserver-project.git
sudo cp /var/www/html/ec2-apache-mysql-webserver-project/* /var/www/html
sudo rm -rf ec2-apache-mysql-webserver-project

sudo yum install mariadb1011-server-utils.x86_64

sudo -i passwd 

sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo mysql_secure_installation

sudo mysql -u root -p

sudo yum update -y
