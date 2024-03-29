Установить в виртуальную машину или VDS Docker,
настроить набор контейнеров через docker compose по инструкции по ссылке:
https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-docker-compose-ru.
Часть с настройкой certbot и HTTPS опустить, если у вас нет настоящего домена и белого IP.

mkdir wordpress && cd wordpress
mkdir nginx-conf
nano nginx-conf/nginx.conf
server {
        listen 80;
        listen [::]:80;

        server_name example.com www.example.com;
        index index.php index.html index.htm;
        root /var/www/html;

        location ~ /.well-known/acme-challenge {
                allow all;
                root /var/www/html;
        }

        location / {
                try_files $uri $uri/ /index.php$is_args$args;
        }

        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass wordpress:9000;
                fastcgi_index index.php;
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_param PATH_INFO $fastcgi_path_info;
        }

        location ~ /\.ht {
                deny all;
        }

        location = /favicon.ico {
                log_not_found off; access_log off;
        }

        location = /robots.txt {
                log_not_found off; access_log off; allow all;
        }

        location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
                expires max;
                log_not_found off;
        }
}

nano .env

MYSQL_ROOT_PASSWORD=1
MYSQL_USER=ik
MYSQL_PASSWORD=1

git init
nano .gitignore
.env

nano .dockerignore
.env
.git
docker-compose.yml
.dockerignore

nano docker-compose.yml
  GNU nano 6.2                           docker-compose.yml *
      - MYSQL_DATABASE=wordpress
    volumes:
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network
  wordpress:
    depends_on:
      - db
    image: wordpress:5.1.1-fpm-alpine
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=$MYSQL_USER
      - WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD
      - WORDPRESS_DB_NAME=wordpress

    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network

  webserver:
    depends_on:
      - wordpress
    image: nginx:1.15.12-alpine
    container_name: webserver
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d

    networks:
      - app-network
volumes:
  wordpress:
  dbdata:

networks:
  app-network:
    driver: bridge


sudo docker-compose up -d