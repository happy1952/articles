# PHP8 编译安装

```bash
$ ./configure --prefix=/usr/local/php8 \
--with-config-file-path=/usr/local/php8/etc \
--with-config-file-scan-dir=/usr/local/php8/conf.d \
--enable-fpm \
--with-fpm-user=www \
--with-fpm-group=www \
--enable-mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-iconv-dir \
--with-freetype=/usr/local/freetype \
--with-jpeg \
--with-png \
--with-zlib \
--enable-xml \
--disable-rpath \
--enable-bcmath \
--enable-shmop \
--enable-sysvsem \
--enable-inline-optimization \
--with-curl \
--enable-mbregex \
--enable-mbstring \
--enable-intl \
--enable-pcntl \
--enable-ftp \
--enable-gd \
--with-openssl \
--with-mhash \
--enable-pcntl \
--enable-sockets \
--with-xmlrpc \
--with-zip \
--without-libzip \
--enable-soap \
--with-gettext \
--enable-opcache \
--with-xsl \
--with-pear
```