# 从本地构建 php-src 镜像的基础上进行构建
FROM php-src:local

RUN sed -i 's/security.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list

RUN apt-get update \
	 && apt-get install -y gdb vim

RUN apt-get clean && rm -rf /var/lib/apt/lists/

RUN echo "gdb-php-src started ...." > /start.md

CMD ["tail", "-f", "/start.md"]
