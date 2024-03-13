ARG DOCKER_PHP_VERSION

FROM php:${DOCKER_PHP_VERSION}-fpm-alpine

ARG TZ='UTC'

# https://wiki.alpinelinux.org/wiki/Setting_the_timezone
RUN echo "${TZ}" && apk --update add tzdata && \
    cp /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    apk del tzdata

RUN apk add --no-cache bash mysql-client msmtp perl wget procps shadow libzip libpng libjpeg-turbo libwebp freetype icu \
    libintl patch acl git gettext-dev libcurl libxml2-dev openssh-client pcre-dev su-exec build-base oniguruma-dev \
    gmp-dev postgresql-dev gettext gettext-dev

RUN curl -sSLf \
        -o /usr/local/bin/install-php-extensions \
        https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions && \
    chmod +x /usr/local/bin/install-php-extensions

RUN install-php-extensions \
    soap \
    zip \
    curl \
    bcmath \
    exif \
    gd \
    iconv \
    intl \
    mbstring \
    opcache \
    pdo_pgsql \
    imagick \
    mongodb \
    xdebug \
    memcached \
    amqp \
    redis \
    json


RUN apk add --update --no-cache --virtual build-essentials \
    icu-dev icu-libs zlib-dev g++ make automake autoconf libzip-dev \
    nodejs npm \
    libpng-dev libwebp-dev libjpeg-turbo-dev freetype-dev && \
    docker-php-ext-configure gettext && \
    docker-php-ext-configure pcntl --enable-pcntl && \
    docker-php-ext-install gettext && \
    docker-php-ext-install pcntl && \
    docker-php-ext-install xml && \
    docker-php-ext-install gmp && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install sockets && \
    apk del build-essentials && rm -rf /usr/src/php*

RUN apk add --no-cache supervisor htop

RUN mkdir -p /var/log/supervisor

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]

RUN apk add $PHPIZE_DEPS libsmbclient samba-dev
RUN pecl install smbclient
RUN docker-php-ext-enable smbclient
RUN php --ri smbclient

RUN apk add --no-cache $PHPIZE_DEPS
RUN pecl install -o -f redis mongodb \
    &&  rm -rf /tmp/pear

RUN pecl install rar && \
    docker-php-ext-enable rar

# Clean
RUN rm -rf /var/cache/apk/* && docker-php-source delete

USER root

RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer
RUN /usr/local/bin/composer self-update

USER www-data

WORKDIR /var/www/

