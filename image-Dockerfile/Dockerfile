FROM alpine:3.8

RUN apk update \
 && apk add --no-cache \
        ca-certificates \
        ruby ruby-irb ruby-etc ruby-webrick \
        tini \
 && apk add --no-cache --virtual .build-deps \
        build-base \
        ruby-dev gnupg \
 && echo 'gem: --no-document' >> /etc/gemrc \
 && gem install oj -v 3.3.10 \
 && gem install json -v 2.1.0 \
 && gem install fluentd -v 1.3.3 \
 && gem install bigdecimal -v 1.3.5 \
 && apk del .build-deps \
 && rm -rf /tmp/* /var/tmp/* /usr/lib/ruby/gems/*/cache/*.gem

RUN mkdir -p /fluentd/log

RUN mkdir -p /fluentd/etc /fluentd/plugins
#RUN addgroup -S fluent && adduser -S -g fluent fluent
#RUN chown -R fluent /fluentd && chgrp -R fluent /fluentd

ONBUILD COPY fluent.conf /fluentd/etc/
ONBUILD COPY plugins /fluentd/plugins/

ENV LD_PRELOAD=""
EXPOSE 24224 5140

#USER fluent

RUN apk add --update --virtual .build-deps \
        sudo build-base ruby-dev \
 && sudo gem install \
        fluent-plugin-out-http \
        fluent-plugin-record-modifier \
        fluent-plugin-zabbix-agent \
        fluent-plugin-rewrite-tag-filter \
        fluent-plugin-route \
        fluent-plugin-redis-store \
        fluent-plugin-kafka \
        fluent-plugin-elasticsearch \
 && sudo gem sources --clear-all \
 && apk del .build-deps \
 && rm -rf /var/cache/apk/* \
           /home/fluent/.gem/ruby/2.5.0/cache/*.gem
RUN mkdir -p /plugin-templeate
COPY fluent.conf /fluentd/etc/
COPY in_zabbix_agent.rb /usr/lib/ruby/gems/2.5.0/gems/fluent-plugin-zabbix-agent-0.2.0/lib/fluent/plugin/in_zabbix_agent.rb
COPY plugins /plugin-templeate
COPY entrypoint.sh /bin/
RUN chmod +x /bin/entrypoint.sh

USER root
ENTRYPOINT ["tini",  "--", "/bin/entrypoint.sh"]
CMD ["fluentd"]

