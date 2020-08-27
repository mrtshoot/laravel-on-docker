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
Go on your laravel project root directory and create file with name of Dockerfile(Captal D)

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
## Step5
Create three bash file on root of project
add following content on each bash file

#### up.sh
```
#!/bin/bash
docker build -t $1:latest .
docker run -d  -p $2 --name $1 $1:latest
```
with up.sh you can build and run your project on docker and see result of your project configuration.you should run up.sh with following structure

```
./up.sh [image-name] [host_port]:9000
```
for example
```
./up.sh myimagename 8000:9000
```

becareful that container port must be unchanged.

#### down.sh
```
#!/bin/bash
docker rm -f $1
```

if you test your code and everything is ok you should down your container and run push.sh script, otherwise you should check your bug and fix it then run up.sh bash script again.you should run down.sh with following structure

```
./down.sh [image-name]
```


#### push.sh
```
#!/bin/bash
registry=$2
docker tag $1:latest $registry/$1:latest
docker push $registry/$1:latest
```

## Step6
git push to your origin tmaster and after everything is ok go to master.

## Author
Mrtshoot - mrtshoot@gmail.com
