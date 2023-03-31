FROM alpine:3.16.5

ARG VERSION=v1.7.0

ENV GID=991 \
    UID=991 \
    UPLOAD_MAX_SIZE=50M \
    PHP_MEMORY_LIMIT=256M \
    OPCACHE_MEMORY_LIMIT=256 \
    DB_HOST=flarum_mysql_db \
    DB_USER=flarum \
    DB_NAME=flarum \
    DB_PORT=3306 \
    FLARUM_TITLE=Docker-Flarum \
    DEBUG=false \
    LOG_TO_STDOUT=false \
    GITHUB_TOKEN_AUTH=false \
    FLARUM_PORT=8888

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
  && apk add --no-cache bash \
    curl \
    git \
    icu-data-full \
    libcap \
    nginx \
    php8 \
    php8-ctype \
    php8-curl \
    php8-dom \
    php8-exif \
    php8-fileinfo \
    php8-fpm \
    php8-gd \
    php8-gmp \
    php8-json \
    php8-iconv \
    php8-intl \
    php8-mbstring \
    php8-mysqlnd \
    php8-opcache \
    php8-pecl-apcu \
    php8-openssl \
    php8-pdo \
    php8-pdo_mysql \
    php8-phar \
    php8-session \
    php8-tokenizer \
    php8-xmlwriter \
    php8-zip \
    php8-zlib \
    su-exec \
    s6 \
  && cd /tmp \
  && sed -i 's/memory_limit = .*/memory_limit = ${PHP_MEMORY_LIMIT}/' /etc/php8/php.ini \
  && curl --progress-bar https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --disable-tls=false \
  && chmod +x /usr/local/bin/composer \
  && mkdir -p /run/php /flarum/app \
  && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
  && COMPOSER_CACHE_DIR="/tmp" composer create-project flarum/flarum:$VERSION /flarum/app \
  && composer clear-cache \
  && rm -rf /flarum/.composer /tmp/* \
  && setcap CAP_NET_BIND_SERVICE=+eip /usr/sbin/nginx

COPY rootfs /
RUN chmod +x /usr/local/bin/* /etc/s6.d/*/run /etc/s6.d/.s6-svscan/*
VOLUME /etc/nginx/flarum /flarum/app/extensions /flarum/app/public/assets /flarum/app/storage/logs
CMD ["/usr/local/bin/startup"]
