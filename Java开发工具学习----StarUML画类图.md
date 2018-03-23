###1.添加新工程
启动StarUML，然后一个名叫“New Project By Approach”的对话框会弹出，选择“Empty Project”，在右边的“Model Explorer”面板中可以看到新建的“Untitled”工程，工程的属性可以在下方的Properties面板中修改（工程名、作者等）。
###2.添加模型
通过“Model”主菜单，或者在Model Explorer面板的工程上右击，依次” Add — Model ”
###3.添加类图
通过“Model”主菜单，或右击选定模型，依次“Add Diagram — Class Diagram”。
###4.设置profile（UML轮廓）
通过“Model — Profile…”菜单去设置工程所需的profile。这决定了工程所使用的规则和约定。一定要包含”JAVA Porfile”这一项目。
###5.保存工程
立即就保存工程，这样在出现问题的时候，您就不会丢失信息。
###6.创造图表
现在，开始真正创造图表，从默认就在屏幕的左边的“Toolbox”面板选择“类”图标，然后左键单击diagram窗口的某处。这样就使用通用名字创造了一个新的类。双击，将类改名为Circle。
###7.添加属性
右击图中的目标，在弹出菜单中选择“Add”中的“Attribute”(被标示为绿色)，为其添加一个属性(或者域)，填入期望的名字“_radius”。
- 在窗体右下边的Properties面板中，找到“Type”输入框，输入double作为_radius属性的类型。
- 类的内部数据（域/属性）都是私有的，因为他们是严格由类内部使用的。所以，在Properties面板中将_radius设置为“私有”。

###8.创造 IShape interface
从toolbox中，选择“Interface”，并点击图表的某处。将其改名为IShape。创建以后，选中它。
- 在顶部工具栏，选择 “Stereotype Display” 下拉按钮，将值改变为“None”。这将改变默认的圆形形状，使其变为成长方形。
- 还是在顶部工具栏，取消选中” Suppress Operations “。这将使我们能够看到接口所拥有的方法。 
- 设定返回值类型（☆）。在“Model Explorer”中展开IShape节点，右击你刚刚创建的getArea方法，并选择“Add Parameter”。在“Properties”框中，将参数的名子变为空，将“DirectionKind”变为“RETURN”，将“Type”变为double。
- 将IShape和getArea的IsAbstract属性框打上勾，他们在图标上的名字将变为斜体。这是UML的标准，表示这是接口或者其他抽象实体。

###9.添加类和接口的关系
可以通过从toolbox中选择表示“Realization”的箭头，并从Circle拖拽向IShape，使Circle实现接口IShape。重复同样的过程，为Rectangle添加实现关系。这是添加了Circle 和 Rectangle对于IShape接口的实现关系。
- 如果想使连接线表现为直角的方式，右击连接线，并选择” Format — Line Style — Rectilinear”菜单。你通过这种方式，使箭头重叠在一起，可以使你的图看起来更整洁。

###10.添加类基于接口的行为
由于Circle和Rectangle类都实现了IShape接口，就必须有同样的行为(方法)。在“Model Explorer”面板中，复制getArea方法(按Ctrl-C或者右键点击并选择Copy菜单），并粘贴到Circle和Rectangle类。
- 这些实现了的方法在Circle和Rectangle类中都不是抽象的，而是具体的，所以取消勾选IsAbstract框。

###11.添加Pizza类
向Pizza添加double型的私有域_price，添加返回double类型的公有操作getPrice。
###12.为Pizza类添加IShape 的引用
从toolbox中选择” DirectedAssociation “箭头，点击Pizza类，并向IShape拖拽 。
- 选中箭头，在右边的“Properties”框上，将name一栏改为“has-a”，“End1.Aggregation” 一栏改为“AGGREGATE”(这个图示说明Pizza和shape对象是“聚合“的关系)。
- 将“End2.Name”一栏改为_shape，将“End2.Visibility”改为私有。这样就自动为Pizza添加一个名字为_shape，使用IShape接口的私有域。
- 为_shape创建一个“获得者”方法，名字叫做getShape，返回IShape 。这就是创建一个行为，名字是getShape，返回IShape 。

###13.添加构造函数
- 为Pizza添加构造函数，右击，在弹出的“Add” 菜单中选择“Operation”。从这里，增加一个普通的带有dboule型price参数和IShape类型shape参数的操作。
- 为Circle增加一个带有double型的radius参数的构造函数。
- 为Rectangle增加一个带有double型width和height参数的构造函数。

现在你的图应该是这样的
![](https://i.imgur.com/QpfniHL.jpg)
###14.添加Test_Pizza类
为了说明UML类图更多的功能，又增加了一个叫做“Test_Pizza”的类，它用作测试目的，并使用到Pizza和IShape类。
- 通过从toolbox中选择“Dependency”箭头，从一个类拖向他所以来的类，来添加不通类之间的依赖关系。在这个例子中， Test_Pizza 依赖于Pizza、Circle和Rectangle类，因为它实例化了它们。
- 从Properties box选择name属性，或者双击图表上的“依赖线”，可以为依赖关系添加标签。特别的是，当一类实例化另一个类，我们会把依赖线叫做“instantiates”。 你可以选中并拖动依赖线的标签，以达到更美观的效果。
- 依赖关系不会影响代码生成

###15.保存&导出项目
将图表导出为其他格式是非常有用的。您可以通过选择“File”菜单的“Export Diagram” ，并且选择合适的文件类型来执行改操作。
##StarUML高级应用
###1.生成Java stub代码
在菜单中依次选择“Tools — Java — Generate Code”。
- 从对话框中选择你的模块(这里可能Model1),点击“Next”。
- 为了使你的模块或者图标的所有类都生成stub code，选择“Select All”然后按“Next”。
- 选择一个有效的输出目录，“Next”。
- 在“Options Setup” ，请务必选中“Generate the Documentation by JavaDoc”，“Generate empty JavaDoc”，所有其他复选框不选中，“Next”。
- 现在StarUML将从你的图产生代码，点击“Finish”退出对话框。
- 现在，您可以编辑生成的代码，以增加应用。

###2.逆向工程
tarUML还可以从现有的Java代码创建一个类图，这被称为“reverse engineering”。当你想从现有的代码生成图表，或者你修改了SU生成的代码，并且想在图表中反应出来的时候，逆向工程功能就非常有用了。
- 到主菜单栏中选择“Tools — Java — Reverse Engineer…”，可以将现有的代码逆向工程。



