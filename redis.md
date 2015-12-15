# ubuntu下的redis安装和配置。

## 最简便的方法，如果不出错的话。:smirk:

  1. 在安装之前，先`apt-get uodate` 一下。
  2. 然后直接用apt-get进行安装 `sudo apt-get install redis-server` redis的服务版。安装完成之后，键入`redis-cli`如果出现 输入模式则证明安装完成。
  3. 安装php的扩展。` sudo apt-get install php5-redis` 安装完成之后，键入` php -m` ，如果在扩展模块中看到redis则证明redis模块安装完成。
### 如果上述第二个安装方法不正确，则要进行源码安装了。
 1. 下载php-redis源码：`wget https://github.com/nicolasff/phpredis/zipball/master -O php-redis.zip`
 2. 解压缩：`unzip php-redis.zip`
 进入目录：`cd php-redis`
 运行：`phpize` （ps：这个是`php-dev`下的编译扩展的工具，如果没有的话快点执行 `sudo apt-get install php-dev`安装）
 运行配置文件以生成Makefile：`./configure`
 生成共享库(比如redis.so)：`make`
 3. 安装：`make install`
 最后会得到提示已经将软件安装到了`/usr/lib/php5/20121212/redis.so`，这个安装的本质就是把我们之前生成的共享库文件拷贝过来，就这样。
 4. 首先在`/etc/php5/mods-available/`新建`redis.ini`文件，写入下列内容，你可以参照同目录下的其他比如`php-mysql`扩展的情况

 ```
 ; configuration for php Redis module
 ; priority=20
 extension=redis.so
 ```
 然后建立链接：
 `sudo ln -s /etc/php5/mods-available/redis.ini /etc/php5/apache2/conf.d/20-redis.ini`。然后重启apache，这样就安装成功了。:kissing_heart:
