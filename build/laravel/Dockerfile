FROM php:apache

RUN curl --silent --location https://deb.nodesource.com/setup_6.x | bash - \
	&& apt-get update && apt-get upgrade -y

RUN apt-get install -y mysql-client-5.5 vim imagemagick nodejs zlib1g-dev libxml2-dev

RUN docker-php-ext-install pdo_mysql \
	&& docker-php-ext-install zip \
	&& docker-php-ext-install soap

RUN a2enmod rewrite

ARG LARAVEL_PROJECT_NAME
ARG HOST_IP

RUN yes | pecl install xdebug
RUN echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
  && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
	&& echo "xdebug.remote_host=$HOST_IP" >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

#RUN service apache2 start


RUN sed -i "s/\/var\/www\/html/\/var\/www\/html\/$LARAVEL_PROJECT_NAME\/public/g" /etc/apache2/sites-available/000-default.conf

#NECESSARIO PARA MANTER OS LOGS DO APACHE2
RUN unlink /var/log/apache2/access.log \
 && unlink /var/log/apache2/error.log \
 && unlink /var/log/apache2/other_vhosts_access.log \
 && echo "log_errors=On" >> /usr/local/etc/php/php.ini

RUN curl -sS https://getcomposer.org/installer | php \
	&& mv composer.phar /usr/local/bin/composer \
	&& printf "\nPATH=\"~/.composer/vendor/bin:\$PATH\"\n" | tee -a ~/.bashrc

# ESSA É A FORMA DE CRIAR UM PROJETO DOCKER DE DENTRO DO CONTAINER
#RUN cd /var/www/html && \
#	composer create-project laravel/laravel $PROJECT_NAME $LARAVEL_VERSION --prefer-dist --no-interaction && \
#	php $PROJECT_NAME/artisan key:generate && \
#	chown www-data:www-data -R $PROJECT_NAME/storage

VOLUME /var/www/html/
#NECESSÁRIO PORQUE A IMAGEM BASE USA: CMD ["apache2-foreground"]
CMD service apache2 stop \
	&& service apache2 start \
	&& tail -F -n0 /var/log/apache2/access.log /var/log/apache2/error.log
