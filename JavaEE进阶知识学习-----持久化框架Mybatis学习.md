###JDBC操作数据库的步骤
编写sql→预编译→设置参数→执行sql→封装结果
###Mybatis接口编程
为接口自动创建一个代理对象，由代理对象去执行增删改查方法。
###SqlSession代表和数据库的一次会话
###Xml文件的dtd约束文件绑定
在xml→XML Catalog 中添加，key type选择URL
###Mybatis的configuration 配置相关参数设置如下
####properties属性是指读取外部文件
####settings设置Mybatis的配置，会调整Mybatis的运行行为，相关参数如下
1. cacheEnabled	该配置影响的所有映射器中配置的缓存全局开关	True | false	True
2. lazyLoadingEnabled	延迟加载的全局开关。当开启时，所有关联对象都会延迟加载，特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态	True | false	False
3. useColumnLable	使用列标签代替列名	True | false	True
4. defaultStatementTimeout	设置超时时间，它决定驱动等待数据库响应的秒数		
5. mapUnderscoreToCamelCase	是否开启自动驼峰命名规则映射，即从经典数据库列名A_COLUMN到经典Java属性名aColumn的类似映射	True | false	false


	<settings>
	<setting  name=” mapUnderscoreToCamelCase” value=”true” />
	</settings>
###typeAliases别名处理器，
可以为一个pojo包下所有类设置别名，默认为简单类名小写，主要用于mapper中的xml中type类型值，也可以在bean中使用@Alias(“xxx”)为其指定别名
###Mybatis接口编程返回值
例如插入一条数据：public Boolean insert();方法是可以带返回值得。
###插入数据之后显示自增主键
在xml中的插入语句中配置useGenerateKeys=”true” keyProperty=”id”
###Mybatis处理参数处理：
####单个参数传入不做什么处理，使用#{参数}获取。
####多个参数传入时Mybatis会做特殊处理，多个参数会被封装成一个map,
Key:param1…………..paramN
Value: 传入的参数值，#{}就是从map中获取指定key的值，由于这样会导致传入参数过多，混淆视线，故采用命名参数的方法，即明确指定封装参数map的key,在接口中传入的参数前使用@Param(“id”)注解
Key：使用@Param注解的值
Value：参数值
###通过#{指定的key}取出参数值
如果有多个参数正好是业务模型的数据模型，我们就可以直接传入pojo
###通过#{属性名}：取出传入的pojo的属性值
传入map，直接通过#{key}取到值
###Mybatis参数处理场景

	public Employee getEmp(@Parame(“id”) Integer id , String lastName);
		取值： id==>#{id/param1} 	lastName==>#{param2}
	public Employee getEmp(Integer id , @Parame(“e”) Employee emp);
		取值：id==>#{param1}		lastName==>#{param2.lastName/e.lastName}
特别注意：如果像下面的情况，传入Collocation（List，Set）类型或者数组，也会做特殊处理，也是把传入的list或者数组封装到map中。
Key:Collocation（collocation）,如果是list还可以使用这个key(list),数组（array）
public Employee getEmpById(List<Integer> ids);
		取值：取出第一个id的值：#{list[0]}
###Mybatis取值的两种方式：#{}和 ${} 
####第一种：#{}：是以预编译的形式，将参数设置到sql语句中，防止sql注入
####第二种：${}：取出的值直接拼接在sql语句中，会有安全问题，
当原生jdbc不支持占位符的地方我们就可以使用${}进行取值
比如分表拆分，动态选择多个参数字段升序和降序操作

	Select*from ${year}_sary where ……..
	Select * from employee order by ${name} ${order}
###如果#{}设置参数时如果为空
Oracle数据库则不能识别，报错 jdbcType OTHER,原因是：mybatis全局配置中，jdbcTypeForNull =OTHER,Oracle不支持
1.	所以需要在参数中设置jdbcType属性，如#{emain,jdbcType=null}
2.	修改全局配置文件 jdbcTypeForNull =null


###Mybatis返回Map，
返回一条记录的map： key就是列名，值就是对应的值，


	public Map<String , Object> getEmpById(Integer id);
	在xml中resultType=“map”。
