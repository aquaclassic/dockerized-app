FROM ubuntu:16.04

MAINTAINER Marko Vujanic

RUN apt-get update \
      && apt-get install -y locales \
      && locale-gen en_US.UTF-8

      ENV LANG en_US.UTF-8
      ENV LANGUAGE en_US:en
      ENV LC_ALL en_US.UTF-8

      RUN apt-get update \
          && apt-get install -y nginx curl zip unzip git software-properties-common supervisor sqlite3 \
          && add-apt-repository -y ppa:ondrej/php \
          && apt-get update \
          && apt-get install -y php7.0-fpm php7.0-cli php7.0-mcrypt php7.0-gd php7.0-mysql \
             php7.0-sqlite3 php7.0-imap php-memcached php7.0-mbstring php7.0-xml php7.0-curl \
          && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
          && mkdir /run/php \
          && apt-get remove -y --purge software-properties-common \
          && apt-get -y autoremove \
          && apt-get clean \
          && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
          && echo "daemon off;" >> /etc/nginx/nginx.conf \
          && ln -sf /dev/stdout /var/log/nginx/access.log \
          && ln -sf /dev/stderr /var/log/nginx/error.log

# override default nginx to be setup with php
COPY default /etc/nginx/sites-available/default
# make php not run like a dameon and write php to stdout/err
COPY php-fpm.conf /etc/php/7.0/fpm/php-fpm.conf

EXPOSE 80

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# container will by default run supervisord which needs to be setup to run both nginx and php
CMD "/usr/bin/supervisord"
