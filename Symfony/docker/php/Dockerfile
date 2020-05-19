FROM php:7.4-fpm

LABEL maintainer="Mael <mael.constantin@gmail.com>"

ARG USER_ID
ARG GROUP_ID

# Mise à jour de la distribution
RUN apt-get update

# Installation des libs
RUN apt-get install -y wget gnupg git unzip libzip-dev libicu-dev \
    libfreetype6-dev libjpeg62-turbo-dev libpng-dev software-properties-common

# Installation des extensions PHP
RUN docker-php-ext-install pdo_mysql mysqli zip intl opcache

# Installation de Xdebug & APCU
RUN pecl install xdebug apcu

# Installation de GD & configuration
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

# Activation et configuration des extensions
RUN docker-php-ext-configure intl \
    && docker-php-ext-configure opcache --enable-opcache \
    && docker-php-ext-enable xdebug  \
    && docker-php-ext-enable apcu

# Installation de composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Installation de l'outil Symfony
RUN wget https://get.symfony.com/cli/installer -O - | bash \
    && mv /root/.symfony/bin/symfony /usr/local/bin/symfony

ADD xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Must be modified for each project
WORKDIR /var/www/html/projectname

# Must be modified for each project
RUN chown -R $USER_ID:$GROUP_ID /var/www/html/projectname

RUN groupadd -f -g $GROUP_ID user
RUN useradd -u $USER_ID -g $GROUP_ID user
RUN mkdir /home/user && chmod 0770 /home/user && chown -R $USER_ID:$GROUP_ID /home/user && chown -R $USER_ID:$GROUP_ID /var/www
USER user

EXPOSE 9000