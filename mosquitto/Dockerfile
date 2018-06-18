FROM buildpack-deps:jessie
LABEL maintainer="https://github.com/yuudj/docker"
ENV PATH=/usr/local/bin:/usr/local/sbin:$PATH

ENV MOSQUITTO_VERSION=1.4.15
ENV MONGOC_VERSION=9982861dac67bae659ce8a3370b18c3a44f764fc

RUN set -x; \
        apt-get update && apt-get install -y --no-install-recommends \
                libc-ares-dev git libmysqlclient-dev libssl-dev build-essential wget  ca-certificates \
                curl libcurl4-openssl-dev  libc-ares2 libcurl3 \
		cmake uuid-dev zlib1g-dev daemon libwebsockets-dev \
        && apt-get remove libwebsockets3 -y \
	&& git clone https://github.com/warmcat/libwebsockets.git \
	&& cd libwebsockets \
        && git checkout tags/v2.2.1 \
	&& mkdir build \
	&& cd build \
	&& cmake .. -DCMAKE_C_COMPILER=/usr/bin/gcc \
	&& make \
	&& make install \
        && cd /tmp \
        && git clone https://github.com/mongodb/mongo-c-driver.git && \
        cd mongo-c-driver && \
        git checkout ${MONGOC_VERSION} && \
        sh autogen.sh --with-libbson=bundled && \
        make && \
        make install && \
        cd /tmp \
        && wget http://mosquitto.org/files/source/mosquitto-$MOSQUITTO_VERSION.tar.gz -O mosquitto.tar.gz \
        && wget http://mosquitto.org/files/source/mosquitto-$MOSQUITTO_VERSION.tar.gz.asc -O mosquitto.tar.gz.asc \
        && mkdir mosquitto-src && tar xfz mosquitto.tar.gz --strip-components=1 -C mosquitto-src \
        && cd mosquitto-src \
        && make WITH_SRV=yes WITH_MEMORY_TRACKING=no WITH_WEBSOCKETS=yes WITH_TLS_PSK=no \
        && make install && ldconfig \ 
        && useradd -r mosquitto \
        && git clone git://github.com/jpmens/mosquitto-auth-plug.git && \
        cd mosquitto-auth-plug && \
        cp config.mk.in config.mk && \
        sed -i "s/BACKEND_MONGO ?= no/BACKEND_MONGO ?= yes/" config.mk && \
        sed -i "s/BACKEND_FILES ?= no/BACKEND_FILES ?= yes/" config.mk && \
        sed -i "s/BACKEND_MYSQL ?= yes/BACKEND_MYSQL ?= no/" config.mk && \
        sed -i "s/MOSQUITTO_SRC =/MOSQUITTO_SRC = ..\//" config.mk && \
        sed -i "s/EVP_MD_CTX_new/EVP_MD_CTX_create/g" cache.c && \
        sed -i "s/EVP_MD_CTX_free/EVP_MD_CTX_destroy/g" cache.c && \
        make && \
        cp auth-plug.so /usr/local/lib/ && \
        cp np /usr/local/bin/ && chmod +x /usr/local/bin/np && \
        cd / && rm -rf mosquitto_src  && rm -rf mongo-c-driver && \
        apt-get purge -y build-essential git wget ca-certificates \
        && apt-get autoremove -y \
        && apt-get -y autoclean \
        && rm -rf /var/cache/apt/* \
        && rm -rf  /tmp/*


COPY config /mqtt/config

EXPOSE 1883
EXPOSE 8080
VOLUME ["/mqtt/config", "/mqtt/data"]
COPY run.sh /
RUN chmod +x /run.sh
ENTRYPOINT ["/run.sh"]
CMD ["mosquitto-app", "-c", "/mqtt/config/mosquitto.conf"]
