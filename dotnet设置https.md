## dotnet设置https
-----------------------------
### 一、安装
1. #### [nginx安装](https://github.com/chi8708/Linux-.Net/blob/main/linux%E9%83%A8%E7%BD%B2mysql.md)后没配置https。报错
the "ssl" parameter requires ngx_http_ssl_module in /usr/local/nginx/conf/nginx.conf。
解决如下
- 1.1. 找到nginx安装包/usr/local/src/nginx-1.20.1/；这是我的安装包路径，每个人的都可能不一样，如果没有找到的话可以使用：find / -name "nginx"自行查找。如果还是没有可以去http://nginx.org/download/找到对应版本的安装包下载。
```
./configure --prefix=/usr/local/nginx          --with-http_stub_status_module --with-http_ssl_module
```
```
make
```
- 1.2. 替换 
    注意：在替换原有的nginx，需要先停掉原来的nginx程序，或者直接`ps -elf | grep nginx查到相关程序，直接kill -9掉。`

    然后在把安装包下的objs/nginx，覆盖原有的配置。`cp ./objs/nginx /usr/local/nginx/sbin/`

- 1.3. 重新启动nginx：
   `cd /usr/local/nginx/sbin`
   `./nginx`

2. #### 申请免费ssl证书，并添加独立配置文件，导入到nginx
```
    # 添加 GitRankMobileApi.conf独立配置文件
    server {
        listen  443 ssl;
        server_name    www.gitrk.cn;
        ssl_certificate     /home/wwwroot/cert/www.gitrk.cn.pem;
        ssl_certificate_key /home/wwwroot/cert/www.gitrk.cn.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        
        location / {
            proxy_pass https://127.0.0.1:5000; 
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection upgrade;
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }
```
```
    # 将GitRankMobileApi.conf加入nginx.conf配置文件中
    include ./GitRankMobileApi.conf;
```


### 二、常见问题


### 三、维护

### 参考
1. 配置
   - https://blog.csdn.net/Xiao_W_u/article/details/121493336
   

