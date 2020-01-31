FROM php:7.2-fpm-alpine
LABEL maintainer="markus@martialblog.de"
ARG version='3.22.0+200127'
ARG sha256_checksum='61f1d6db93746040dc53af04f9c1c8fd48f18cc523c1f377b25a5c94c8c3e467'

# Install OS dependencies
RUN set -ex; \
        apk add --no-cache --virtual .build-deps \
        freetype-dev \
        libpng-dev \
        libjpeg-turbo-dev \
        openldap-dev \
        imap-dev \
        postgresql-dev && \
        apk add --no-cache netcat-openbsd bash

# Install PHP Plugins
RUN set -ex; \
        docker-php-ext-configure gd --with-freetype-dir=/usr --with-png-dir=/usr --with-jpeg-dir=/usr ; \
        docker-php-ext-configure imap --with-imap-ssl && \
        docker-php-ext-install \
        gd \
        imap \
        ldap \
        mbstring \
        pdo \
        pdo_mysql \
        pdo_pgsql \
        pgsql \
        zip

# Download, unzip and chmod of LimeSurvey
ADD "https://github.com/LimeSurvey/LimeSurvey/archive/${version}.tar.gz" /tmp

RUN set -ex; \
        echo "${sha256_checksum}  /tmp/${version}.tar.gz" | sha256sum -c - && \
        \
        tar xzvf "/tmp/${version}.tar.gz" --strip-components=1 -C /var/www/html/ && \
        \
        rm -rf "/tmp/${version}.tar.gz" \
        /var/www/html/docs \
        /var/www/html/tests \
        /var/www/html/*.md && \
        chown -R www-data:root /var/www/ ; \
        chmod -R g=u /var/www

EXPOSE 9000

COPY entrypoint.sh entrypoint.sh
ENTRYPOINT ["/var/www/html/entrypoint.sh"]
CMD ["php-fpm"]