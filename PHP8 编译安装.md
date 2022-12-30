# PHP8 编译安装

## 安装依赖工具

```bash
[root@localhost]$ sudo yum install re2c bison autoconf make libtool ccache libxml2-devel sqlite-devel
```

## 编译安装

```bash
# 下载 PHP 8.1.13
[root@localhost]$ wget https://www.php.net/distributions/php-8.1.13.tar.gz

# 解压
[root@localhost]$ tar -zxvf ./php-8.1.13.tar.gz
[root@localhost]$ cd php-8.1.13

# 编译安装
# 如果没有 configure 文件时，可以使用 ./buildconf 生成 configure 文件
[root@localhost]$ ./configure --prefix=/usr/local/php8.1.13 \
--with-config-file-path=/usr/local/php8.1.13/etc \
--with-config-file-scan-dir=/usr/local/php8.1.13/conf.d \
--enable-fpm \
--with-fpm-user=www \
--with-fpm-group=www \
--enable-mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-freetype=/usr/local/freetype \
--with-jpeg \
--with-zlib \
--enable-xml \
--disable-rpath \
--enable-bcmath \
--enable-shmop \
--enable-sysvsem \
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
--with-zip \
--without-libzip \
--enable-soap \
--with-gettext \
--enable-opcache \
--with-xsl \
--with-pear

[root@localhost]$ make -j4
[root@localhost]$ make install
```

## 常见问题

1. 未指明硬件架构
```code
checking whether we are cross compiling... configure: error: in `/data/wwwroot/php-8.1.13':
configure: error: cannot run C compiled programs.
If you meant to cross compile, use `--host'.
See `config.log' for more details

解决方案：
1. uname -m # 查看当前硬件架构

2. 然后在 configure 后加上 --host=x86_64，--host 后面的参数以上面命令输出为准。
```


2. 配置 opcache 时未找到支持的共享内存缓存支持

```code
configure: error: No supported shared memory caching support was found when configuring opcache. Check config.log for any errors or missing dependencies.

解决方案：
使用 --disable-opcache 临时屏蔽掉 opcache，后面再单独编译安装。
```

3. 编译报错如下：

```code
collect2: error: ld returned 1 exit status
make: *** [sapi/cgi/php-cgi] Error 1

解决方案：
1. 使用 vim 编辑 Makefile 文件

2. 在 EXTRA_LIBS = -lcrypt ... -lcrypt 这行的最后面加上一个 -liconv
```

4. libzip 版本太低

```code
configure: error: Package requirements (libzip >= 0.11 libzip != 1.3.1 libzip != 1.7.0) were not met:

解决方案：
手动升级下 libzip。
1. wget https://libzip.org/download/libzip-1.1.2.tar.gz
2. tar -zxvf libzip-1.1.2.tar.gz
3. cd libzip-1.1.2/
4. ./configure --prefix=/usr/local/libzip-1.1.2
5. make && make install
6. export PKG_CONFIG_PATH='/usr/local/libzip-1.1.2/lib/pkgconfig'
7. 或者在 configure 时指定参数 --with-zip=/usr/local/libzip-1.1.2
```