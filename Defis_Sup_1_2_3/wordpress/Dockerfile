FROM wordpress:6.3-php8.1-apache

# Installation des extensions PHP nécessaires
RUN apt-get update && apt-get install -y \
    mariadb-client \
    libzip-dev \
    zip \
    unzip \
    less \
    redis-tools \
    && docker-php-ext-install zip


# Installation de l'extension Redis pour PHP
RUN pecl install redis \
    && docker-php-ext-enable redis

# Configuration personnalisée de PHP
COPY php.ini /usr/local/etc/php/conf.d/custom.ini

# Installation de WP-CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp
