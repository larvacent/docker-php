# 基于 Ubuntu xenial 构建的 PHP执行环境

## Build Status

[![Build Status](https://travis-ci.org/larvacent/docker-php.svg?branch=master)](https://travis-ci.org/larvacent/docker-php) 

## Container layout

Container                               | Distribution name        | PHP Version
--------------------------------------- | ------------------------ | --------------
`larvacent/php:5.6`      | xenial (LTS)             | PHP 5.6
`larvacent/php:7.0`      | xenial (LTS)             | PHP 7.0
`larvacent/php:7.1`      | xenial (LTS)             | PHP 7.1
`larvacent/php:7.2`      | cosmic (LTS)             | PHP 7.2
`larvacent/php:7.3`      | cosmic (LTS)             | PHP 7.3

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
