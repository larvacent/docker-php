# 基于 Ubuntu xenial 构建的 PHP执行环境

## Build Status

[![Build Status](https://travis-ci.org/larvacent/docker-php.svg?branch=master)](https://travis-ci.org/larvacent/docker-php) 

## Container layout

Container                               | Distribution name        | PHP Version
--------------------------------------- | ------------------------ | --------------
`larvacent/php:5.6`      | xenial (LTS)             | PHP 5.6
`larvacent/php:7.0`      | xenial (LTS)             | PHP 7.0
`larvacent/php:7.1`      | xenial (LTS)             | PHP 7.1
`larvacent/php:7.2`      | xenial (LTS)             | PHP 7.2
`larvacent/php:7.3`      | xenial (LTS)             | PHP 7.3

## Filesystem layout

Directory                       | Description
------------------------------- | ------------------------------------------------------------------------------
`/usr/local/etc/php-fpm.d`       | php-fpm pool configuration
`/usr/local/etc/nginx`           | Nginx configuration path
`/usr/local/etc/nginx/sites`     | Nginx sites configuration path

File                                                | Description
--------------------------------------------------- | ------------------------------------------------------------------------------
`/usr/local/etc/php.ini`                          | PHP configuration
`/usr/local/etc/nginx/nginx.conf`                 | Global nginx configuration options
`/usr/local/etc/php-fpm.d/www.conf`               | php-fpm pool configuration
`/usr/local/etc/php/php-fpm.conf`             | PHP FPM daemon configuration

## Expand

Expand                                                | Version
--------------------------------------------------- | ------------------------------------------------------------------------------
`msgpack`                          | 0.5.7/2.0.2
`igbinary`                 | 2.0.5
`memcached`               | 2.2.0/3.0.4
`redis`             | 3.1.6
`yaml`             | 1.3.1/2.0.2
`xdebug`             | 2.5.5 Only build tags

## Build Tag

openssh-client git subversion curl wget nano unzip diffutils ntp openjdk-8-jdk yuicompressor closure-compiler nodejs-8.9.4 yarn-1.3.2

### Path
/usr/local/bin/yuicompressor.jar
/usr/local/bin/closure-compiler.jar




