## linux部署mysql
-----------------------------
### 一、安装
#### 1. 在线安装
  介绍centos8下.net5环境搭建、创建.net5程序、设置开机自动启动（注册为服务运行）
1. 执行命令
`rpm -Uvh https://packages.microsoft.com/config/centos/8/packages-microsoft-prod.rpm`
2. 安装SDK
`yum install dotnet-sdk-5.0`
3. 将.net发布的项目上传至一个目录如/home/testApp
4. 运行项目`cd /home/testApp   dotnet testApp.dll`
5. 设置为服务，并开机启动
- 1. 创建服务`vim /etc/systemd/system/testApp.service`
    写入下面内容
    ``` [Unit]
        Description=testApp for centos7

        [Service]
        WorkingDirectory=/home/testApp/Release
        ExecStart=/usr/bin/dotnet /home/testApp/Release/testApp.dll
        Restart=always
        RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
        SyslogIdentifier=dotnet-testApp
        User=root
        Environment=ASPNETCORE_ENVIRONMENT=Production
        Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

        [Install]
        WantedBy=multi-user.target ``` 
- 2. 设置开机自动启动testApp.service服务` systemctl enable testApp.service`
- 3. 开启服务，并查询状态
`systemctl start testApp.service`
`systemctl status testApp.service`

### 二、常见问题

### 三、维护
1. 部署成功，需设置为服务，并开机启动。

### 参考
- https://cnbing.net/Detail/2.html