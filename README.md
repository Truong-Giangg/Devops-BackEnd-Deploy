# Devops-BackEnd-Deploy
Add user and permission:
```bash
adduser shoeshop
chown -R shoeshop. shoeshop
chmod -R 750 shoeshop
```
Install correct java & maven version
```bash
apt install openjdk-17-jdk openjdk-17-jre
apt install maven -y
```
Install database & config
```bash
apt install mariadb-server
```
Config database: change local ip to 0.0.0.0 so others can connect to DB
```bash
netstat -tlpun
systemctl stop mariadb
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/mysql/mariadb.conf.d/50-server.cnf
systemctl restart mariadb
netstat -tlpun
```
Create database and user for that database
```bash
mysql -u root
show databases;
create database shoeshop;
CREATE USER 'shoeshop'@'%' IDENTIFIED BY 'shoeshop';
GRANT ALL PRIVILEGES ON shoeshop.* TO 'shoeshop'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```
Login new database
```bash
mysql -h 192.168.184.130 -p 3306 -u shoeshop -p
use shoeshop;
source /home/giang/projects/shoeshop/shoe_shopdb.sql
show tables;
```
Modify project database configuration file
```bash
sed -i 's/<address_server>/192.168.184.130/g' src/main/resources/application.properties
sed -i 's/<port>/3306/g' src/main/resources/application.properties
sed -i 's/<database_name>/shoeshop/g' src/main/resources/application.properties
sed -i 's/<username>/shoeshop/g' src/main/resources/application.properties
sed -i 's/<password>/shoeshop/g' src/main/resources/application.properties
```
Build and run
```bash
mvn clean install
java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
nohub java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar 2>&1 &
```
