###Quartz和SpringMVC，Spring整合
1.Quartz依赖的包如下：
	
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context-support</artifactId>
		<version>${spring.version}</version>
	</dependency>
	
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-tx</artifactId>
		<version>${spring.version}</version>
	</dependency>
	
	<dependency>
		<groupId>org.quartz-scheduler</groupId>
		<artifactId>quartz</artifactId>
		<version>2.2.3</version>
	</dependency>
####使用Quartz配置作业的方式
第一种：MethodInvokingJobDetailFactoryBean，在dispatcher-servlet.xml中配置

	<bean id="simpleJobDetail"
		class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
		<property name="targetObject" ref="myBean" />
		<property name="targetMethod" value="printMessage" />
	</bean>
SimpleTrigger的相关配置

	<!-- 距离当前时间1秒之后执行，之后每隔两秒钟执行一次 -->
	<bean id="mySimpleTrigger" class="org.springframework.scheduling.quartz.SimpleTriggerFactoryBean">
	    <property name="jobDetail"  ref="simpleJobDetail"/>
	    <property name="startDelay"  value="1000"/>
	    <property name="repeatInterval"  value="2000"/>
	</bean>
其中myBean的相关代码如下

	@Component("myBean")
	public class MyBean {
		public void printMessage() {
			// 打印当前的执行时间，格式为2017-01-01 00:00:00
			Date date = new Date();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("MyBean Executes!" + sf.format(date));
		}
	}
第二种：JobDetailFactoryBean，在dispatcher-servlet.xml中配置

	<bean id="firstComplexJobDetail"
		class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
		<property name="jobClass"
			value="com.imooc.springquartz.quartz.FirstScheduledJob" />
		<property name="jobDataMap">
			<map>
				<entry key="anotherBean" value-ref="anotherBean" />
			</map>
		</property>
		<property name="Durability" value="true"/>				
	</bean>
anotherBean的相关代码如下：

	@Component("anotherBean")
	public class AnotherBean {
		public void printAnotherMessage() {
			System.out.println("AnotherMessage");
		}
	}
FirstScheduledJob类相关代码如下
	public class FirstScheduledJob extends QuartzJobBean{
	     private AnotherBean anotherBean;
	     
	     public void setAnotherBean(AnotherBean anotherBean){
	    	 this.anotherBean = anotherBean;
	     }
	
		@Override
		protected void executeInternal(JobExecutionContext arg0)
				throws JobExecutionException {
			Date date = new Date();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("FirstScheduledJob Executes!" + sf.format(date));
			this.anotherBean.printAnotherMessage();		
		}
	}

myCronTrigger的相关配置如下

	<!-- 每隔5秒钟执行一次 -->
	<bean id="myCronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
	    <property name="jobDetail"  ref="firstComplexJobDetail"/>
	    <property name="cronExpression"  value="0/5 * * ? * *"/>
	</bean>

两种方法的Scheduler的相关配置

	<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
	    <property name="jobDetails">
	        <list>
	            <ref bean="simpleJobDetail"/>
	            <ref bean="firstComplexJobDetail"/>
	        </list>
	    </property>
	    <property name="triggers">
	        <list>
	            <ref bean="mySimpleTrigger"/>
	            <ref bean="myCronTrigger"/>
	        </list>
	    </property>
	</bean>
####完整项目如下：
1.dispatcher-servlet.xml文件

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
		xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:p="http://www.springframework.org/schema/p"
		xsi:schemaLocation="http://www.springframework.org/schema/beans  
	            http://www.springframework.org/schema/beans/spring-beans.xsd  
	            http://www.springframework.org/schema/mvc  
	            http://www.springframework.org/schema/mvc/spring-mvc.xsd  
	            http://www.springframework.org/schema/context  
	            http://www.springframework.org/schema/context/spring-context.xsd"
		default-lazy-init="true">
	
		<!-- 通过mvc:resources设置静态资源，这样servlet就会处理这些静态资源，而不通过控制器 -->
		<!-- 设置不过滤内容，比如:css,jquery,img 等资源文件 -->
		<mvc:resources location="/*.html" mapping="/**.html" />
		<mvc:resources location="/css/*" mapping="/css/**" />
		<mvc:resources location="/js/*" mapping="/js/**" />
		<mvc:resources location="/images/*" mapping="/images/**" />
		<!-- 设定消息转换的编码为utf-8防止controller返回中文乱码 -->
		<bean
			class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
			<property name="messageConverters">
				<list>
					<bean
						class="org.springframework.http.converter.StringHttpMessageConverter">
						<property name="supportedMediaTypes">
							<list>
								<value>text/html;charset=UTF-8</value>
							</list>
						</property>
					</bean>
				</list>
			</property>
		</bean>
		<!-- 添加注解驱动 -->
		<mvc:annotation-driven />
		<!-- 默认扫描的包路径 -->
		<context:component-scan base-package="com.imooc.springquartz" />
	
		<!-- mvc:view-controller可以在不需要Controller处理request的情况，转向到设置的View -->
		<!-- 像下面这样设置，如果请求为/，则不通过controller，而直接解析为/index.jsp -->
		<mvc:view-controller path="/" view-name="index" />
		<bean class="org.springframework.web.servlet.view.UrlBasedViewResolver">
			<property name="viewClass"
				value="org.springframework.web.servlet.view.JstlView"></property>
			<!-- 配置jsp路径前缀 -->
			<property name="prefix" value="/"></property>
			<!-- 配置URl后缀 -->
			<property name="suffix" value=".jsp"></property>
		</bean>
	
		<bean id="simpleJobDetail"
			class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
			<property name="targetObject" ref="myBean" />
			<property name="targetMethod" value="printMessage" />
		</bean>
	
		<bean id="firstComplexJobDetail"
			class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
			<property name="jobClass"
				value="com.imooc.springquartz.quartz.FirstScheduledJob" />
			<property name="jobDataMap">
				<map>
					<entry key="anotherBean" value-ref="anotherBean" />
				</map>
			</property>
			<property name="Durability" value="true"/>				
		</bean>
		<!-- 距离当前时间1秒之后执行，之后每隔两秒钟执行一次 -->
		<bean id="mySimpleTrigger" class="org.springframework.scheduling.quartz.SimpleTriggerFactoryBean">
		    <property name="jobDetail"  ref="simpleJobDetail"/>
		    <property name="startDelay"  value="1000"/>
		    <property name="repeatInterval"  value="2000"/>
		</bean>
		
		<!-- 每隔5秒钟执行一次 -->
		<bean id="myCronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
		    <property name="jobDetail"  ref="firstComplexJobDetail"/>
		    <property name="cronExpression"  value="0/5 * * ? * *"/>
		</bean>
		
		<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		    <property name="jobDetails">
		        <list>
		            <ref bean="simpleJobDetail"/>
		            <ref bean="firstComplexJobDetail"/>
		        </list>
		    </property>
		    <property name="triggers">
		        <list>
		            <ref bean="mySimpleTrigger"/>
		            <ref bean="myCronTrigger"/>
		        </list>
		    </property>
		</bean>
	</beans>  
2.项目结构
![](https://i.imgur.com/0s16lAO.png)
基础知识就先告一段落吧！！！！