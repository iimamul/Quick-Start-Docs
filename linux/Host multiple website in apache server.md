
# Host multiple website in linux vps

A brief description of how to Host multiple website in
apache server in linux machine

## Installation

### 1. Install apache server

```bash
sudo apt install apache2
```
### 2. Make directory for sites

```bash
sudo mkdir -p /var/www/codewithharry.com/
sudo mkdir -p /var/www/programmingwithharry.com/
```
### 3. Transfer site content with filezilla(port:22)

### 4. Create a new config file in following folder for individual sites

```bash
FOR ATTACH A DOMAIN
  sudo vim /etc/apache2/sites-available/codewithharry.com.conf

FOR HOST IN DIFFERENT PORTS OF IP ADDRESS
  sudo vim /etc/apache2/sites-enabled/codewithharry.com.conf
  sudo vim /etc/apache2/sites-available/programmingwithharry.com.conf
```
### 5. Add these virtual config in these file(change port number for differnt sites)
**codewithharry.com.conf**
```bash
<VirtualHost *:88>
  ServerName codewithharry.com
  ServerAdmin yourPublicEmail@email.com
  DocumentRoot /var/www/codewithharry.com
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
**programmingwithharry.com.conf**
```bash
<VirtualHost *:89>
  ServerName programmingwithharry.com
  ServerAdmin yourPublicEmail@email.com
  DocumentRoot /var/www/programmingwithharry.com
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
### 6. Check available port listening 

```bash
ss -nat
```
### 7. Open ports.conf to open a new port

```bash
sudo nano /etc/apache2/ports.conf
```
### 8. Add new ports

```bash
Listen 80
Listen 88
Listen 89
```

### 9. Allow port through ubuntu firewall (ufw)

```bash
sudo ufw allow 88
sudo ufw allow 89
```
### 10. Restart apache server

```bash
sudo service apache2 restart
```
## Acknowledgements

 - [codewith harry youtube](https://youtu.be/BEGtS3XE6j4)
 - [blogpost](https://www.codewithharry.com/blogpost/host-multiple-websites-ubuntu-vps/)
 

