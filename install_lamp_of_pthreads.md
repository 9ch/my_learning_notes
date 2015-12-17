# 安装基于pthreads扩展的lamp环境--ubuntu

## 安装apache

1. 首先安装`apache`所需要的扩展库。
  1. `apr`的安装
    1.去apr下载地址 http://apr.apache.org/ 下载源码
	2.解压到`/usr/src`，在下载目录执行`tar -zxvf apr-1.4.6.tar.gz -C /usr/src/ `
	3.进入目录`usr/src/apr-1.4.6`,执行`./configure --prefix=/usr/local/apr；make && make install`
  2. `apr-util`的安装
    1.`apr-util`下载地址 http://apr.apache.org/ 下载源码
	2.解压到/usr/src，在下载目录执行`tar -zxvf apr-util-1.5.1.tar.gz -C /usr/src/ `
	3.进入目录`/usr/src/apr-util-1.5.1`,执行`./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr；make && make install`
  3. `pcre`的安装
    1.去pcre下载地址 http://pcre.org/ 下载源码
	2.解压到`/usr/src`，在下载目录执行`tar -zxvf pcre-8.38.tar.gz -C /usr/src/ `
	3.进入目录`/usr/src/pcre-8.38,执行./configure --prefix=/usr/local/pcre；make；make install`
2. 安装`apache`
  1.进入目录`/usr/src/httpd-2.4.3 `
  2.`./configure --prefix=/usr/local/apache2 --with-mpm=worker --enable-cache --enable-disk-cache --enable-mem-cache --enable-file-cache --enable-nonportable-atomics --enable-mods-shared=most --enable-so --enable-rewrite --enable-ssl --with-apr=/usr/local/apr/ --with-apr-util=/usr/local/apr-util/ --with-pcre=/usr/local/pcre`
  3.执行 `make && make install`
  4.安装成功后，启动文件为`/usr/local/apache2/bin/apachectl`
     `/usr/local/apache2/bin/apachectl stop` 停止服务
     `/usr/local/apache2/bin/apachectl start` 开始服务
     `/usr/local/apache2/bin/apachectl restart` 重启服务
  5.apache下面的默认文件目录为：`/usr/local/apache2/htdocs`
  6.配置文件为：`/usr/local/apache2/conf/httpd.conf`
## 安装mysql
  1. `sudo apt-get install mysql-server` 安装服务端，此时会让你输入root密码。
  2. `sudo apt-get install mysql-client` 安装客户端，安装完成后，在命令行输入`mysql -u root -p`,如果进入到mysql客户端的话，则证明安装成功。
  
## 安装php5
  1. 先把依赖关系库给安装完，以免编译过程中出错
  ` sudo apt-get install gcc make libzzip-dev libreadline-dev libxml2-dev libssl-dev libmcrypt-dev libcurl4-openssl-dev lib32bz2-dev libpng-dev`
  2.进入到`/usr/local/src`目录下面
  3.下载
  `wget http://www.php.net/distributions/php-5.5.8.tar.gz`(具体的版本自己选择)
  4.解压
  `tar zxvf php-5.5.8.tar.gz`
  5.进入解压目录
  `cd /usr/local/src/php-5.5.8`
  6.安装
  我安装的是在/usr/local/php5目录。配置文件是在/etc目录下面，可以自己更改安装目录  `./configure --prefix=/usr/local/php5 --with-config-file-path=/etc  -with-apxs2=/usr/local/apache2/bin/apxs --enable-maintainer-zts --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --enable-opcache --with-pdo-mysql --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex --enable-fpm --enable-mbstring --with-zlib --with-libxml-dir=/usr --enable-xml --with-iconv-dir  --with-pear`如果此时出现一个缺少autoconf或者m4的错误，则手动进行安装: apt-get install autoconf,安装这个默认会把m4也安装上
  7. `make && make install`
  8.安装完成之后，要手动添加php.ini文件。
  `cp php.ini-development /etc/php.ini `
  9.安装完成。
  
## 安装pthreads扩展。
  1.下载pthreads，到pecl.php.net找到pthreads，并下载相应的版本。我这里下载的是pthreads-2.0.9版本。
  2.解压。
  `tar -zxvf pthreads-2.0.9.tar.gz /usr/local/src`
  3.编译，安装
  `cd /usr/local/src/pthreads-2.0.9`
  `/usr/local/php5/bin/phpize`
  `./configure --with-php-config=/usr/local/php5/bin/php-config`
  `make && make install`
  4.将pthreads扩展加入到php.ini文件里面
  `cp php.ini-development /etc/php.ini `
## 配置apache解析php
  1.打开apache的配置文件。`vim /usr/local/apache/conf/httpd.conf`，在
`LoadModule php_module     modules/libphp5.so`这行下面加入`PHPIniDir /etc/php.ini`
  2.在  <IfModule   dir_module/>目录里面更改下面信息
  ```
 <IdModule dir_module/>
    DirectoryIndex index.html index.php
 </IfModule>
 ```
 3.在`AddType application/x-gzip .gz .tgz`下面加入：
 ```
  AddType application/x-httpd-php .php
  AddType application/x-httpd-php-source .phps
 ```
 4.重启apache。 `/usr/local/apache2/bin/apachectl restart`
 5.在`/usr/local/apache2/htdocs`下面新建一个test.php 在里面输入`<?php phpinfo();?>`
 6.在浏览器里面输入localhost/test.php。如果出现phpinfo的信息且里面有pthreads的扩展项。则证明安装成功。
## 说一些再安装过程中常出现的一些问题
 1.在编译过程中。如果出现什么错误，一般情况下都是缺少一些库，这时需要找到对应的库，并且安装上，则问题一般都能解决。例如：在安装apache过程中，可能出现无法解析g++的错误
 则此时可以安装一个g++扩展（`apt-get install g++`），问题就可以得到解决。
 2.不同的apache版本里面的配置文件可能会有不同。所以在配置解析php时，配置项可能会有所不同。这里给出的配置项这是一个参考。具体的配置方式，大家可以google一下。
 3.安装顺序，php一定要是最后安装的。安装php的过程中，要在./configure 里面加载配置apache和mysql的配置项及扩展库，这时是需要预先安装好apache和mysql的。
 
### 以上是在安装基于pthreads扩展的lamp环境时，所总结的lamp安装方法，里面掺杂了各种本人对于此安装方法的理解，如果有什么不足之处，请各位大神指出。
@copyright 9ch
  