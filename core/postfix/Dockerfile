FROM alpine:3.8
# python3 shared with most images
RUN apk add --no-cache \
    python3 py3-pip git bash \
  && pip3 install --upgrade pip
# Shared layer between rspamd, postfix, dovecot, unbound and nginx
RUN pip3 install git+https://github.com/usrpro/MailuStart.git#egg=mailustart
# Image specific layers under this line

RUN apk add --no-cache postfix postfix-pcre rsyslog \
 && pip3 install podop

COPY conf /conf
COPY start.py /start.py

EXPOSE 25/tcp 10025/tcp
VOLUME ["/data"]

RUN addgroup -g 1000 -S app && \
    adduser -u 1000 -S app -G app
RUN chmod -R 777 /etc/postfix/
RUN chmod 664 /etc/rsyslog.conf
RUN cat /etc/passwd

CMD /start.py

HEALTHCHECK --start-period=350s CMD echo QUIT|nc localhost 25|grep "220 .* ESMTP Postfix"
