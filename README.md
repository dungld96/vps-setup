# vps-setup

#cấu hình nhiều domain trên apache2

sudo mkdir -p /var/www/mentor.youth.com.vn/html
sudo chown -R $USER:$USER /var/www/mentor.youth.com.vn/html
sudo chmod -R 755 /var/www/mentor.youth.com.vn

sudo gedit /var/www/mentor.youth.com.vn/html/index.php
sudo gedit /etc/apache2/sites-available/mentor.youth.com.vn.conf

<VirtualHost *:80>
    ServerAdmin admin@mentor.youth.com.vn
    ServerName mentor.youth.com.vn
    ServerAlias www.mentor.youth.com.vn
    DocumentRoot /var/www/mentor.youth.com.vn/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

sudo a2ensite mentor.youth.com.vn.conf
sudo systemctl reload apache2
sudo a2dissite 000-default.conf
sudo systemctl restart apache2

#server looxi 404
##có thể do chưa bật module rewrite hoặc 1 module nào khác

#cài ssl
##Bật hỗ trợ SSL cho Apache bằng lệnh:
sudo a2enmod ssl
##Sau khi bật, khởi động lại Apache để có hiệu lực
sudo service apache2 restart

sudo mkdir /etc/apache2/ssl

#phân quyền đọc ghi cho thư mục project
chown -Rf www-data.www-data /var/www/mentor.youth.com.vn/html/

#phân tất cả quyền cho tất cả thư mục ở 1 folder
sudo find /var/www/mentor.youth.com.vn/html -type d -exec chmod 777 {} \;

#phân tất cả quyền cho tất cả file ở 1 folder
sudo chmod -R 777 /var/www/mentor.youth.com.vn/html

#remove php v 7.3
sudo apt-get purge php7.3-common

#cài đặt php 7.3
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
apt install php7.3 php7.3-cli php7.3-common

#tránh lỗi extension php, cài các extension hay dùng nhất
apt install php-pear php7.3-curl php7.3-dev php7.3-gd php7.3-mbstring php7.3-zip php7.3-mysql php7.3-xml php7.3-fpm libapache2-mod-php7.3 php7.3-imagick php7.3-recode php7.3-tidy php7.3-xmlrpc php7.3-intl

#install composer
https://linuxize.com/post/how-to-install-and-use-composer-on-ubuntu-18-04/

#install git
sudo apt install git-all




#Tạo ssh key và add vào tài khoản git
##Kiểm tra xem đã tồn tại ssh key
ls ~/.ssh

##Tạo ssh key
ssh-keygen -t rsa -C "your_email@example.com"

##show ssh key
cat ~/.ssh/id_rsa.pub


#install mysql
https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04


#nginx config: 

server {
	listen 443 ssl http2;
	server_name gw.vncss.net www.gw.vncss.net;
	root /home/ubuntu/web/client/build;
        index index.html index.htm index.nginx-debian.html;
	location / {
	  try_files $uri /index.html;
	}
	# SSL
    ssl_certificate /etc/letsencrypt/live/gw.vncss.net/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/gw.vncss.net/privkey.pem; # managed by Certbot
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
	ssl_prefer_server_ciphers on; 
	ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
}

server {
    if ($host = www.gw.vncss.net) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = gw.vncss.net) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    listen [::]:80;

    server_name gw.vncss.net www.gw.vncss.net;

    return 302 https://$server_name$request_uri;

}
