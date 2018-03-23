###Maven是什么？
Maven是一款服务于Java平台的自动化构建工具
###安装Maven核心程序
1. 查看系统是否配置的JAVA_HOME环境变量
2. 配置Maven相关的环境变量:MAVEN_HOME或M2_HOME和path
3. M2_HOME: D:\javasoft\apache-maven-3.2.2
4. path: D:\javasoft\apache-maven-3.2.2\bin
5. 验证是否安装成功：mvn –v

###Maven的核心概念
###常用的Maven命令
执行与构建过程相关的maven命令，必须进入pom.xml所在的目录，与构建过程相关：编译，测试，打包
常用命令：

	[1]mvn clean:清理
	[2]mvn compile:编译主程序
	[3]mvn test-compile:编译测试程序
	[4]mvn test:执行测试
	[5]mvn package:打包
###在Eclipse中使用Maven
1. installations:指定Maven核心程序的位置，位于window-perfence-maven
2. user settings:指定conf/settings.xml的位置,进而获取
设置通过Maven创建的工程的JDK版本——一劳永逸

- 打开settings.xml文件
- 找到profiles标签
- 加入如下配置


	<profile>
		<id>jdk-1.7</id>
		<activation>
			<activeByDefault>true</activeByDefault>
			<jdk>1.7</jdk>
		</activation>
		<properties>
			<maven.compiler.source>1.7</maven.compiler.source>
			<maven.compiler.target>1.7</maven.compiler.target>
			<maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>
		</properties>
	</profile>







