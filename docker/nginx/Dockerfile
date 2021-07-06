FROM ubuntu
LABEL maintainer="yaand <ya.androidapp@gmail.com>"       org.opencontainers.image.authors="yaand <ya.androidapp@gmail.com>"       org.opencontainers.image.url="https://github.com/YA-androidapp/Docker-Cheatsheet"       org.opencontainers.image.documentation="https://github.com/YA-androidapp/Docker-Cheatsheet"       org.opencontainers.image.source="https://github.com/YA-androidapp/Docker-Cheatsheet/blob/main/Dockerfile"       org.opencontainers.image.version="1.0.0"

ENV DB_HOST="db"     DB_PORT="3306"     DB_USER="dbuser"     DB_PASSWD="dbpass"     DB_DATABASE="dbname"

RUN apt-get update && apt-get install -y nginx
ADD index.html /var/www/html/
EXPOSE 80
ENTRYPOINT ["nginx", "-g", "daemon off;"]
