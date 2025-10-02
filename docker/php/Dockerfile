FROM composer:2 as composer-stage
FROM php:8.4-fpm-alpine

WORKDIR /var/www/html

COPY --from=composer-stage /usr/bin/composer /usr/bin/composer
COPY ./app .
# Instalando extensões necessárias do PHP
RUN apk add --no-cache mysql-client postgresql-dev msmtp perl wget procps shadow libzip libpng libjpeg-turbo libwebp freetype icu git

RUN apk add --no-cache --virtual build-essentials \
    icu-dev icu-libs zlib-dev g++ make automake autoconf libzip-dev \
    libpng-dev libwebp-dev libjpeg-turbo-dev freetype-dev postgresql-dev && \
    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg --with-webp && \
    docker-php-ext-install gd mysqli pdo_mysql intl opcache bcmath exif zip pdo_pgsql pgsql && \
    apk del build-essentials && rm -rf /usr/src/php*

RUN apk add --no-cache pcre-dev $PHPIZE_DEPS \
        && pecl install redis \
        && docker-php-ext-enable redis.so

RUN addgroup -g 1000 drupal && adduser -G drupal -g drupal -s /bin/sh -D drupal

RUN chown -R drupal /var/www/html
#first the Dockerfile creates an image on top of the php:8.2-fpm-alpine .
#then the WORKDIR command sets the working directory to /var/www/html.
USER drupal

EXPOSE 9000

CMD ["php-fpm"]
