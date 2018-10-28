FROM php:7.1-fpm
COPY sources.list ./sources.list
COPY imagick-3.4.3.tgz ./imagick-3.4.3.tgz
COPY mongodb-1.5.1.tgz ./mongodb-1.5.1.tgz
COPY apcu-5.1.12.tgz ./apcu-5.1.12.tgz

RUN mv /etc/apt/sources.list /etc/apt/sources.list.backup.1 && mv sources.list /etc/apt/ && apt-get update -y 

RUN apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpq-dev \
        g++ \
        libicu-dev \
        libssl-dev \
        libmcrypt-dev \
        libxml2-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install zip \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install soap \
    && apt-get purge --auto-remove -y g++ \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV PHPREDIS_VERSION php7
RUN curl -L -o /tmp/redis.tar.gz https://github.com/phpredis/phpredis/archive/4.1.1.tar.gz \
&& mkdir /tmp/redis \
&& tar -xf /tmp/redis.tar.gz -C /tmp/redis \
&& rm /tmp/redis.tar.gz \
&& ( \
cd /tmp/redis/phpredis-4.1.1 \
&& phpize \
    && ./configure \
&& make -j$(nproc) \
    && make install \
) \
&& rm -r /tmp/redis \
&& docker-php-ext-enable redis

# install GD and mcrypt
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
    && docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

# install apcu
RUN pecl install -f apcu-5.1.12.tgz && docker-php-ext-enable apcu

#install Imagemagick & PHP Imagick ext
RUN apt-get update && apt-get install -y \
       libmagickwand-dev --no-install-recommends

RUN pecl install -f imagick-3.4.3.tgz && docker-php-ext-enable imagick

# install mongodb ext
#RUN curl -L -o /tmp/mongodb-1.5.1.tar.gz https://github.com/mongodb/mongo-php-driver/releases/download/1.5.1/mongodb-1.5.1.tgz \
RUN pecl install -f mongodb-1.5.1.tgz \
    && docker-php-ext-enable mongodb

RUN docker-php-ext-install opcache 