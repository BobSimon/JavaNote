##Linux环境下安装JDK

###创建安装目录，在/usr/java下建立安装路径，并且将安装包放在该路径下： 

	mkdi /usr/java
###Linux下有两种格式的安装文件，选择一种即可，安装包直接去官网查找
###（1）jdk-6u11-linux-i586.bin 这个是自解压的文件，在linux上安装如下：

	chmod 755 jdk-6u11-linux-i586.bin 
	./jdk-6u11-linux-i586.bin
###注意：这个步骤一定要在jdk-6u11-linux-i586.bin所在目录下
###然后按提示输入即可。
###（2）若是用jdk-6u11-linux-i586-rpm.bin 这个也是一个自解压文件，不过解压后的文件是jdk-6u11-linux-i586-rpm 包，执行rpm命令装到linux上就可以了。安装如下：

	chmod 755 ./jdk-6u11-linux-i586-rpm  
	./jdk-6u11-linux-i586-rpm .bin 
	rpm -ivh jdk-6u11-linux-i586-rpm 
###之后按提示输入。
###安装完成后JDK路径是：/usr/java/jdk1.6.0_45
##2 配置环境变量
###首先进入编辑Linux配置环境变量的文件：

	vi /etc/profile 
###在里面添加如下内容：

	JAVA_HOME=/usr/java/jdk1.6.0_45

	JAVA_BIN=/usr/java/jdk1.6.0_45/bin
	
	PATH=$PATH:$JAVA_HOME/bin
	
	CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	
	export JAVA_HOME JAVA_BIN PATH CLASSPATH
###让/etc/profile文件修改后立即生效 ,使用如下命令:

	. /etc/profile
###注意: . 和 /etc/profile 之间有空格
##3 测试
###输入：

	java -version 
###屏幕出现如下内容则说明安装成功

	java version "jdk1.6.0_45"
	Java(TM) 2 Runtime Environment, Standard Edition (build jdk1.6.0_45)
	Java HotSpot(TM) Client VM (build jdk1.6.0_45, mixed mode)