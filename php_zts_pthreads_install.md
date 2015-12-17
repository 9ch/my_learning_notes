# ubuntu下的pthreads扩展安装--基于pecl
  
  说在最前面，安装之前一定要先`apt-get update` 一下。否则安装过程中可能会出现查找不到文件的情况，因为是基于pecl，所以可能被墙，所以推荐安装一个vpn。
  首先，如果你已经安装了`php`的话，那么不好意思，你要卸载掉它，才能安装`pthreads`，
  因为`pthreads`是根据`zts`安装的，如果直接安装的话，会出一个缺少`zts`的配置，然后让你重新编译安装php。
  
  好了先说第一步：
  
## 卸载
  1. 如果是用ubuntu直接apt-get安装的php的话，则需要使用`apt-get remove --purge php5`(具体安装的那个版本则填写相对应的版本就ok了)
  2. 如果上面的没有卸载完成，则使用`apt-get autoremove --purge php5`(同上)
  3. 如果是编译安装的php的话，则找到它的安装目录。直接把目录删除就可以了。
  
## 第二步： 预先安装所需要的库
  1.在安装之前，先把编译安装过程中所需要的库预先安装上，免得安装过程中出现不必要的错误。
  `sudo apt-get install gcc make libzzip-dev libreadline-dev libxml2-dev libssl-dev libmcrypt-dev libcurl4-openssl-dev lib32bz2-dev libpng-dev`
  
## 第三步：安装php
  1. `cd /usr/local/src`
  2. `wget http://www.php.net/distributions/php-5.5.8.tar.gz` (具体版本自己选择)
  3. `tar zxvf php-5.5.8.tar.gz` 
  4. `cd /usr/local/src/php-5.5.8` 进入到解压目录安装配置
  5. 我安装的是在/usr目录下面。配置文件是在/etc目录下面，可以自己更改安装目录
  ```
  ./configure --prefix=/usr --with-config-file-path=/etc --enable-maintainer-zts --with-gd --enable-gd-native-ttf --with-openssl --with-mhash 
  --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --enable-opcache --with-pdo-mysql --disable-rpath --enable-bcmath 
  --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex --enable-fpm --enable-mbstring --with-zlib 
  --with-libxml-dir=/usr --enable-xml --with-iconv-dir
  ```
  如果此时出现一个缺少autoconf或者m4的错误，则手动进行安装:
   `apt-get install autoconf`,安装这个默认会把m4也安装上
  6. 编译安装环节：
  `make && make install`，
  7. 移动配置文件到/etc下面 
  `cp php.ini-development /etc/php.ini` （具体目录根据安装时的设置的目录为准）
  
## 第四步 ：安装pthreads
  1. 使用pecl工具下载安装`pthreads`，
   `pecl install pthreads-1.0.0`。（对了，pthreads现在默认支持php7，跟低版本的不兼容，所以在安装pthreads的时候，选择对应的版本，否则会安装不成功）
   如果提示失败的话。就说明网被墙了，此时你需要一个vpn
  2. 修改配置文件，将`pthreads`扩展文件加入到配置文件里面
  `echo "extension=pthreads.so" >> /etc/php.ini`
  
## 第五步： 检查安装是否成功
  `php -m | grep pthreads` 
  如果在命令行里面看到pthreads就说明你安装成功了
  
   © 2015 9ch