多条记录封装一个map:Map<Integer,Employee>:键是这条记录的主键，值是记录封装的javabean


	@MapKey(“id”) 告诉Mybatis封装这个map的时候使用哪个属性作为map的key
	@MapKey(“id”) 
	Public map <Integer, Employee>getEmpByLastName(String lastName);
	在xml中 resultType = “Employee的全类名
###Mysql添加外键约束方法
1.	先在多的一端添加一列：  alter table 表名 add column 列名int(11);
2.	添加约束：alter table 多端表名 add constraint fk_emp_dept foreign key(多端外键列名) references 一端表名（主键）


###Mybatis多表关联查询时，
可以使用级联属性映射结果，如下在自定义的map中，查询员工时


	<result column = “dept_name” property = “dept.deptpartName”/>
Association是指定义单个对象属性的封装结果，就是一对多，多的那端的对象属性。


	<association property=”dept” javaType=”dept对象的全类名” >
	<result   />
	</association>
使用association分布查询，其实就是在一个map结果集映射中执行其他.xml文件中的方法，例如查询员工和对应的部门，1.根据员工id查询员工信息，包含了外键，2.写出根据部门id查询部门信息


	<association property = “dept” select=”com.luo.mapper.getDeptById” colum=”外键列名”></association>
使用association分布查询时对一个结果包含的对象属性可以使用延迟加载（按需加载或者懒加载），即员工信息中的部门信息，查询员工信息时候，不查询部门信息，当使用部门信息的时候才查询部门信息
配置如下：

	在全局配置文件中配置<setting name = “lazyLoadingEnabled” value = “true” />
	<setting name = “aggressiveLazyLoading” value = “false” />
###Mybatis关联查询
collection定义关联集合的封装规则，即查询部门信息的同时查询部门中所有员工信息。
使用<collection property = ”” ofType = “”>定义集合类型的封装，其中 property是集合属性名，ofType是集合属性的全类名。里面使用<result>映射列。
1.	<collection>也可以使用分布查询，根据id查询部门信息，在根据部门id查询员工信息，同<association>使用方法一样，都是调用namespace下的mapper方法。
2.	注意：使用<collection>和<association>进行分布查询时，会传入参数到另一个mapper中的sql语句中，如果需要将多列的值传递过去，可以将多列的值封装map传递，例如：
另一个sql需要#{deptid},  column=“{deptid=id}”
多个参数传递 column =  “{key1 = column1, key2 = column2}”
在<collection>中有属性fetchType = “”,=lazy表示使用延迟加载，eager表示使用立即加载，默认是延迟。


###Mybatis中的鉴别器
<discriminator  javaType = “”></discriminator >鉴别器，使用在<resultMap>可以判别某列的值，然后根据某列的值改变封装行为，例如如下查询员工场景的查询：
如果查出的是女生，就把部门信息查询出来，否则不查询，
如果是男生，就把last_name这一列的值赋给email。

	<discriminator   javaType = “String ” column = “gender”>
	<case value = “0” resultType = “员工的全类名”>
	使用<association>查询部门信息
	</case>
	<case value = “1” resultType=””></case>
	</discriminator>
###动态sql
使用if直接拼接sql会存在多一个and的问题，导致出错。解决办法可以加上1=1，后面都用and 条件，也可以使用Mybatis中的<where>标签包含if标签,where 只能去掉多出的第一个的and或者or，不能去掉最后多出的and或者or，故也存在问题。可以使用trim
Trim用法如下：使用trim标签包含if标签。

	<trim prefix = “where” suffxOverrides=”and”>
意思就是在整个if字符串前加上 where前缀，在字符串末尾去掉and
<choose>标签和<when>配合使用，可以多条件，使用单条件的查询。
###<where>封装查询条件，<set>封装跟新条件，
带了那个字段就更新那个字段。<set>标签和<if>标签配合。
###使用foreach遍历集合
例如查询指定id集合的员工信息。

	public List<Employee> getEmpsByids(List<Integer> ids)
	<select  id = “getEmpsByids” resultType = “Employee的全类名”>
	Select * from employee where id  in 
	<foreach collection = “ids” item = “item_id” seprator = “,” open = “(“ close = “)”>
	#{item_id }
	</foreach></select >
