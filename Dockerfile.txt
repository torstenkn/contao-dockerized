FROM php:apache
ENV APACHE_DOCUMENT_ROOT /var/www/html/contao/public
RUN apt update && apt install -y curl git 7zip libfreetype-dev libjpeg62-turbo-dev libpng-dev libicu-dev && apt clean

RUN docker-php-ext-configure gd --with-freetype --with-jpeg && docker-php-ext-install -j$(nproc) gd && docker-php-ext-install -j$(nproc) intl && docker-php-ext-install -j$(nproc) pdo_mysql && docker-php-ext-install -j$(nproc) iconv

RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

WORKDIR /var/www/html
RUN composer create-project contao/managed-edition contao 5.5
RUN curl -sS -o ${APACHE_DOCUMENT_ROOT}/contao-manager.phar.php https://download.contao.org/contao-manager/stable/contao-manager.phar

RUN chown -R www-data:www-data /var/www/html
RUN a2enmod rewrite

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

RUN rm -rf /var/www/html/contao/var/cache/*