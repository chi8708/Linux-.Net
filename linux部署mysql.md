### 一、安装
#### 1. 在线安装
1. `yum -y install mysql-server`
2. 
   * 查看是否安装成功`rpm -q mysql` 或`yum list installed|grep mysql`。
   * 查看位置`whereis mysql`
3. 初始化，修改表面不区分大小写`mysqld --initialize --user=mysql --lower-case-table-names=1`
4. 启动服务`service mysqld start`
5. * 查看服务运行状态 `systemctl|grep mysql`
6. 进入客户端。`mysql -u root -p` ,一般会创建个为空的密码。若密码不正确从日志文件`cat -n /var/log/mysql/mysqld.log`中查找
6. 登录成功后修改密码`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';`
7. 最好修改mysql表名不区分大小写
### 二、常见问题
#### 1. 云服务器远程登录mysql
1. 通过控制台把3306端口打开。
2. 创建%用户 `mysql> CREATE USER 'cts'@'%' IDENTIFIED BY 'Hadoop3!';`
3. 用户授权 `mysql> grant all privileges on . to 'cts'@'%';`。刷新权限flush privileges;
4. 使用Navicat进行连接
#### 2.mysql表名不区分大小写
 1. mysql8以后修改/etc/my.cnf  重启服务没有作用，mysql8后需重新初始化。查看配置` show VARIABLES like '%lower_case_table_names%'`

 2. 清空error.log为了方便查看错误日志，可以先将/var/log/mysqld.log清空。 `echo ' ' > /var/log/mysqld.log`
 3. 停用服务`service mysql stop`
 4. 删除系统数据库与用户数据库`rm -rf /var/lib/mysql`
 5. 创建数据库目录`mkdir /var/lib/mysql`
 6. 赋权
`chown -R mysql:mysql /var/lib/mysql`
`chmod 750 /var/lib/mysql`
 1. 修改 /etc/my.cnf
在[mysqld]增加
`lower_case_table_names=1`
 1. 初始化mysql
`sudo mysqld --defaults-file=/etc/my.cnf --initialize --user=mysql --basedir=/var/lib/mysql --datadir=/var/lib/mysql`

### 三、维护
1. 默认文件
   * 1.配置文件 /etc/my.cnf  
   * 2.数据库位置 /var/lib/mysql  `show VARIABLES like '%datadir%'  ` 
   * 3.日志位置 /var/log/mysql      `show VARIABLES like '%log_error%'`

### 参考
1. mysql表名不区分大小写
   - https://blog.csdn.net/chj_1224365967/article/details/125221580
   -  https://blog.csdn.net/LFP528136199/article/details/109192273
  