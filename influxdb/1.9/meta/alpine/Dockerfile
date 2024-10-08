FROM alpine:3.14

RUN echo 'hosts: files dns' >> /etc/nsswitch.conf
RUN apk add --no-cache tzdata bash ca-certificates && \
    update-ca-certificates

ENV INFLUXDB_VERSION 1.9.5-c1.9.5
RUN set -ex && \
    apk add --no-cache --virtual .build-deps wget gnupg tar && \
    for key in \
        05CE15085FC09D18E99EFB22684A14CF2582E0C5 ; \
    do \
        gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys "$key" ; \
    done && \
    wget --no-verbose https://dl.influxdata.com/enterprise/releases/influxdb-meta-${INFLUXDB_VERSION}_linux_amd64.tar.gz.asc && \
    wget --no-verbose https://dl.influxdata.com/enterprise/releases/influxdb-meta-${INFLUXDB_VERSION}_linux_amd64.tar.gz && \
    gpg --batch --verify influxdb-meta-${INFLUXDB_VERSION}_linux_amd64.tar.gz.asc influxdb-meta-${INFLUXDB_VERSION}_linux_amd64.tar.gz && \
    mkdir -p /usr/src && \
    tar -C /usr/src -xzf influxdb-meta-${INFLUXDB_VERSION}_linux_amd64.tar.gz && \
    rm -f /usr/src/influxdb-*/influxdb-meta.conf && \
    chmod +x /usr/src/influxdb-*/* && \
    cp -a /usr/src/influxdb-*/* /usr/bin/ && \
    (rm -rf /root/.gnupg || true) && \
    rm -rf *.tar.gz* /usr/src && \
    apk del .build-deps
COPY influxdb-meta.conf /etc/influxdb/influxdb-meta.conf

EXPOSE 8091

VOLUME /var/lib/influxdb

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["influxd-meta"]
