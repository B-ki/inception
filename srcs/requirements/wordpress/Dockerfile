FROM debian:buster

RUN apt update && apt install -y php php-curl php-mysqli php-fpm curl mariadb-client

COPY conf/www.conf /etc/php/7.3/fpm/pool.d/www.conf

WORKDIR /var/www/wordpress

RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
    chmod +x wp-cli.phar && \
    mv wp-cli.phar /usr/local/bin/wp && \
    mkdir -p /run/php

RUN wp core download --allow-root

COPY tools/setup.sh /usr/local/bin/

RUN chmod +x /usr/local/bin/setup.sh

EXPOSE 9000

ENTRYPOINT [ "/usr/local/bin/setup.sh" ]
