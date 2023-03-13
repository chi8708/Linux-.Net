## linux部署nginx
-----------------------------
### 一、安装
#### 1. 在线安装
1. `yum -y install nginx`
2. * 查看位置`whereis nginx`
#### 2. 离线安装
下载离线安装包http://nginx.org/en/download.html 
1. 安装nginx所需环境
    - [ ]  1.1 安装 gcc
    命令：yum install gcc-c++
    （弹出选项，输入 y 即可）
   - [ ]  1.2 安装 PCRE
    命令：yum install -y pcre pcre-devel
   - [ ]  1.3 安装 zlib
    命令：yum install -y zlib zlib-devel
   - [ ]  1.4 安装 openssl
    命令： yum install -y openssl openssl-devel
2. 编译安装
   - [ ] 2.1 上传压缩包到指定目录
   - [ ] 2.2 解压nginx压缩包：tar -zxvf nginx-1.8.0.tar.gz
   - [ ] 2.3 查询配置参数，各个参数意思，此处不列举：./configure --help
   - [ ] 2.4 参数设置（仅供参考，路径配置可不同，均属于加载，安装路径，可直接使用）
   `./configure
   –prefix=/usr/local/nginx
   –pid-path=/var/run/nginx/nginx.pid
   –lock-path=/var/lock/nginx.lock
   –error-log-path=/var/log/nginx/error.log
   –http-log-path=/var/log/nginx/access.log
   –with-http_gzip_static_module
   –http-client-body-temp-path=/var/temp/nginx/client
   –http-proxy-temp-path=/var/temp/nginx/proxy
   –http-fastcgi-temp-path=/var/temp/nginx/fastcgi
   –http-uwsgi-temp-path=/var/temp/nginx/uwsgi
   –http-scgi-temp-path=/var/temp/nginx/scgi`
<br/>注意：此处将临时文件路径设置为：/var/temp/nginx/client，需要手动在var目录下新建temp，在temp下新建nginx目录，以及下面的client。`mkdir -p /var/temp/nginx/client`

   - [ ] 2.5 编译安装：make install (此处命令，在解压后的nginx-1.8.0目录下直接执行，nginx安装在：/usr/local/nginx)
   - [ ] 2.6 查看安装路径
   `cd /usr/local/nginx`有三个文件夹：conf、sbin、html
   conf下面是配置文件，主要是nginx.conf
   sbin下面是nginx文件。
   - [ ] 2.7 启动nginx：
   `cd /usr/local/nginx/sbin`
   `./nginx`
    - [ ] 2.8 查询nginx进场：`ps aux|grep nginx`
   后面跟着./nginx是主进程
   另一个则是工作进程
    - [ ] 2.8 停止nginx
   方式一：`./nginx -s stop ()`
   此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。
   方式二：`./nginx -s quit`
   此方式停止步骤是待nginx进程处理任务完毕进行停止。
   - [ ] 2.9 重启nginx
   `./nginx -s reload`
   用于改动了nginx.conf 后重新加载配置文件，使配置文件生效。
   - [ ] 2.10 测试是否成功（ip+80默认端口）
  #### 3.开机自启动nginx
 - [ ] 3.1 vi /etc/init.d/nginx 输入下面内容
   作用：可以直接使用此处脚本对ng进行操作，如
   查看状态：/etc/init.d/nginx status
   启动：/etc/init.d/nginx start
   停止：/etc/init.d/nginx stop
   重启：/etc/init.d/nginx restart
   ```
      #!/bin/bash
      # nginx Startup script for the Nginx HTTP Server
      # it is v.0.0.2 version.
      # chkconfig: - 85 15
      # description: Nginx is a high-performance web and proxy server.
      #              It has a lot of features, but it's not for everyone.
      # processname: nginx
      # pidfile: /var/run/nginx.pid
      # config: /usr/local/nginx/conf/nginx.conf
      nginxd=/usr/local/nginx/sbin/nginx
      nginx_config=/usr/local/nginx/conf/nginx.conf
      nginx_pid=/var/run/nginx.pid
      RETVAL=0
      prog="nginx"
      # Source function library.
      . /etc/rc.d/init.d/functions
      # Source networking configuration.
      . /etc/sysconfig/network
      # Check that networking is up.
      [ ${NETWORKING} = "no" ] && exit 0
      [ -x $nginxd ] || exit 0
      # Start nginx daemons functions.
      start() {
      if [ -e $nginx_pid ];then
         echo "nginx already running...."
         exit 1
      fi
         echo -n $"Starting $prog: "
         daemon $nginxd -c ${nginx_config}
         RETVAL=$?
         echo
         [ $RETVAL = 0 ] && touch /var/lock/subsys/nginx
         return $RETVAL
      }
      # Stop nginx daemons functions.
      stop() {
            echo -n $"Stopping $prog: "
            killproc $nginxd
            RETVAL=$?
            echo
            [ $RETVAL = 0 ] && rm -f /var/lock/subsys/nginx /var/run/nginx.pid
      }
      # reload nginx service functions.
      reload() {
         echo -n $"Reloading $prog: "
         #kill -HUP `cat ${nginx_pid}`
         killproc $nginxd -HUP
         RETVAL=$?
         echo
      }
      # See how we were called.
      case "$1" in
      start)
            start
            ;;
      stop)
            stop
            ;;
      reload)
            reload
            ;;
      restart)
            stop
            start
            ;;
      status)
            status $prog
            RETVAL=$?
            ;;
      *)
            echo $"Usage: $prog {start|stop|restart|reload|status|help}"
            exit 1
      esac
      exit $RETVAL
   ```
  - [ ] 3.2 设置该文件为所有用户可以访问
   `chown a+x /etc/init.d/nginx`（a 是all的意思，x 是execute的意思）
   #### 4.部署一个静态文件
  - [ ] 4.1 打开安装目录下的配置文件（这里是：/usr/loacal/nginx/conf/nginx.conf）
  `vi nginx.conf`
   在location中配置路径和页面，如下
   ```
      server {
      40	        listen       8918;
      41	        server_name  localhost;
      42	
      43	        #charset koi8-r;
      44	
      45	        #access_log  logs/host.access.log  main;
      46	
      47	        location / {
      48	            root   /home/wwwroot/CNet/dist;
      49	            index  index.html index.htm;
      50	        }
   ```
 - [ ] 4.2 ifconfig查看端口，ip+port访问测试，出来也页面就成功了。
 - [ ] 4.3 测试。查看端口是否通` telnet 127.0.0.1 8918`。
  调用`curl http://127.0.0.1:8918`或`wget http://127.0.0.1:8918`


### 二、常见问题
1. 静态文件压缩，修改配置文件
   ``` gzip on;
	 gzip_min_length 100;
	 gzip_types text/plain text/css application/xml application/javascript;
	 gzip_vary on;
    ```

### 三、维护

### 参考
1. 官网
   - http://nginx.org/en/download.html
   - https://nginx.p2hp.com/
2. 离线安装
   - https://developer.aliyun.com/article/1130619
   - https://www.runoob.com/linux/nginx-install-setup.html