说明：collection：指定要遍历的集合，list类型的参数会特殊处理封装在map中，map的key就叫list
		Item：将当前遍历出的元素赋给指定的变量
		Seprator：每个元素之间的分隔符
		Open:遍历出所有结果拼接一个开始的字符
		Close：遍历出所有的结果拼接一个结束的字符
		Index：索引，遍历list的时候是索引
					 遍历map的时候index表示就是map的key,Item就是map的值
###使用foreach批量保存
可以使用foreach遍历，mysql支持values(),()语法

	public void addEmps(@param(“emps”)List<Employee> emps);
	<insert id = “addEmps”>
	insert into employee (last_name,gender ,email,d_id) values 
	<foreach collection=”emps” item = “emp” separator = “,”>
	(#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
	</foreach>
###两个内置参数
不只是方法传递过来的参数可以使用来判断，取值，Mybatis还提供了两个内置参数
_paramter:代表整个参数
单个参数：_parameter就是这个参数，
多个参数：参数会被封装为一个map:_parameter就是代表这个map
_databaseId:如果配置了databaseIdProvider标签，即不同的数据库，_databaseId就是代表当前数据库别名。

	使用<bind name = “” value = “”>可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值。
使用方法如下：

	<select>
	<bulid name = “_lastName” value = “‘ ’%‘ +lastName+ ‘%’ ’’”/>
	<if test = “lastName != null”>
		Where lastName like #{_lastName }
	</if></select>
说明：在模糊查询中关于首尾%的问题，可以使用“”%${}%”来接受值，由于不安全，但是#{}Z又不能拼接%，所以使用bulid属性拼接%后，再使用。
###	sql标签
抽取可重用的sql语句，使用 <include>来引用。也可以使用自定义属性
###Mybatis缓存机制,
一级缓存（本地缓存）：与数据库同一次会话期间查询到的数据会放在本地缓存中，以后如果需要获取相同的数据，直接从缓存中获取相同的数据，不再去数据库中查询数据。一次会话是指一次sqlsession。、
一级缓存失效情况：
1. Sqlsession 不同
2. sqlsession相同，查询条件不同
3. sqlsession 相同，但是两种相同查询期间夹杂了其他的增加，修改，删除操作。
4. sqlsession相同，但是手动清除了缓存


二级缓存（全局缓存）：基于namespace级别的缓存，就是一个xml文件
工作机制：
1. 一个会话，查询一条数据，这个数据就会放在当前会话的一级缓存
2. 如果会话关闭，一级缓存中的数据就会被放在二级缓存中，新的会话查询信息，就参考二级缓存。


使用方法：
1. 在全局配置文件中开启二级缓存<setting name = “cacheEnabled” value = “true” />
2. 去mapper.xml中配置使用二级缓存，在mapper中加上<cache></cache>,和select同级。
3. pojo实现序列化接口。


缓存<cache>的相关属性：

	eviction=“FIFO”：缓存回收策略：默认的是 LRU。
	LRU   –  最近最少使用的：移除最长时间不被使用的对象。
	FIFO  –   先进先出：按对象进入缓存的顺序来移除它们。	
	SOFT   –   软引用：移除基于垃圾回收器状态和软引用规则的对象。
	WEAK   –    弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。
	flushInterval：刷新间隔，单位毫秒。默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新
	size：引用数目，正整数代表缓存最多可以存储多少个对象，太大容易导致内存溢出
	readOnly：只读，true/false
	true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象 不能被修改。这提供了很重要的性能优势。
	false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些， 但是安全，因此默认是 false。
###userCache
每一个select标签都有一个userCache = “false”,一级缓存任然可以使用，关闭的是二级缓存。
###flushCache
如果增删改中的flushCache标签的等于true,一级缓存就清除了。二级缓存也会被清除。
查询标签flushCache = “false”如果等于true,每次查询之后都会清空缓存
Sqlsession.clearCache();只是清除当前session的一级缓存。
###Ehcache第三方缓存框架：使用方法如下：
1.	导入相关jar: ehcache-core-2.6.8.jarslf4j-api-1.6.1.jar   slf4j-log4j12-1.6.2.jar
2.	导入Mybatis与Ehcache的整合jar  mybatis-ehcache-1.0.3.jar
3.	在mapper.xml文件中添加<cache  type = “org.mybatis.caches.ehcache.EhcacheCache”></cache>
4.	在config文件夹中添加ehcache.xml文件
5.	如果在其他mapper中需要使用缓存，可以使用<cache-ref namespace = “使用缓存的mapper的全类名”/>


###Mybatis+Spring+SpringMVC整合方法：
1.	导入Spring架包 
2.	导入Mybatis: mybatis-3.4.1.jar
3.	数据库驱动：mysql-connector-java-5.1.37-bin.jar
4.	Mybatis与Spring整合jar: mybatis-spring-1.3.0.jar
5.	新建config文件夹，添加dbconfig.properties和mybatis-config.xml,在config文件夹中新建包，存放mapper.xml文件。
6.	在web.xml文件中配置Spring，SpringMVC
加载Spring容器，前端控制器，post乱码过滤器
7.	添加SpringMVC的配置文件


###Mybatis逆向工程
1.	简单查询所有员工，
List<Employee> emps = mapper.selectByExample(null);
2.	带条件的复杂查询
EmployeeExample example = new EmployeeExample();
Criteria  criteria = example.createCriteria();
Criteria.addXXXLike(“%e%”);
Criteria.addXXXEqualTo(“1”);
如果多个条件是and关系就用上述一直添加，如果存在or关系，则如下添加
Criteria  criteria2 = example.createCriteria();
criteria2.addXXXLike(“%e%”);
example.or(criteria2);
List<Employee> emps = mapper.selectByExample(example);

###	Mybatis运行原理解析总结

1、获取sqlSessionFactory对象:

	解析文件的每一个信息保存在Configuration中，返回包含Configuration的DefaultSqlSession；
	注意：【MappedStatement】：代表一个增删改查的详细信息
2、获取sqlSession对象

	返回一个DefaultSQlSession对象，包含Executor和Configuration;
	这一步会创建Executor对象； 
3、获取接口的代理对象（MapperProxy）

	getMapper，使用MapperProxyFactory创建一个MapperProxy的代理对象
	代理对象里面包含了，DefaultSqlSession（Executor）
4、执行增删改查方法
总结：
1、根据配置文件（全局，sql映射）初始化出Configuration对象
2、创建一个DefaultSqlSession对象，他里面包含Configuration以及Executor（根据全局配置文件中的defaultExecutorType创建出对应的Executor）
3、DefaultSqlSession.getMapper（）：拿到Mapper接口对应的MapperProxy；
4、MapperProxy里面有（DefaultSqlSession）；
5、执行增删改查方法：
		
- 调用DefaultSqlSession的增删改查（Executor）；
- 会创建一个StatementHandler对象。同时也会创建出ParameterHandler和ResultSetHandler）
- 调用StatementHandler预编译参数以及设置参数值;使用ParameterHandler来给sql设置参数
- 调用StatementHandler的增删改查方法；
- ResultSetHandler封装结果


注意：

	四大对象每个创建的时候都有一个interceptorChain.pluginAll(parameterHandler);
###PageHelper的使用

	Page<Object> page = PageHelper.startPage(5,1);
在查询前进行分页，传入当前第几页，和每页记录数
可以使用PageInfo对象得到分页信息，

	PageInfo<Employee> info = new PageInfo<>(list,5);
注意：list是指要分页数据的集合，5是指连续显示多少页，通过int[] nums = info.getNavigatepageNums()得到连续显示的页码集合。
###Mybatis批量保存
1.	在Spring配置文件中配置一个可以进行批量执行的sqlsession，如下


	<!--创建出SqlSessionFactory对象  -->
	<bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<!-- configLocation指定全局配置文件的位置 -->
		<property name="configLocation" value="classpath:mybatis-config.xml"></property>
		<!--mapperLocations: 指定mapper文件的位置-->
		<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"></property>
	</bean>
	<!--配置一个可以进行批量执行的sqlSession  -->
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg name="sqlSessionFactory" ref="sqlSessionFactoryBean"></constructor-arg>
		<constructor-arg name="executorType" value="BATCH"></constructor-arg>
	</bean>
在service中注入sqlsession

	@Autowired
	Private SqlSession sqlSession;
在方法中使用：

	EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
	Mapper.addEmployee();	
