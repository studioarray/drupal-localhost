FROM php:5.6-apache

MAINTAINER Jens Janson <jens@studioarray.com>

COPY init.sh /

# Enable the Cache Expiration, URL Rewriting and SSL Apache modules.
# Configure and install PHP extensions.
RUN apt-get update && apt-get install -y libjpeg-dev \
    libpng12-dev \
    libpq-dev \
    zlib1g-dev \
    && docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
    && docker-php-ext-install exif \
    gd \
    mbstring \
    mysqli \
    opcache \
    pdo \
    pdo_mysql \
    zip \
    # && apt-get update && apt-get install -y libmagickwand-dev \
    # imagemagick \
    && apt-get update && apt-get install -y curl \
    wget \
    git \
    mysql-client \
    # && pecl install imagick \
    # && echo extension=imagick.so > /usr/local/etc/php/conf.d/ext-imagick.ini \
    && echo "date.timezone = 'Europe/London'" >> /usr/local/etc/php/php.ini \
    && echo "upload_max_filesize = 512M" >> /usr/local/etc/php/php.ini \
    && echo "post_max_size = 512M" >> /usr/local/etc/php/php.ini \
    && echo "memory_limit = 1G" >> /usr/local/etc/php/php.ini \
    && echo "allow_url_include = on" >> /usr/local/etc/php/php.ini \
    && echo "log_errors = on" >> /usr/local/etc/php/php.ini \
    && echo "error_log = /dev/stderr" >> /usr/local/etc/php/php.ini \
    && chmod +x /init.sh

# INSTALLS COMPOSER & DRUSH
RUN php -r "copy('https://getcomposer.org/installer', '/tmp/composer-setup.php');" \
    && php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && composer global require drush/drush:8 \
    && rm /tmp/composer-setup.php

# INSTALLS XDEBUG
# RUN pecl install xdebug && \
#     docker-php-ext-enable xdebug

# Create temp and private dirs
RUN cd /var/www && mkdir temp && mkdir private

# COPY ["conf/php-pecl-xdebug.ini", "/usr/local/etc/php/conf.d/docker-php-pecl-xdebug.ini"]

COPY ["conf/website.conf", "/etc/apache2/sites-available/"]

RUN a2ensite website.conf

RUN groupadd -r -g 501 jens && useradd -r -u 501 -g jens jens

EXPOSE 80
EXPOSE 443

ENTRYPOINT ["/init.sh"]

CMD ["apache2-foreground"]
