# laravel-on-docker
laravel on docker for developers

## Step1
First of all please install docker based on your operating system.

[Docker on Windows](https://docs.docker.com/docker-for-windows/install/ "Go to docker on desktop installation guide page")

[Docker on Ubuntu](https://docs.docker.com/engine/install/ubuntu/ "Go to docker on ubuntu installation guide page")

[Docker on CentOS](https://docs.docker.com/engine/install/centos/ "Go to docker on centos installation guide page")

[Docker on MacOS](https://docs.docker.com/docker-for-mac/install/ "Go to docker on macos installation guide page")

## Step2
Start Docker service on your system and create [docker hub account](https://hub.docker.com/signup)
### note: on windows docker desktop please use powershell or gitbash

## Step3
Go on your laravel project root directory and create file with name of Dockerfile(Captal D) and .dockerignore

## Step4
Add following content on your Dockerfile
```
FROM mrtshoot/php:7.2-fpm
Maintainer Mrtshoot

# Copy composer.lock and composer.json
COPY ./composer.lock ./composer.json /var/www/

# Set working directory
WORKDIR /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    iputils-ping \
    zip \
    wget \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    supervisor 
 
# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install extensions
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl bcmath
RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
RUN docker-php-ext-install gd
RUN pecl install -o -f redis \
&&  rm -rf /tmp/pear \
&&  docker-php-ext-enable redis

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

#Add SSH Server and Requirements
#RUN apt update && apt install openssh-server -y
#RUN mkdir /home/www/.ssh && mkdir /home/www/.composer
#RUN chown -R www:www /home/www/
#on this release we dont need ssh access.my developers understand docker knowledges

# Change current user to www
USER www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]
```


