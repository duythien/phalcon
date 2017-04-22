FROM ubuntu:14.04
MAINTAINER Phanbook <helllo@phanbook.com>

# run update and install nginx, php-fpm and other useful libraries
RUN apt-get update -y && \
    apt-get install -y \
    curl \
    wget \
    nano \
    git \
    php5-fpm \
    php5-cli \
    php5-intl \
    php5-mcrypt \
    php5-apcu \
    php5-gd \
    php5-curl \
    php5-mysql \
    mcrypt \
    php5-imagick \
    php5-memcached \
    php-gettext \
    gettext

    
ENV PHALCON_VERSION=2.0.x
# php-fpm config
RUN sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php5/fpm/php-fpm.conf
RUN sed -i -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i "s|listen = /var/run/php5-fpm.sock|listen = 0.0.0.0:9000|g" /etc/php5/fpm/pool.d/www.conf

# PHP Error Reporting Config
RUN sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php5/fpm/php.ini
RUN sed -i "s/display_errors = .*/display_errors = On/" /etc/php5/fpm/php.ini
RUN find /etc/php5/cli/conf.d/ -name "*.ini" -exec sed -i -re 's/^(\s*)#(.*)/\1;\2/g' {} \;


RUN apt-get -qq install php5-dev libpcre3-dev gcc make re2c
RUN (cd /tmp && git clone  -b $PHALCON_VERSION --single-branch git://github.com/phalcon/cphalcon.git && \
    cd cphalcon/ext && \
    phpize &&  \
    ./configure && \
make && make install)

# ADD cphalcon /tmp/cphalcon
# RUN (cd /tmp/cphalcon/ext && \
#     phpize &&  \
#     ./configure && \
# make && make install)

RUN rm -rf /tmp/*

RUN apt-get remove -y -f \
        build-essential \
        make \
        re2c \

    && apt-get clean -y \
    && rm -rf \
        /tmp/* \
        /var/lib/apt/lists/* \
        /installers \
        /opt/docker/etc/php/fpm/pool.d/www.conf

# Manually set up the apache environment variables
# ENV APACHE_RUN_USER www-data
# ENV APACHE_RUN_GROUP www-data
# ENV APACHE_LOG_DIR /var/log/apache2
# ENV APACHE_LOCK_DIR /var/lock/apache2
# ENV APACHE_PID_FILE /var/run/apache2.pid

# RUN apt-get install php5 apache2 -y
# RUN a2enmod rewrite

# RUN apt-get install software-properties-common -y
# RUN add-apt-repository ppa:phalcon/legacy && apt-get update -y
# RUN  apt-get install php5-phalcon -y
RUN echo "extension=phalcon.so" > /etc/php5/fpm/conf.d/30-phalcon.ini
RUN echo "extension=phalcon.so" > /etc/php5/cli/conf.d/30-phalcon.ini

RUN echo "extension=getext.so" > /etc/php5/fpm/conf.d/30-gettext.ini

VOLUME [ "/var/www/html" ]
WORKDIR /var/www/html
EXPOSE 9000
CMD ["/usr/sbin/php5-fpm"]
#CMD ["apachectl", "-D", "FOREGROUND"]

