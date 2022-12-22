FROM nginxinc/nginx-unprivileged:mainline-alpine AS builder

USER root

WORKDIR /usr/local/src

RUN wget "http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" -O nginx.tar.gz

RUN apk add --no-cache --virtual .build-deps \
    git \
    gcc \
    libc-dev \
    make \
    openssl-dev \
    pcre2-dev \
    zlib-dev \
    linux-headers \
    libxslt-dev \
    perl-dev \
    bash \
    alpine-sdk \
    findutils \
    brotli-dev

RUN set -x \
    && tar xzvf nginx.tar.gz \
    && mv /usr/local/src/nginx-${NGINX_VERSION} /usr/local/src/nginx

RUN cd /usr/local/src \
    && git clone --depth 1 https://github.com/google/ngx_brotli.git \
    && MODULEDIR=$(pwd)/ngx_brotli \
    && cd /usr/local/src/nginx \
    && ./configure --with-compat $CONFARGS --add-dynamic-module=$MODULEDIR \
    && make \
    && make install \
    && apk del .build-deps

FROM nginxinc/nginx-unprivileged:mainline-alpine

USER root

COPY --from=builder /usr/local/nginx/modules/ngx_http_brotli_filter_module.so /usr/local/nginx/modules/ngx_http_brotli_filter_module.so
COPY --from=builder /usr/local/nginx/modules/ngx_http_brotli_static_module.so /usr/local/nginx/modules/ngx_http_brotli_static_module.so

EXPOSE 8080
EXPOSE 4443

STOPSIGNAL SIGQUIT

USER $UID

CMD ["nginx", "-g", "daemon off;"]
