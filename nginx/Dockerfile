FROM ubuntu:20.04 AS build

ARG NGINX_VERSION="1.17.10"
ARG OPENSSL_VERSION="OpenSSL_1_1_1g"
ARG PCRE_VERSION="8.44"
ARG ZLIB_VERSION="1.2.11"


RUN set -x && \
    apt-get update && \
    apt-get install -y curl build-essential && \
    curl -LO http://nginx.org/download/nginx-$NGINX_VERSION.tar.gz && \
    tar zxvf nginx-$NGINX_VERSION.tar.gz && \
    curl -LO https://github.com/openssl/openssl/archive/$OPENSSL_VERSION.tar.gz && \
    tar zxvf $OPENSSL_VERSION.tar.gz && \
    curl -LO https://ftp.pcre.org/pub/pcre/pcre-$PCRE_VERSION.tar.gz && \
    tar zxvf pcre-$PCRE_VERSION.tar.gz && \
    curl -LO https://zlib.net/zlib-$ZLIB_VERSION.tar.gz && \
    tar zxvf zlib-$ZLIB_VERSION.tar.gz && \
    cd nginx-$NGINX_VERSION && \
    ./configure \
    --with-cc-opt='-O2 -static -static-libgcc -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fstack-protector-strong' \
    --with-ld-opt='-static -Wl,-z,relro -Wl,-z,now' \
    --user=nginx \
    --group=nginx \
    --with-openssl=../openssl-OpenSSL_1_1_1g \
    --with-pcre=../pcre-$PCRE_VERSION \
    --with-pcre-jit \
    --with-zlib=../zlib-$ZLIB_VERSION \
    --with-select_module \
    --with-poll_module \
    --with-threads \
    --with-file-aio \
    --with-http_ssl_module \
    --with-http_v2_module \
    --with-http_realip_module \
    --with-http_dav_module \
    --with-http_mp4_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_auth_request_module \
    --with-http_random_index_module \
    --with-http_secure_link_module \
    --with-http_degradation_module \
    --with-http_slice_module \
    --with-http_stub_status_module \
    --with-stream \
    --with-stream_ssl_module \
    --with-stream_realip_module \
    --with-stream_ssl_preread_module && \
    make -j1 && \
    make -j1 install

FROM alpine:3.11
RUN addgroup -g 1000 -S nginx && \
    adduser -u 1000 -S nginx -G nginx
COPY --from=build --chown=nginx:nginx /usr/local/nginx /usr/local/nginx
COPY --chown=nginx:nginx nginx.conf /usr/local/nginx/conf/nginx.conf


WORKDIR /usr/local/nginx
USER 1000:1000

EXPOSE 8080

CMD ["sbin/nginx", "-g", "daemon off;"]
