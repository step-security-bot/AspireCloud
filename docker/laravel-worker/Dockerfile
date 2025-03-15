FROM php:8.3-cli@sha256:2f0ed286d3412fb8cb57b9d6b184e594434475c6d187101fa93c7275998d1edf AS base

COPY --from=composer:2.8.5 /usr/bin/composer /usr/bin/composer
ADD --chmod=0755 https://github.com/mlocati/docker-php-extension-installer/releases/download/2.7.14/install-php-extensions /usr/local/bin/

RUN apt update && apt install -y bash git postgresql-client zip

RUN install-php-extensions pdo pdo_pgsql zip intl opcache redis

COPY ./docker/cli/php.ini /usr/local/etc/php/php.ini

RUN useradd --create-home --shell /bin/bash app

WORKDIR /app

################
FROM base AS dev

RUN install-php-extensions xdebug

USER app

################
FROM base AS prod

# no workie, composer can't create /app/vendor ¯\_(ツ)_/¯
# COPY --chown=app:app . /app

COPY . /app
RUN chown -R app:app /app

USER app

RUN composer install --no-dev --no-interaction --no-progress --optimize-autoloader --working-dir=/app \
    && mkdir -p storage/logs storage/app/public storage/app/private storage/framework/sessions storage/framework/views storage/framework/cache/data
