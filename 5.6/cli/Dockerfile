FROM larvacent/base-php:xenial

LABEL maintainer="support@larvacent.com"

ARG PHP_VERSION

# Environment settings
ENV PHP_VERSION=${PHP_VERSION:-5.6.34}
ENV PHP_TGZ_URL=http://php.net/distributions/php-${PHP_VERSION}.tar.gz \
	PHP_INI_DIR=/usr/local/etc \
	PATH=/root/.composer/vendor/bin:$PATH \
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
	" \
	\
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests mcrypt $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& export CFLAGS="$PHP_CFLAGS" \
		CPPFLAGS="$PHP_CPPFLAGS" \
		LDFLAGS="$PHP_LDFLAGS" \
	&& if [ ! -f /usr/include/gmp.h ]; then \
		ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h; \
	fi \
	&& cd /usr/local/src \
	&& curl -fSL ${PHP_TGZ_URL} -o php-${PHP_VERSION}.tar.gz \
	&& tar zxf php-${PHP_VERSION}.tar.gz \
	&& rm -f php-${PHP_VERSION}.tar.gz \
	&& cd php-${PHP_VERSION} \
	&& /sbin/ldconfig \
	&& ./configure \
		--prefix=/usr/local \
		--libdir=/usr/local/lib64 \
		--with-config-file-path=${PHP_INI_DIR} \
		--with-config-file-scan-dir=${PHP_INI_DIR}/php \
		--with-libxml-dir \
		--with-openssl \
		--with-kerberos \
		--with-zlib \
		--with-bz2 \
		--with-curl \
		--with-png-dir \
		--with-gd \
		--with-jpeg-dir \
		--with-xmlrpc \
		--with-libedit \
		--with-iconv-dir=/usr/local \
		--with-xsl \
		--with-mysqli=mysqlnd \
		--with-pdo-mysql=mysqlnd \
		--with-pdo-sqlite \
		--with-sqlite3 \
		--with-mhash \
		--with-mcrypt \
		--without-pear \
		--with-gettext \
		--with-gmp \
		--with-tidy \
		--with-pcre-dir \
		--with-readline \
		--with-pcre-regex \
		--with-freetype-dir \
		--with-zlib-dir \
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
		--enable-xml  \
		--disable-rpath \
		--enable-inline-optimization  \
		--enable-pcntl \
		--enable-fileinfo \
		--enable-intl \
		--enable-opcache \
		--enable-cli \
	&& make -j "$(nproc)" ZEND_EXTRA_LIBS='-liconv' \
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
	" \
	\
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests libmemcached11 libyaml-0-2 libnghttp2-14 $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& msgpackVersion=0.5.7 \
		igbinaryVersion=2.0.5 \
		memcachedVersion=2.2.0 \
		redisVersion=3.1.6 \
		yamlVersion=1.3.2 \
		memcacheVersion=3.0.8 \
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/msgpack-${msgpackVersion}.tgz -o msgpack-${msgpackVersion}.tgz \
	&& tar zxf msgpack-${msgpackVersion}.tgz \
	&& rm -rf msgpack-${msgpackVersion}.tgz \
	&& cd msgpack-${msgpackVersion} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/msgpack-${msgpackVersion} \
	&& echo "extension=msgpack.so" >> ${PHP_INI_DIR}/php/msgpack.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/igbinary-${igbinaryVersion}.tgz -o igbinary-${igbinaryVersion}.tgz \
	&& tar zxf igbinary-${igbinaryVersion}.tgz \
	&& rm -rf igbinary-${igbinaryVersion}.tgz \
	&& cd igbinary-${igbinaryVersion} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/igbinary-${igbinaryVersion} \
	&& echo "extension=igbinary.so" >> ${PHP_INI_DIR}/php/igbinary.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/memcache-${memcacheVersion}.tgz -o memcache-${memcacheVersion}.tgz \
	&& tar zxf memcache-${memcacheVersion}.tgz \
	&& rm -rf memcache-${memcacheVersion}.tgz \
	&& cd memcache-${memcacheVersion} \
	&& phpize \
	&& ./configure --enable-memcache --with-zlib-dir --with-libdir=lib64 \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/memcache-${memcachedVersion} \
	&& echo "extension=memcache.so" >> ${PHP_INI_DIR}/php/memcache.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/memcached-${memcachedVersion}.tgz -o memcached-${memcachedVersion}.tgz \
	&& tar zxf memcached-${memcachedVersion}.tgz \
	&& rm -rf memcached-${memcachedVersion}.tgz \
	&& cd memcached-${memcachedVersion} \
	&& phpize \
	&& ./configure --enable-memcached --enable-memcached-igbinary --enable-memcached-json --enable-memcached-msgpack --with-libdir=lib64 \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/memcached-${memcachedVersion} \
	&& echo "extension=memcached.so" >> ${PHP_INI_DIR}/php/memcached.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/redis-${redisVersion}.tgz -o redis-${redisVersion}.tgz \
	&& tar zxf redis-${redisVersion}.tgz \
	&& rm -rf redis-${redisVersion}.tgz \
	&& cd redis-${redisVersion} \
	&& phpize \
	&& ./configure --enable-redis --enable-redis-igbinary \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/redis-${redisVersion} \
	&& echo "extension=redis.so" >> ${PHP_INI_DIR}/php/redis.ini \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/yaml-${yamlVersion}.tgz -o yaml-${yamlVersion}.tgz \
	&& tar zxf yaml-${yamlVersion}.tgz \
	&& rm -rf yaml-${yamlVersion}.tgz \
	&& cd yaml-${yamlVersion} \
	&& phpize \
	&& ./configure \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/yaml-${yamlVersion} \
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