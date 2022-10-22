ARG ALPINE_VERSION=3
ARG NGINX_VERSION=1.22.0

FROM alpine:${ALPINE_VERSION} as build

ARG NGINX_VERSION=1.22.0
ARG NGINX_HTTP_FLV_MODULE_VERSION=1.2.10

WORKDIR /tmp

ADD https://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz /tmp
ADD https://github.com/winshining/nginx-http-flv-module/archive/refs/tags/v${NGINX_HTTP_FLV_MODULE_VERSION}.tar.gz /tmp

RUN tar -zxf nginx-${NGINX_VERSION}.tar.gz \
&& tar -zxf v${NGINX_HTTP_FLV_MODULE_VERSION}.tar.gz \
&& mv nginx-${NGINX_VERSION} nginx \
&& mv nginx-http-flv-module-${NGINX_HTTP_FLV_MODULE_VERSION} nginx-http-flv-module \
&& apk add --no-cache --virtual .build-deps make gcc libc-dev pcre2-dev openssl-dev zlib-dev \
&& cd nginx \
&& ./configure --with-compat --add-dynamic-module=/tmp/nginx-http-flv-module \
&& make \
&& make install \
&& apk del .build-deps \
&& rm -rf /tmp/*

FROM nginx:${NGINX_VERSION}-alpine

WORKDIR /app

COPY --from=build /usr/local/nginx/modules/ngx_http_flv_live_module.so /etc/nginx/modules/ngx_http_flv_live_module.so

COPY app /app
COPY nginx /etc/nginx

EXPOSE 80 1935
