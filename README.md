# JavaStudyNote
## 近来相关工作笔记，都是写杂碎的东西
> 在linux服务器上用sftp存储文件， sftp 用户名@ip  端口   然后输入密码；
<br /> scp -r  要上传到服务器的文件   用户名@ip:服务器路径     然后输入校验密码；
<br /> 远程命令 telnet 10.1.x.x 端口号；
<br /> grep 关键字  logs.log，这种我是不建议的，现在用elk查看日志，比命令好多了，可能在**银行会有这种操作；
<br /> 查看docker version 版本
<br /> 查看docker ps -a 查看所有的进程
<br /> 查看docker images -a 查看所有的镜像
<br /> 查看docker exec -it 容器ID /bin/bash   进入某个容器
<br /> 查看docker kill 容器ID 关闭某个容器
<br /> 查看docker inspect 容器ID 查看容器内部配置信息
<br /> 查看docker cp /etc/localime  容器ID:/etc/   把文件copy到容器
<br /> 查看docker system prune -a   然后确定写y 清掉停止的容器
<br /> 查看docker system df  分析磁盘情况
