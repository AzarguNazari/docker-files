# docker-files

# Dockerfile Commands
```
RUN echo "hello world"    # to run a bash commmand
ADD source destination    # add resource from localhost to container
COPY source destination   # copy from localhost to container
FROM imagename:version    # pull image from docker registry
ENV  foo something        # declare environment on dockerfile
WORKDIR /workdirectory    # define work directory
EXPOSE port               # expose sepcific port
LABEL label-name          # A LABEL is a key-value pair to give metadata information for image
STOPSIGNAL signal           # The STOPSIGNAL instruction sets the system call signal that will be sent to the container to exit.  
USER user                 # define user
VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"]             # define volume for container
CMD command               # run specific command
MAINTAINER maintainer-name # give name of container maintainer
ARG argument value        # give argument
ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]   # An ENTRYPOINT allows you to configure a container that will run as an executable.
SHELL ["powershell", "-command"]                          #The SHELL instruction allows the default shell used for the shell form of commands to be overridden.
```

# Java Dockerfile
```
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"
```

# Spring Boot dockerfile
```
FROM openjdk:8-jdk-alpine
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring
ARG DEPENDENCY=target/dependency
COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY ${DEPENDENCY}/META-INF /app/META-INF
COPY ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","hello.Application"]
```


## Apache Dockerfile
```
FROM ubuntu:12.04

MAINTAINER Hazar Gul Nazari version: 0.1

RUN apt-get update && apt-get install -y apache2 && apt-get clean && rm -rf /var/lib/apt/lists/*

ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

EXPOSE 80

CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
```

## NodeJs dockerfile
```

FROM ubuntu
MAINTAINER Hazar Gul Nazari

RUN apt-get install -y software-properties-common python
RUN add-apt-repository ppa:chris-lea/node.js
RUN echo "deb http://us.archive.ubuntu.com/ubuntu/ precise universe" >> /etc/apt/sources.list
RUN apt-get update
RUN apt-get install -y nodejs
#RUN apt-get install -y nodejs=0.6.12~dfsg1-1ubuntu1
RUN mkdir /var/www

ADD app.js /var/www/app.js

CMD ["/usr/bin/node", "/var/www/app.js"] 
```

## PHP application dockerize
```
FROM php:7.0-apache
COPY src/ /var/www/html
EXPOSE 80
```

## Laravel Application dockerize
```
# Copy composer.lock and composer.json
COPY composer.lock composer.json /var/www/

# Set working directory
WORKDIR /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install extensions
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl
RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
RUN docker-php-ext-install gd

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

# Change current user to www
USER www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]
```
