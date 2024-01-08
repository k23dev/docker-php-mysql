#FROM php:5.6.40-apache-jessie
FROM php:8.3.2RC1-apache-bullseye

# Set archive repository of debian jessie
ADD sources.list /etc/apt/

# Update APT
RUN apt-get update -qq

# Set force-yes for all apt-get install
RUN echo "APT::Get::force-yes \"true\";" > /etc/apt/apt.conf.d/90forceyes

# Basic software
RUN apt-get install -y -qq \
    unzip \
    git \
    curl \
    build-essential \
    vim

# Basic extension for PHP
RUN apt-get install -y -qq  \
    libmcrypt-dev \
    && docker-php-ext-install mcrypt

# Install support for GD
RUN apt-get install -y -qq \
    libfreetype6-dev \
    libpng12-dev \
    libjpeg-dev \
    libpng-dev \
    && docker-php-ext-configure gd \
        --enable-gd-native-ttf \
        --with-freetype-dir=/usr/include/freetype2 \
        --with-png-dir=/usr/include \
        --with-jpeg-dir=/usr/include \
    && docker-php-ext-install gd \
    && docker-php-ext-enable gd

# Install support for MySQL
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Install support for Sybase
RUN apt-get install -y -qq freetds-dev \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.a /usr/lib/libsybdb.a \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.so /usr/lib/libsybdb.so \
    && docker-php-ext-install pdo pdo_dblib

# Apache: Configure site for laravel
# RUN mkdir -p /var/www/laravel/public/
# RUN chown -R www-data:www-data /var/www
# ADD laravel.conf  /etc/apache2/sites-available/
# RUN find /etc/apache2/sites-enabled/ -type l -exec rm '{}' +
# RUN a2ensite laravel

# PHP: Prepare php.ini
RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini
RUN sed -i 's/;date.timezone =/date.timezone = "America\/Argentina\/Buenos_Aires"/' /usr/local/etc/php/php.ini

# PHP: Set error_reporting = E_ALL (disabled, do its by default)
#RUN sed -i 's/;error_reporting =/error_reporting = E_ALL/' /usr/local/etc/php/php.ini
# PHP: Set display_errors = On (disabled, do its by default)
#RUN sed -i 's/;display_errors =/display_errors = On/' /usr/local/etc/php/php.ini

# Apache: Enable mod rewrite and restart to apply changes
RUN a2enmod rewrite
RUN service apache2 restart

# Install Composer
RUN curl -sS https://getcomposer.org/installer -o composer-setup.php
RUN php composer-setup.php --quiet --filename=composer --install-dir=/usr/local/bin
RUN php -r "unlink('composer-setup.php');"


# Copiar resources
WORKDIR /var/www
