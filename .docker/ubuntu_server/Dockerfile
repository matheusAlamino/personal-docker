FROM ubuntu:20.04

MAINTAINER Matheus Martins <matheus.alamino@outlook.com>

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /var/www

# Installing base packages, adding PHP repo
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install -y \
    apache2 \
    libapache2-mod-php \
    software-properties-common curl \
    php-pear apt-transport-https locales gnupg wget && \
    LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php -y

# Install dependencies for MS SQLSRV PHP Driver
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - && \
    curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > /etc/apt/sources.list.d/mssql-release.list && \
    apt-get update -yqq && \
    ACCEPT_EULA=Y apt-get install -y msodbcsql17 unixodbc-dev unixodbc --no-install-recommends && \
    rm -r /var/lib/apt/lists/*

RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install -y \
    nano \
    zip \
    unzip \
    php7.4-cli \
    php7.4-mysql \
    php7.4-mysqlnd \
    php7.4-apcu \
    php7.4-memcached \
    php7.4-imap \
    php7.4-pgsql \
    php7.4-intl \
    php7.4-mbstring \
    php7.4-redis \
    php7.4-mongo \
    php7.4-sqlite \
    php7.4-curl \
    php7.4-json \
    php7.4-ssh2 \
    php7.4-gd \
    php7.4-gmp \
    php7.4-xml \
    php7.4-zip \
    php7.4-soap \
    php7.4-bcmath \
    php7.4-dev \
    php7.4-ldap \
    php-pear \
    mcrypt \
    make \
    gettext \
    wget \
    git \
    curl && apt-get clean && rm -rf /var/lib/apt/lists/*

# Enable apache mods.
RUN phpenmod pdo_mysql && \
    a2enmod expires && \
    a2enmod headers && \
    a2enmod rewrite

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/upload_max_filesize = .*$/upload_max_filesize = 5000M/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/post_max_size = .*$/post_max_size = 5000M/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/memory_limit = .*$/memory_limit = 1024M/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/;extension=pdo_mysql.so/extension=pdo_mysql.so/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/;extension=gettext/extension=gettext/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/display_errors = Off/display_errors = On/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/doc_root =/doc_root = \/var\/www/" /etc/php/7.4/apache2/php.ini

# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

COPY ./Apache/sites-available/*.conf /etc/apache2/sites-available/
COPY ./Apache/apache2.conf /etc/apache2/apache2.conf
#ADD ./Apache/hosts /etc/hosts

RUN a2ensite api-desafio.development.conf && \
    service apache2 restart

# Update the default apache site with the config we created.
#ADD apache-config.conf /etc/apache2/sites-enabled/000-default.conf

# Expose apache.
EXPOSE 80

# By default start up apache in the foreground, override with /bin/bash for interative.
CMD /usr/sbin/apache2ctl -D FOREGROUND