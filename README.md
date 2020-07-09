# JavaStudyNote
## 近来相关工作笔记，都是写杂碎的东西
> 在linux服务器上用sftp存储文件， sftp 用户名@ip  端口   然后输入密码；
<br /> scp -r  要上传到服务器的文件   用户名@ip:服务器路径     然后输入校验密码；
<br /> 远程命令 telnet 10.1.x.x 端口号；
<br /> grep 关键字  logs.log，这种我是不建议的，现在用elk查看日志，比命令好多了，可能在**银行会有这种操作；
<br /> docker version 版本
<br /> docker ps -a 查看所有的进程
<br /> docker images -a 查看所有的镜像
<br /> docker exec -it 容器ID /bin/bash   进入某个容器
<br /> docker kill 容器ID 关闭某个容器
<br /> docker inspect 容器ID 查看容器内部配置信息
<br /> docker cp /etc/localime容器ID:/etc/   把文件copy到容器
<br /> docker system prune -a   然后确定写y 清掉停止的容器
<br /> docker system df  分析磁盘情况

> 线上碰到的问题
>> Java.lang.NoClassDefFoundError:Could not initialize class sun.awt.X11GraphicsEnvironment 
后面还有很多行抛出的错误提示；这个问题当时在开发环境是没问题的，但是在服务器环境不能正常渲染验证码。
这个服务器如果是weblogic的话，vim setDomainEnv.sh编辑这个文件夹，在该文件的的JAVA_OPTIONS="" ,在这个双引号中加入-Djava.awt.headless=true
重启下weblogic就好了。
如果部署在tomcat上的话，在catalina.sh中加上 一句 CATALINA_OPTS=-Djava.awt.headless=true

>>  sftp上传文件遇到的问题，抛出No Configuration was registered that can handle the configuration named com.sun.security.jgss.krb5.initiate
这个在开发环境是能正常上传文件比如上传图片，在sit环境也是可以的，后面在uat环境是不可以的。
后面尝试对jar版本降低，这个问题就没有了。
sftp这里用的jar是jsch







