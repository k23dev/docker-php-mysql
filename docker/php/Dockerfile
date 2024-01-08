#FROM php:5.6.40-apache-jessie
FROM php:8.3.2RC1-apache-bullseye

# Set archive repository of debian jessie
ADD sources.list /etc/apt/

# Update APT
RUN apt update -qq

# Set force-yes for all apt install
RUN echo "APT::Get::force-yes \"true\";" > /etc/apt/apt.conf.d/90forceyes

# Basic software
RUN apt install -y -qq \
    unzip \
    git \
    curl \
    build-essential \
    vim

# Install support for MySQL
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Install support for Sybase
RUN apt install -y -qq freetds-dev \
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
