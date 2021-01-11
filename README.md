## Features

 - EC2
 - Docker
 - Wordpress
 - Mysql
 - Docker network

## Prerequisites

 - AWS key pair

## docker.yml

Creates an Ubuntu ec2 instance
Ansible will then create a wordpress container and mysql container using docker bridge network in Ubuntu instance

---
Docker volume is created

    - name: Create Docker volume
      docker_volume:
        name: wpdata

and is mounted to wordpress container using

    volumes:
      - wpdata:/usr/local/apache2/htdocs/

---

Docker bridge network is created

    - name: Creating a nework for Wordpress and MySQL
      docker_network:
         name: wpdb

wordpress and mysql containers are connected to network 

    networks:
      - name: wpdb
---

## .env files

    mysql.env
    wordpress.env

files are used to pass the environment variables required for the wordpress container and mysql container.

    $ cat mysql.env 
    MYSQL_ROOT_PASSWORD=admin123
    MYSQL_DATABASE=wp
    MYSQL_USER=wpuser
    MYSQL_PASSWORD=wpuser123

    $ cat wordpress.env 
    WORDPRESS_DB_HOST=mysql
    WORDPRESS_DB_USER=wpuser
    WORDPRESS_DB_PASSWORD=wpuser123
    WORDPRESS_DB_NAME=wp

Ansible passes environment variables using .env file to docker container:

    env_file: /home/ubuntu/mysql.env
    env_file: /home/ubuntu/wordpress.env

---

### variable.vars

used to pass the variables to Ansible

    ---
    wordpress_name: "wordpress"
    wordpress_image: "wordpress:latest"
    mysql_name: "mysql"
    mysql_image: "mysql:latest"
    aws_access_key_id: "<aws_access_key>"
    aws_secret_access_key: "<aws_secret_key>"

and is called in the Ansible:

    vars_files:
     - variable.vars
