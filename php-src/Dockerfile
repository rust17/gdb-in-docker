FROM ubuntu:18.04

ENV PHP_INI_DIR /usr/local/etc/php
RUN set -eux; \
	mkdir -p "$PHP_INI_DIR/conf.d"; \
# allow running as an arbitrary user (https://github.com/docker-library/php/issues/743)
	[ ! -d /var/www/html ]; \
	mkdir -p /var/www/html; \
	chown www-data:www-data /var/www/html; \
	chmod 777 /var/www/html

# 替换源
RUN sed -i 's/security.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list

# 安装必要工具
RUN apt-get update \
	 && apt-get install -y build-essential autoconf bison re2c cmake \
                           libxml2-dev libsqlite3-dev
RUN apt-get clean && rm -rf /var/lib/apt/lists/

# gdb 配置文件
COPY ./.gdbinit /root/.gdbinit
RUN echo "set auto-load safe-path /" > /root/.gdbinit

# 将源代码放到容器内
COPY ./ /var/www/php-src

# 编译安装
RUN cd /var/www/php-src && ls \
	&& ./buildconf \
	&& ./configure \
		--enable-phpdbg-debug --enable-debug --enable-fpm --with-fpm-user=www-data --with-fpm-group=www-data \
		--with-config-file-path="$PHP_INI_DIR" \
		--with-config-file-scan-dir="$PHP_INI_DIR/conf.d" \
	&& make clean \
	&& make \
	&& find -type f -name '*.a' -delete \
	&& make install \
	&& make clean

RUN set -eux; \
	cd /usr/local/etc; \
	sed 's!=NONE/!=!g' php-fpm.conf.default | tee php-fpm.conf > /dev/null; \
	cp php-fpm.d/www.conf.default php-fpm.d/www.conf; \
	{ \
		echo '[global]'; \
		echo 'error_log = /proc/self/fd/2'; \
		echo; echo '; https://github.com/docker-library/php/pull/725#issuecomment-443540114'; echo 'log_limit = 8192'; \
		echo; \
		echo '[www]'; \
		echo '; if we send this to /proc/self/fd/1, it never appears'; \
		echo 'access.log = /proc/self/fd/2'; \
		echo; \
		echo 'clear_env = no'; \
		echo; \
		echo '; Ensure worker stdout and stderr are sent to the main error log.'; \
		echo 'catch_workers_output = yes'; \
		echo 'decorate_workers_output = no'; \
	} | tee php-fpm.d/docker.conf; \
	{ \
		echo '[global]'; \
		echo 'daemonize = no'; \
		echo; \
		echo '[www]'; \
		echo 'listen = 9000'; \
	} | tee php-fpm.d/zz-docker.conf

EXPOSE 9000