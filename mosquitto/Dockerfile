FROM buildpack-deps:jessie
LABEL maintainer="https://github.com/yuudj/docker"
ENV PATH=/usr/local/bin:/usr/local/sbin:$PATH

ENV MOSQUITTO_VERSION=1.4.14
RUN set -x; \
        apt-get update && apt-get install -y --no-install-recommends \
                libc-ares-dev git libmysqlclient-dev libssl-dev build-essential wget  ca-certificates \
                curl libcurl4-openssl-dev  libc-ares2 libcurl3 \
		cmake uuid-dev zlib1g-dev daemon \
	&& git clone https://github.com/warmcat/libwebsockets.git \
	&& cd libwebsockets \
	&& mkdir build \
	&& cd build \
	&& cmake .. -DCMAKE_C_COMPILER=/usr/bin/gcc \
	&& make \
	&& make install \
        && cd /tmp \
        && wget http://mosquitto.org/files/source/mosquitto-$MOSQUITTO_VERSION.tar.gz -O mosquitto.tar.gz \
        && wget http://mosquitto.org/files/source/mosquitto-$MOSQUITTO_VERSION.tar.gz.asc -O mosquitto.tar.gz.asc \
        && mkdir mosquitto-src && tar xfz mosquitto.tar.gz --strip-components=1 -C mosquitto-src \
        && cd mosquitto-src \
        && make WITH_SRV=yes WITH_MEMORY_TRACKING=no WITH_WEBSOCKETS=yes WITH_TLS_PSK=no \
        && make install && ldconfig \ 
        && useradd -r mosquitto \
        && apt-get purge -y build-essential git wget ca-certificates \
        && apt-get autoremove -y \
        && apt-get -y autoclean \
        && rm -rf /var/cache/apt/* \
        && rm -rf  /tmp/*


COPY config /mqtt/config

EXPOSE 1883 9001
VOLUME ["/mqtt/config", "/mqtt/data"]
COPY run.sh /
RUN chmod +x /run.sh
ENTRYPOINT ["/run.sh"]
CMD ["mosquitto-app", "-c", "/mqtt/config/mosquitto.conf"]
