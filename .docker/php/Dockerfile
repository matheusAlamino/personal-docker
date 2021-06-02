FROM php:7.4-cli

WORKDIR /var/www
RUN rm -rf /var/www/html

RUN ln -s public html

RUN curl -sS https://getcomposer.org/installer | php -- --intall-dir=/usr/local/bin/composer

EXPOSE 9000
ENTRYPOINT ["php"]