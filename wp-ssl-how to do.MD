
# 创建nginx 代理相关文件

mkdir nginx-proxy && cd nginx-proxy
git clone https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion.git .

# 设置环境变量

mv .env.sample .env && vim .env

# 执行

 ./start.sh

# 创建wordpress  ssl

mkdir wordpress-letsencrypt && cd wordpress-letsencrypt

git clone https://github.com/evertramos/docker-wordpress-letsencrypt.git .

 vim docker-compose.yml


# 编辑内容

version: '3'

services:
   db:
       MYSQL_DATABASE: myweb-db
       MYSQL_USER: root
       MYSQL_PASSWORD: mywebpwd

   wordpress:
     depends_on:
       - db
     container_name: myweb-wp-container
     image: wordpress:latest
     restart: unless-stopped
     volumes:
       - ${WP_CORE}:/var/www/html
       - ${WP_CONTENT}:/var/www/html/wp-content
       - ./conf.d/php.ini:/usr/local/etc/php/conf.d/php.ini
     environment:
       WORDPRESS_DB_HOST: myweb-db-container:3306
       WORDPRESS_DB_NAME: myweb-db
       WORDPRESS_DB_USER: root
       WORDPRESS_DB_PASSWORD: mywebpwd
       WORDPRESS_TABLE_PREFIX: wp_
       VIRTUAL_HOST: myweb.com, www.myweb.com
       LETSENCRYPT_HOST: myweb.com, www.myweb.com
       LETSENCRYPT_EMAIL: info@myweb.com
    
   phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    external_links:
      - db
    ports:
      - 8181:80
    environment:
      MYSQL_USERNAME: root
      MYSQL_ROOT_PASSWORD: mywebpwd

networks:
    default:
       external:
         name: webproxy


# 设置wp 环境

mv .env.sample .env && vim .env

# .env file to set up your wordpress site

#
# Network name
# 
# Your container app must use a network conencted to your webproxy 
# https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion
#
NETWORK=webproxy

#
# Database Container configuration
# We recommend MySQL or MariaDB - please update docker-compose file if needed.
#
CONTAINER_DB_NAME=myweb-db-container

# Path to store your database
#DB_PATH=/path/to/your/local/database/folder
DB_PATH=/var/lib/mysql

# Root password for your database
MYSQL_ROOT_PASSWORD=mywebpwd

# Database name, user and password for your wordpress
MYSQL_DATABASE=myweb-db-container
MYSQL_USER=root
MYSQL_PASSWORD=mywebpwd
#
# Wordpress Container configuration
#
CONTAINER_WP_NAME=myweb-wp-container

# Max Log File Size
#LOGGING_OPTIONS_MAX_SIZE=200k

# Path to store your wordpress files
#WP_CORE=/path/to/your/wordpress/core/files
#WP_CONTENT=/path/to/your/wordpress/wp-content
WP_CORE=/var/www/html
WP_CONTENT=/var/www/html/wp-content


# Table prefix
WORDPRESS_TABLE_PREFIX=wp_

# Your domain (or domains)
DOMAINS=myweb.com,www.myweb.com

# Your email for Let's Encrypt register
LETSENCRYPT_EMAIL=info@myweb.com

//8。执行项目

docker-compose up -d

                                                                                                                                             91,1          Bot


