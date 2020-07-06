FROM php:7.4.7-cli

RUN apt-get update && apt-get install -y --no-install-recommends \
  vim \
  libzip-dev \
  unzip

# Install PHP Extensions
RUN docker-php-ext-install zip \
  && docker-php-ext-install opcache \
  && docker-php-ext-enable opcache

# Install Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php -r "if (hash_file('SHA384', 'composer-setup.php') === rtrim(file_get_contents('https://composer.github.io/installer.sig'))) { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
  && php composer-setup.php \
  && php -r "unlink('composer-setup.php');" \
  && mv composer.phar /usr/local/bin/composer

# Download RoadRunner
ENV RR_VERSION 1.8.1
RUN mkdir /tmp/rr \
  && cd /tmp/rr \
  && echo "{\"require\":{\"spiral/roadrunner\":\"${RR_VERSION}\"}}" >> composer.json \
  && composer install \
  && vendor/bin/rr get-binary -l /usr/local/bin \
  && rm -rf /tmp/rr

COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/bin/
RUN install-php-extensions xdebug mysqli pdo_mysql pgsql pdo_pgsql opcache redis memcached

ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS="0" \
    PHP_OPCACHE_MAX_ACCELERATED_FILES="10000" \
    PHP_OPCACHE_MEMORY_CONSUMPTION="1000" \
    PHP_OPCACHE_MAX_WASTED_PERCENTAGE="5"
COPY ./etc/php/conf.d/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

WORKDIR /var/www/wright/ams

#RUN cd /var/www/wright/ams; composer install

EXPOSE 8081

ENTRYPOINT ["tail", "-f", "/dev/null"]
ENTRYPOINT ["/usr/local/bin/rr", "serve", "-d", "-c", "/etc/roadrunner/.rr.yaml"]
