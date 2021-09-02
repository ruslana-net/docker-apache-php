# See https://github.com/docker-library/php/blob/master/5.6/fpm/Dockerfile
FROM php:7.4-apache

MAINTAINER Ruslan Muriev <ruslana.net@gmail.com>

ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data

ARG TIMEZONE
ARG ENVIRONMENT

RUN apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    vim \
    sudo \
    libssl-dev libxml2-dev libpng-dev libcurl4-openssl-dev pkg-config \
    libwebp-dev libjpeg62-turbo-dev libxpm-dev libfreetype6-dev \
    xvfb libfontconfig xfonts-75dpi fontconfig libjpeg62-turbo libxrender1 \
    libxslt-dev

RUN eval "$(ssh-agent -s)"

#RUN echo "FromLineOverride=YES" >> /etc/ssmtp/ssmtp.conf && \
#    echo 'sendmail_path = "/usr/sbin/ssmtp -t"' > /usr/local/etc/php/conf.d/mail.ini

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
&& composer --version

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
&& printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini \
&& "date"

# Type docker-php-ext-install to see available extensions
#RUN docker-php-ext-configure gd --with-gd --with-webp-dir --with-jpeg-dir \
    --with-png-dir --with-zlib-dir --with-freetype-dir

RUN \
    docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
    && docker-php-ext-install pdo pdo_mysql mysqli
    
#RUN docker-php-ext-install pdo pdo_mysql mysqli opcache gd xml xmlrpc mbstring curl zip bcmath intl simplexml xsl soap sockets


# Install phpunit, the tool that we will use for testing
RUN curl --location --output /usr/local/bin/phpunit https://phar.phpunit.de/phpunit.phar \
    && chmod +x /usr/local/bin/phpunit

RUN printf 'ServerName %s', ${PUBLIC_IPV4} > /etc/apache2/conf-enabled/fqdn.conf

ADD sites-available/*.conf /etc/apache2/sites-available/

RUN rm /etc/apache2/sites-available/000-default.conf \
    && rm /etc/apache2/sites-available/default-ssl.conf \
    && ln -s /etc/apache2/sites-available/*.conf /etc/apache2/sites-enabled/ \
    && rm /etc/apache2/sites-enabled/000-default.conf

#RUN echo 'alias sf="php app/console"' >> ~/.bashrc \
#&& echo 'alias sf3="php bin/console"' >> ~/.bashrc

RUN docker-php-ext-install pdo pdo_mysql mysqli 

RUN a2enmod rewrite
RUN a2enmod ssl

RUN usermod -u 1000 www-data

WORKDIR /var/www/www

EXPOSE 80
EXPOSE 443
