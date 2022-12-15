FROM php:8.1.13-fpm-alpine

ARG USER=http
ARG UID=1000
ARG GID=$UID

WORKDIR /src

RUN apk add --no-cache --virtual .build-deps $PHPIZE_DEPS

RUN apk add --no-cache --virtual .run-deps \
    libcap \
    mysql-client \
    nodejs \
    npm \
    libjpeg-turbo-dev \
    libpng-dev \
    libxml2-dev \
    libzip-dev \
    freetype-dev \
    icu-dev \
    oniguruma-dev \
    imagemagick-dev \
    zlib-dev \
    jpegoptim \
    optipng \
    pngquant \
    gifsicle \
    ffmpeg \
    && cp "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" \
    && php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && pecl install apcu \
    && pecl install imagick \
    && pecl install redis \
    && docker-php-ext-enable apcu \
    && docker-php-ext-enable imagick \
    && docker-php-ext-enable opcache \
    && docker-php-ext-enable redis \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install exif \
    && docker-php-ext-install fileinfo \
    && docker-php-ext-install gd \
    && docker-php-ext-install intl \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install simplexml \
    && docker-php-ext-install zip \
    && apk del .build-deps

RUN setcap "cap_net_bind_service=+ep" $(which php) \
    && setcap "cap_net_bind_service=+ep" $(which php-fpm)

COPY www.conf /usr/local/etc/php-fpm.d/www.conf

RUN adduser \
    --disabled-password \
    --gecos "" \
    --no-create-home \
    --uid "$UID" \
    "$USER"

RUN mkdir -p /src \
    && chown $UID:$GID /src
