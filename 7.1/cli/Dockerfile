FROM larvacent/base-php:xenial

LABEL maintainer="support@larvacent.com"

ARG PHP_VERSION
ARG MSGPACK_VERSION
ARG IGBINARY_VERSION
ARG MEMCACHED_VERSION
ARG REDIS_VERSION
ARG IMAGICK_VERSION
ARG APCU_VERSION
ARG MONGO_VERSION
ARG YAML_VERSION

# Environment settings
ENV PHP_VERSION ${PHP_VERSION}
ENV PHP_TGZ_URL http://php.net/distributions/php-${PHP_VERSION}.tar.gz
ENV PHP_INI_DIR=/usr/local/etc \
	PATH=/root/.composer/vendor/bin:$PATH \
	TERM=linux \
	COMPOSER_ALLOW_SUPERUSER=1

# Apply stack smash protection to functions using local buffers and alloca()
# Make PHP's main executable position-independent (improves ASLR security mechanism, and has no performance impact on x86_64)
# Enable optimization (-O2)
# Enable linker optimization (this sorts the hash buckets to improve cache locality, and is non-default)
# Adds GNU HASH segments to generated executables (this is used if present, and is much faster than sysv hash; in this configuration, sysv hash is also generated)
# https://github.com/docker-library/php/issues/272
ENV PHP_CFLAGS="-fstack-protector-strong -fpic -fpie -O2"
ENV PHP_CPPFLAGS="$PHP_CFLAGS"
ENV PHP_LDFLAGS="-Wl,-O1 -Wl,--hash-style=both -pie"

RUN set -xe \
	&& buildDeps=" \
		libxml2-dev \
		libssl-dev \
		libbz2-dev \
		libcurl4-openssl-dev \
		libjpeg-dev \
		libpng-dev \
		libfreetype6-dev \
		libgmp-dev \
		libmcrypt-dev \
		libedit-dev \
		libtidy-dev \
		libxslt-dev \
		libzip-dev \
	" \
	\
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests mcrypt $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& export CFLAGS="$PHP_CFLAGS" \
		CPPFLAGS="$PHP_CPPFLAGS" \
		LDFLAGS="$PHP_LDFLAGS" \
	&& cd /usr/local/src \
	&& curl -fSL ${PHP_TGZ_URL} -o php-${PHP_VERSION}.tar.gz \
	&& tar zxf php-${PHP_VERSION}.tar.gz \
	&& rm -f php-${PHP_VERSION}.tar.gz \
	&& cd php-${PHP_VERSION} \
	&& ./configure \
		--prefix=/usr/local \
		--libdir=/usr/local/lib64 \
		--with-config-file-path=${PHP_INI_DIR} \
		--with-config-file-scan-dir=${PHP_INI_DIR}/php \
		--with-libxml-dir \
		--with-openssl \
		--with-kerberos \
		--with-zlib \
		--with-curl \
		--with-bz2 \
		--with-png-dir \
		--with-gd \
		--with-jpeg-dir \
		--with-libzip \
		--with-xmlrpc \
		--with-libedit \
		--with-iconv-dir=/usr/local \
		--with-xsl \
		--with-gmp \
		--with-tidy \
		--with-pcre-dir \
		--with-readline \
		--with-pcre-regex \
		--with-pcre-jit \
		--with-freetype-dir \
		--with-zlib-dir \
		--without-pear \
		--with-mcrypt \
		--with-mysqli=mysqlnd \
		--with-pdo-mysql=mysqlnd \
		--with-pdo-sqlite \
		--with-sqlite3 \
		--with-mhash \
		--with-gettext \
		--with-libdir=lib64 \
		--enable-bcmath \
		--enable-calendar \
		--enable-exif \
		--disable-cgi \
		--enable-ftp \
		--enable-gd-native-ttf \
		--enable-mbstring \
		--enable-mbregex \
		--enable-shmop \
		--enable-soap \
		--enable-sockets \
		--enable-sysvmsg \
		--enable-sysvsem \
		--enable-sysvshm \
		--enable-wddx \
		--enable-zip \
		--enable-xml \
		--disable-rpath \
		--enable-inline-optimization \
		--enable-pcntl \
		--enable-fileinfo \
		--enable-intl \
		--enable-opcache \
		--enable-cli \
	&& make ZEND_EXTRA_LIBS='-liconv' \
	&& make install \
	&& rm -rf /usr/local/src/php-${PHP_VERSION} \
	&& mkdir -p ${PHP_INI_DIR}/php \
	&& mkdir -p /var/lib/php \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

