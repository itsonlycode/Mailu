FROM php:7.2-apache
ENV PORT 8080
#Shared layer between rainloop and roundcube
RUN apt-get update && apt-get install -y \
  python3 curl python3-pip git \
  && rm -rf /var/lib/apt/lists \
  && echo "ServerSignature Off" >> /etc/apache2/apache2.conf

ENV ROUNDCUBE_URL https://github.com/roundcube/roundcubemail/releases/download/1.3.9/roundcubemail-1.3.9-complete.tar.gz

CMD sed -i "s/80/$PORT/g" /etc/apache2/sites-available/000-default.conf /etc/apache2/ports.conf

RUN apt-get update && apt-get install -y \
      zlib1g-dev python3-jinja2 \
 && docker-php-ext-install zip \
 && echo date.timezone=UTC > /usr/local/etc/php/conf.d/timezone.ini \
 && rm -rf /var/www/html/ \
 && cd /var/www \
 && curl -L -O ${ROUNDCUBE_URL} \
 && tar -xf *.tar.gz \
 && rm -f *.tar.gz \
 && mv roundcubemail-* html \
 && cd html \
 && rm -rf CHANGELOG INSTALL LICENSE README.md UPGRADING composer.json-dist installer \
 && sed -i 's,mod_php5.c,mod_php7.c,g' .htaccess \
 && chown -R www-data: logs temp \
 && rm -rf /var/lib/apt/lists

RUN pip3 install git+https://github.com/usrpro/MailuStart.git#egg=mailustart


COPY php.ini /php.ini
COPY config.inc.php /var/www/html/config/
COPY start.py /start.py
RUN chmod -R 777 /usr/local/etc/php/conf.d

EXPOSE 8080/tcp
VOLUME ["/data"]

CMD /start.py
