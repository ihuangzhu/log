# Linux下编译PHP-5.2.x
本文记录了如何编译安装PHP-5.2.x低版本PHP+Nginx+Mysql共存环境

## 材料
* linux_centos
* [php-5.2.17.tar.gz](http://museum.php.net/php5/php-5.2.17.tar.gz)
* [php-5.2.17-fpm-0.5.14.diff.gz](http://php-fpm.org/downloads/php-5.2.17-fpm-0.5.14.diff.gz)
* [php-5.x.x.patch](https://mail.gnome.org/archives/xml/2012-August/txtbgxGXAvz4N.txt)
* [ZendOptimizer-3.3.9-linux-glibc23-x86_64.tar.gz](https://master.dl.sourceforge.net/project/lnmpaio/web/zend/ZendOptimizer-3.3.9-linux-glibc23-x86_64.tar.gz)

## 步骤
1. 准备php源码
    - 解压缩php-5.2.17.tar.gz源码包
    - 打php源码补丁
        ```shell script
      # cd php-5.2.x
      # patch -p0 -b < ../php-5.x.x.patch
        ```
    - 打php-fpm补丁
        ```shell script
      # gzip -cd php-5.2.17-fpm-0.5.14.diff.gz | patch -d php-5.2.17 -p1
        ```
2. 编译安装源码包
    - 编译安装
        ```shell script
      # yum -y install gcc gcc-c++ make automake autoconf
      #  yum -y install libxml2-devel openssl-devel pcre-devel sqlite-devel bzip2-deve llibcurl-devel freetype-devel gd-devel readline-devel libmcrypt-devel libcurl-devel libxslt libxslt-devel
      # ./configure --prefix=/www/server/php/52 --with-config-file-path=/www/server/php/52/etc --with-mysql=/www/server/mysql  --with-mysqli=/www/server/mysql/bin/mysql_config --with-iconv=/usr/local/libiconv --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-discard-path --enable-safe-mode --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --with-curlwrappers --enable-mbregex --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --enable-mbstring --with-mcrypt --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-ldap --with-ldap-sasl --with-xmlrpc --enable-zip --enable-soap --with-ldap=shared
      # make && make install
        ```
3. 配置fpm
    - 编辑php-fpm.conf的<section name="pool">中的选项值
        ```xml
      <section name="pool">
          <value name="name">default</value>
          <value name="listen_address">/tmp/php-cgi-52.sock</value>
          <value name="listen_options">
              <value name="backlog">-1</value>
              <value name="owner">www</value>
              <value name="group">www</value>
              <value name="mode">0666</value>
         </value>
         <value name="user">www</value>
         <value name="group">www</value>
      </section>
        ```
4. 安装ZendOptimizer
    - 安装ZendOptimizer
        ```shell script
      # tar zxvf ZendOptimizer-3.3.9-linux-glibc23-x86_64.tar.gz
      # cd ZendOptimizer-3.3.9-linux-glibc23-x86_64
      # cd data/5_2_x_comp
      # cp ZendOptimizer.so /www/server/php/52/lib/php/
        ```
    - 启用ZendOptimizer
        ```shell script
      # vi /www/server/php/52/etc/php.ini
        ```
        ```ini
      ; Zend Optimizer options
      zend_optimizer.optimization_level=15
      zend_extension=/www/server/php/52/lib/php/ZendOptimizer.so
        ```
5. 启动|关闭php-fpm服务
    - 启动|关闭|重启
        ```shell script
      # cp ZendOptimizer.so /www/server/php/52/sbin/php-fpm start|stop|restart
        ```
