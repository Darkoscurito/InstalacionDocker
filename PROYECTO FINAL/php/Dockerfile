FROM php:8.3-fpm-alpine

# Dependencias del sistema necesarias para las extensiones PHP
RUN apk add --no-cache \
    postgresql-dev \
    libzip-dev \
    zip \
    unzip \
    git

# Extensiones PHP que necesita Laravel con PostgreSQL
# Documentación: https://laravel.com/docs/installation#server-requirements
RUN docker-php-ext-install \
    pdo \
    pdo_pgsql \
    zip \
    bcmath

# Composer — gestor de dependencias de PHP
# Documentación: https://getcomposer.org
COPY --from=composer:2.7 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

EXPOSE 9000

CMD ["php-fpm"]