RUN set -xe \
	&& buildDeps=" \
		libmemcached-dev \
		libyaml-dev \
		libnghttp2-dev \
		libssl-dev \
		libmagick++-dev \
	" \
	\
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests libmemcached11 imagemagick libyaml-0-2 libnghttp2-14 $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/msgpack-${MSGPACK_VERSION}.tgz -o msgpack-${MSGPACK_VERSION}.tgz \
	&& tar zxf msgpack-${MSGPACK_VERSION}.tgz && rm -rf msgpack-${MSGPACK_VERSION}.tgz \
	&& cd msgpack-${MSGPACK_VERSION} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/msgpack-${MSGPACK_VERSION} \
	&& echo "extension=msgpack.so" >> ${PHP_INI_DIR}/php/msgpack.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/igbinary-${IGBINARY_VERSION}.tgz -o igbinary-${IGBINARY_VERSION}.tgz \
	&& tar zxf igbinary-${IGBINARY_VERSION}.tgz && rm -rf igbinary-${IGBINARY_VERSION}.tgz \
	&& cd igbinary-${IGBINARY_VERSION} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/igbinary-${IGBINARY_VERSION} \
	&& echo "extension=igbinary.so" >> ${PHP_INI_DIR}/php/igbinary.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/memcached-${MEMCACHED_VERSION}.tgz -o memcached-${MEMCACHED_VERSION}.tgz \
	&& tar zxf memcached-${MEMCACHED_VERSION}.tgz && rm -rf memcached-${MEMCACHED_VERSION}.tgz \
	&& cd memcached-${MEMCACHED_VERSION} \
	&& phpize \
	&& ./configure --enable-memcached --enable-memcached-igbinary --enable-memcached-json --enable-memcached-msgpack --with-libdir=lib64 \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/memcached-${MEMCACHED_VERSION} \
	&& echo "extension=memcached.so" >> ${PHP_INI_DIR}/php/memcached.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/redis-${REDIS_VERSION}.tgz -o redis-${REDIS_VERSION}.tgz \
	&& tar zxf redis-${REDIS_VERSION}.tgz && rm -rf redis-${REDIS_VERSION}.tgz \
	&& cd redis-${REDIS_VERSION} \
	&& phpize \
	&& ./configure --enable-redis --enable-redis-igbinary \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/redis-${REDIS_VERSION} \
	&& echo "extension=redis.so" >> ${PHP_INI_DIR}/php/redis.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/imagick-${IMAGICK_VERSION}.tgz -o imagick-${IMAGICK_VERSION}.tgz \
	&& tar zxf imagick-${IMAGICK_VERSION}.tgz && rm -rf imagick-${IMAGICK_VERSION}.tgz \
	&& cd imagick-${IMAGICK_VERSION} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/imagick-${IMAGICK_VERSION} \
	&& echo "extension=imagick.so" >> ${PHP_INI_DIR}/php/imagick.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/apcu-${APCU_VERSION}.tgz -o apcu-${APCU_VERSION}.tgz \
	&& tar zxf apcu-${APCU_VERSION}.tgz && rm -rf apcu-${APCU_VERSION}.tgz \
	&& cd apcu-${APCU_VERSION} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/apcu-${APCU_VERSION} \
	&& echo "extension=apcu.so" >> ${PHP_INI_DIR}/php/apcu.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/mongodb-${MONGO_VERSION}.tgz -o mongodb-${MONGO_VERSION}.tgz \
	&& tar zxf mongodb-${MONGO_VERSION}.tgz && rm -rf mongodb-${MONGO_VERSION}.tgz \
	&& cd mongodb-${MONGO_VERSION} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/mongodb-${MONGO_VERSION} \
	&& echo "extension=mongodb.so" >> ${PHP_INI_DIR}/php/mongodb.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/yaml-${YAML_VERSION}.tgz -o yaml-${YAML_VERSION}.tgz \
	&& tar zxf yaml-${YAML_VERSION}.tgz && rm -rf yaml-${YAML_VERSION}.tgz \
	&& cd yaml-${YAML_VERSION} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/yaml-${YAML_VERSION} \
	&& echo "extension=yaml.so" >> ${PHP_INI_DIR}/php/yaml.ini \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

# Add configuration files
COPY image-files/ /

RUN chmod +x /usr/local/bin/docker-entrypoint \
	&& php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
	&& php -r "unlink('composer-setup.php');" \
	&& composer global require hirak/prestissimo --optimize-autoloader \
	&& composer global dumpautoload --optimize \
	&& composer clear-cache

ENTRYPOINT ["docker-entrypoint"]

CMD ["php", "-a"]