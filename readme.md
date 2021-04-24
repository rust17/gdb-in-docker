## 配合 vscode 在 docker 中使用 gdb 断点调试 php 代码

### 项目目录

```
./php-src
./gdb
```

```
./php-src
    ./.vscode // 编辑器配置
    ./code/a.php // 准备 debug 的 php 代码
    ./docker-compose.yml
    ./Dockerfile
    ... // php 源代码
```

```
./gdb
    ./docker-compose.yml
    ./Dockerfile
```

### 运行

```
cd php-src

git clone git@github.com:php/php-src.git

git checkout php8.0 or php7.0... // 可以选择自己想要调试的版本

docker-compose build php-src

cd ../gdb

docker-compose build gdb

docker-compose up -d gdb
```

* 进入 vscode，安装使用 [Remote - Container](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) 进入启动的容器内

* 选择源代码的目录 file --> open --> `/var/www/php-src`

* 需要在容器内安装 [C/C++ 智能提示、断点调试插件](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

* 启动 debug，在 mac 上是点击 touch bar 开启按钮