FROM ubuntu:18.04

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
	&& ./configure --enable-phpdbg-debug --enable-debug --enable-fpm \
	&& make clean \
	&& make \
	&& find -type f -name '*.a' -delete \
	&& make install