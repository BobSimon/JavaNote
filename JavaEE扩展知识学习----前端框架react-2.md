##组件实例的三大属性props,refs,state
因为自定义组件最常用就是第二种方式，采用ES6类语法方式

	class MyComponent2 extends React.Component{
          render(){
              console.log(this)
              return <h1>ES6类语法方式自定义组件</h1>
          }
      }
上述代码中打印的this中就存在组件实例的三大属性props,refs,state。在打印的原型中我们也可以找到render方法。
##组件实例的属性-props属性
1. 每一个组件对象都会有props（properties的简写）属性。
2. 组件标签的所有属性都保存在props属性中
3. 内部读取某一个属性值：this.props.propertyName
4. 作用就是通过标签属性从组件外向组件内传递数据（只读）
5. 对props中的属性值进行类型限制和必要性限制
	

	<body>
	    <div id="example1"></div>
	    <hr>
	    <script type="text/javascript" src="js/react.js"></script>
	    <script type="text/javascript" src="js/react-dom.js"></script>
	    <script type="text/javascript" src="js/babel.min.js"></script>
	    <script type="text/babel">
	     class Person extends  React.Component{
	         render(){
	             console.log(this.props)
	             return (
	                 <ul>
	                     <li>姓名：{this.props.username}</li>
	                     <li>性别：{this.props.age}</li>
	                     <li>年龄：{this.props.sex}</li>
	                 </ul>
	             )
	         }
	     }
	     let person = {username:'张三',age: 39,sex:'男'}
	     ReactDOM.render(<Person username =  {person.username} age = {person.age} sex = {person.sex}/>,document.getElementById('example1'))
	    </script>
	</body>
上述程序中就演示了如何将person传入到组件中。但是如果我们没有传入性别就默认显示男，应该怎么操作呢。

	 ReactDOM.render(<Person username =  {person.username} age = {person.age} />,document.getElementById('example1'))
这就说到了当前组件的默认属性值

	<body>
	    <div id="example1"></div>
	    <hr>
	    <script type="text/javascript" src="js/react.js"></script>
	    <script type="text/javascript" src="js/react-dom.js"></script>
	    <script type="text/javascript" src="js/babel.min.js"></script>
	    <script type="text/babel">
	     class Person extends  React.Component{
	         render(){
	             console.log(this.props)
	             return (
	                 <ul>
	                     <li>姓名：{this.props.username}</li>
	                     <li>性别：{this.props.sex}</li>
	                     <li>年龄：{this.props.age}</li>
	                 </ul>
	             )
	         }
	     }
	     //设置组件的默认props属性值
	     Person.defaultProps = {
	         sex: '第三者'
	     }
	     let person = {username:'张三',age: 39,sex:'男'}
	     ReactDOM.render(<Person username =  {person.username} age = {person.age} />,document.getElementById('example1'))
	    </script>
	</body>
对传入数据进行必要性限制，

	<body>
	    <div id="example1"></div>
	    <hr>
	    <div id="example2"></div>
	    <script type="text/javascript" src="js/react.js"></script>
	    <script type="text/javascript" src="js/react-dom.js"></script>
	    <script src="https://cdn.bootcss.com/prop-types/15.6.0/prop-types.js"></script>
	    <script type="text/javascript" src="js/babel.min.js"></script>
	
	    <script type="text/babel">
	     class Person extends  React.Component{
	         render(){
	             console.log(this.props)
	             return (
	                 <ul>
	                     <li>姓名：{this.props.username}</li>
	                     <li>性别：{this.props.sex}</li>
	                     <li>年龄：{this.props.age}</li>
	                 </ul>
	             )
	         }
	     }
	     //设置组件的默认props属性值
	     Person.defaultProps = {
	         sex: '第三者'
	     }
	     Person.propTypes  = {
	         username: PropTypes.string.isRequired,
	         age: PropTypes.number.isRequired,
	         sex: PropTypes.string.isRequired
	     };
	     let person = {username:'张三',age: 39,sex:'男'}
	     let person2 = {username:'李四',age: 36,sex:'男'}
	     ReactDOM.render(<Person username =  {person.username} age = {person.age} />,document.getElementById('example1'))
	     ReactDOM.render(<Person username =  {person2.username} age = {person2.age} />,document.getElementById('example2'))
	    </script>
	</body>
###说明：组件实例属性的必要性限制具体代码如下：

	 Person.propTypes  = {
	         username: PropTypes.string.isRequired,
	         age: PropTypes.number.isRequired,
	         sex: PropTypes.string.isRequired
	     };
上述代码中因为使用了propTypes，所以就必须引入

	 <script src="https://cdn.bootcss.com/prop-types/15.6.0/prop-types.js"></script>
当然也可以使用npm进行安装。
如果上述代码中person的属性值很多，那么如下这中传数据就显得很麻烦。没有效率，

	ReactDOM.render(<Person username =  {person2.username} age = {person2.age} />,document.getElementById('example2'))
这个时候我们可以使用打包的方式，

	ReactDOM.render(<Person {...person2} />,document.getElementById('example2'))
##嵌套组件
###1.先看实际效果
![](https://i.imgur.com/SVYHZVj.png) 
###2.下图是代码结构图
![](https://i.imgur.com/Mo3hxot.png)
从结构中我们看出这个例子包含了两个组件App和Welcome,并且是App组件包含了Welcome组件，并且Welcome组件中的数据来源遍历App组件中的属性arr数组，
###3.App组件如下

	 class App extends React.Component{
            render(){
                console.log(this.props.arr)
                let {arr} = this.props;
                return (
                        <div>
                            {
                                arr.map((item,index) => {
                                    return <Welcome key={index} name = {item}/>
                                })
                            }

                        </div>
                )
            }
        }
###4.Welcome组件如下

	function Welcome(props){
            return <h2>Welcome {props.name}</h2>
        }
###4.准备数据和渲染组件
	 let arr = ['张三','李四','王五','赵六']
     //渲染组件
     ReactDOM.render(<App arr = {arr}/>,document.getElementById("example2"))
说明：


1. 渲染组件只需要渲染最外部的组件App即可
2. 外部数据传入到App组件采用props属性即可
3. App组件遍历数组的方法，先获取数据 let {arr} = this.props;这就是Es6的对象解构赋值，props是对象，里面有一个arr结构的数据，定义一个{arr}结构来接收，对象解构赋值作用于当前作用于当前作用域。所以可以使用

	 
	
	arr.map((item,index) => {
       return <Welcome key={index} name = {item}/>
           })
当前数据在App组件中，现在要把item数据传给Welcome组件，也是使用props对象来传值，name = {item}。
Welcome获取数据的方式

	 function Welcome(props){
            return <h2>Welcome {props.name}</h2>
        }
注意：首先这里不能写

	return <h2>Welcome {this.props.name}</h2>,
因为在工厂函数创建组件中this是undefined，解决办法就是给Welcome组件增加一个props参数，使用props.name来获取数据。
##组件实例的属性-refs属性
1. 组件内的标签可以定义refs属性来标识自己
2. 在组件中可以通过this.refs.refName来得到对应了真实DOM对象
3. 作用：用于操作指定的refs属性的DOM元素对象（表单标签居多），例如
	

	<input ref = 'username'>
	this.refs.username//返回的就是input对象
###事件处理
1. 通过onXxx属性指定组件的事件处理函数（注意大小写）


* React使用的是自定义事件，而不是DOM事件
* React中的事件是通过委托的方式处理（委托给组件最外层的元素）
2. 通过event.target得到发生事件的DOM元素对象

	

	<input onFocus = {this.handleClick}/>
	handleFocus(event){
		event.target  //返回的是input对象
	}
##例子如下

	  <script type="text/babel">
	      class App extends React.Component{
	          handleClick(){
	             let value =  this.refs.msg.value;
	              alert(value);
	          }
	          render(){
	              console.log(this)
	              return (
	                      <div>
	                          <input ref="msg" type="text"/>
	                          <button onClick={this.handleClick}>提示输入数据</button>
	                          <input type="text" placeholder="失去焦点提示数据"/>
	                      </div>
	              )
	          }
	      }
	      ReactDOM.render(<App/>,document.getElementById("example2"))
    </script>
注意：
1. 使用onClick={this.handleClick}绑定事件
2. 定义事件


	 handleClick(){
         let value =  this.refs.msg.value;
          alert(value);
      }
3. 说明：render方法中的this表示的是组件实例，但是在handleClick中this却指向null,所以上述程序就不会弹出输入框的值，这里就需要我们将handleClick中的this指向组件实例。


完整代码如下

	<body>
	    <div id="example2"></div>
	    <script type="text/javascript" src="js/react.js"></script>
	    <script type="text/javascript" src="js/react-dom.js"></script>
	    <script src="https://cdn.bootcss.com/prop-types/15.6.0/prop-types.js"></script>
	    <script type="text/javascript" src="js/babel.min.js"></script>
	
	    <script type="text/babel">
	      class App extends React.Component{
	          constructor(props){
	              super(props);
	              console.log(this);//this指向的就是组件的实例对象
	              //修改this,找到原型上的函数，再强制绑定this
	              this.handleClick = this.handleClick.bind(this);
	          }
	          handleClick(){
	             let value =  this.refs.msg.value;
	              alert(value);
	          }
	          handleBlue(event){
	              alert(event.target.value);
	          }
	          render(){
	              console.log(this)
	              return (
	                      <div>
	                          <input ref="msg" type="text"/>
	                          <button onClick={this.handleClick}>提示输入数据</button>
	                          <input onBlur={this.handleBlue} type="text" placeholder="失去焦点提示数据"/>
	                      </div>
	              )
	          }
	      }
	      ReactDOM.render(<App/>,document.getElementById("example2"))
	    </script>
	</body>
说明：


1. onBlur是失去光标事件
2. 失去光标显示当前输入框内容，由于是获取当前input的内容，所以使用


	 handleBlue(event){
          alert(event.target.value);
      }
这里不在使用ref来表示input，因为使用了ref那么就还要绑定this,event.target就表示当前DOM对象。
##组件实例的属性-state属性
1. 组件被称为‘状态机’，通过更新组件的状态值来更新对应的页面显示（重新渲染）
2. 初始化状态


	 constructor(props){
          super(props);
          this.state = {
              stateProp1 : value1,
              stateProp2 : value2
          }
      }
3. 读取某一个状态


	this.state.statePropertyName
4. 更新状态（组件界面更新）


	 this.setState({
          stateProp1 : value1,
          stateProp2 : value2,
      })
###state练习实例
分析：
1. 需求分析可以得到三个组件App组件（应用的主组件）AddTodo组件（添加todo的组件）和TodoList组件（显示todo的组件）
2. 其中App组件包含AddTodo组件和TodoList组件，AddTodo组件和TodoList组件属于并列关系，由于并列组件之间无法传递数据。
3. 所以将数据源初始化在App组件

问题：
1. 内部组件如何向外部组件传递数据


完整代码如下

	<body>
	    <div id="example2"></div>
	    <script type="text/javascript" src="js/react.js"></script>
	    <script type="text/javascript" src="js/react-dom.js"></script>
	    <script src="https://cdn.bootcss.com/prop-types/15.6.0/prop-types.js"></script>
	    <script type="text/javascript" src="js/babel.min.js"></script>
	
	    <script type="text/babel">
	        //定义应用主组件
	       class App extends React.Component{
	           constructor(props){
	               super(props);
	               this.state = {
	                   todos: ['吃饭','睡觉','打豆豆']
	               };
	               //更改add的this指向
	               this.add = this.add.bind(this);
	           }
	           add(newTodo){
	            console.log(newTodo);
	            //更新状态
	            let todos = this.state.todos;
	               todos.unshift(newTodo);
	               console.log(todos)
	               this.setState({todos});
	           }
	           render(){
	               let {todos} = this.state;
	               return (
	                       <div>
	                           <h2>我是Todo列表示例</h2>
	                           <AddTodo add={this.add} length={todos.length}/>
	                           <TodoList todos = {todos}/>
	                       </div>
	               )
	           }
	       }
	       //定义AddTodo组件
	        class AddTodo extends React.Component{
	           constructor(props){
	               super(props);
	               this.addTodo = this.addTodo.bind(this);
	           }
	            addTodo(){
	                let newTodo = this.refs.newTodo.value;
	                //将数据给App组件前先判断数据是否合法（是否为空）
	                if(!newTodo.trim()){
	                    alert("输入的内容不能为空！！");
	                    return ;
	                }
	                this.props.add(newTodo)
	                this.refs.newTodo.value = '';//添加后清除输出框数据
	            }
	           render(){
	               return (
	                       <div>
	                           <input ref = "newTodo" type="text"/>
	                           <button onClick={this.addTodo}>添加一个列表,总共{this.props.length}个列表</button>
	                       </div>
	               )
	           }
	        }
	        //定义todo列表显示组件
	        class TodoList extends  React.Component{
	           render(){
	               let {todos} = this.props;
	               return(
	                       <ul>
	                           {
	                               todos.map((item,index)=> <li key ={index}>{item}</li> )
	                           }
	                       </ul>
	               )
	           }
	        }
	        //渲染组件
	        ReactDOM.render(<App/>,document.getElementById("example2"))
	    </script>
	</body>
代码分析如下
![](https://i.imgur.com/bZ1GZTy.png)
##受控组件
就是通过控制组件的state来控制组件
###先看一个示例
有一个输入框和一行文本，文本的值随着输入框中的值的改变而改变，代码如下

	<body>
	    <div id="example2"></div>
	    <script type="text/javascript" src="js/react.js"></script>
	    <script type="text/javascript" src="js/react-dom.js"></script>
	    <script src="https://cdn.bootcss.com/prop-types/15.6.0/prop-types.js"></script>
	    <script type="text/javascript" src="js/babel.min.js"></script>
	
	    <script type="text/babel">
	      class App extends React.Component{
	          //初始化数据
	          constructor(props){
	              super(props);
	              this.state = {
	                  msg:"我是亚历山大"
	              }
	              this.handleChange = this.handleChange.bind(this);
	          }
	          handleChange(event){
	              this.setState({
	                  msg: event.target.value
	              });
	          }
	          render(){
	              let {msg} = this.state;
	              return (
	                      <div>
	                          <input onChange={this.handleChange} type="text" value={msg}/>
	                          <p>{msg}</p>
	                      </div>
	              )
	          }
	      }
	        ReactDOM.render(<App/>,document.getElementById("example2"))
	    </script>
	</body>
##组件的生命周期
![](https://i.imgur.com/WlTmRfH.png)
##示例
	<body>
	    <div id="example2"></div>
	    <script type="text/javascript" src="js/react.js"></script>
	    <script type="text/javascript" src="js/react-dom.js"></script>
	    <script src="https://cdn.bootcss.com/prop-types/15.6.0/prop-types.js"></script>
	    <script type="text/javascript" src="js/babel.min.js"></script>
	
	    <script type="text/babel">
	     class LifeScyle extends React.Component{
	         constructor(props){
	             super(props);
	             this.state = {
	                 person:{
	                     name:"张三",
	                     age:34
	                 }
	             }
	             console.log("我是constructor，我被调用了")
	         }
	         //组件将要被挂载前要执行的方法
	        componentWillMount(){
	             console.log("componentWillMount方法被调用，组件将要被挂载前")
	            //发送ajax请求，开启定时器
	            setTimeout(function(){
	                this.setState ({
	                    person: {
	                        name:"李四",
	                        age:45
	                    }
	                })
	            }.bind(this),2000)
	            setTimeout(() =>{
	                this.setState ({
	                    person: {
	                        name:"王五",
	                        age:453
	                    }
	                })
	            },3000)
	        }
	        //组件挂载完毕后执行的方法
	         componentDidMount(){
	            console.log("componentDidMount方法被调用，组件挂载完毕后")
	             //过三秒后卸载组件
	             setTimeout(() =>{
	                ReactDOM.unmountComponentAtNode(document.getElementById("example2"))
	             },4000)
	             //循环定时器
	            this.intervalId =  setInterval(function(){
	                 console.log("setInterval")
	             },1000)
	         }
	         //组件将要更新的方法
	         componentWillUpdate(){
	             console.log("componentWillUpdate方法被调用，组件将要更新")
	         }
	         //组件更新完毕
	         componentDidUpdate(){
	             console.log("componentDidUpdate方法被调用，组件更新完毕")
	         }
	         //组件将要被卸载
	         componentWillUnmount(){
	             console.log("componentWillUnmount被调用，组件将要被卸载")
	             //通常在这里做一些收尾工作，例如关闭定时器
	             clearInterval(this.intervalId);
	         }
	         render(){
	             let {person} = this.state;
	             console.log("render方法被调用")
	             return (
	                 <div>{person.name}==========={person.age}</div>
	             )
	         }
	     }
	        ReactDOM.render(<LifeScyle/>,document.getElementById("example2"))
	    </script>
	</body>
###输出结果为
![](https://i.imgur.com/Dd29Qcv.png)
###React的生命周期小练习
显示一行文本，文本渐渐变淡，快要消失的时候，又渐渐变亮

	 <script type="text/babel">
        class Animation extends React.Component{
            constructor(props){
                super(props);
                this.state = {
                    opacity : 1
                }
            }
            //开启一个循环定时器，改变opacity的值
            componentDidMount(){
                let {opacity} = this.state;
                setInterval(()=>{
                    opacity -=0.05;
                    console.log(opacity)
                    //判断opacity的值
                    if(opacity < 0.1){
                        opacity = 1;
                    }
                    this.setState({opacity});//opacity:opacity同名属性可以不写
                },100)
            }
            render(){
                let opacity = this.state.opacity;
                return (
                        <div style={{opacity:opacity}}>我是逸心写手，在追逐技术的道路上越走越远！！</div>
                )
            }
        }
        ReactDOM.render(<Animation/>,document.getElementById("example2"))
    </script>
###React中的虚拟DOM的diff算法
diff算法就是计算出页面那个局部在发生变化，React只会重绘局部变化的地方，下面举一个示例

	<script type="text/babel">
      class HelloWorld extends React.Component{
          constructor(props){
              super(props);
              this.state = {
                  date: new Date()
              };
          }
          componentDidMount(){
              setInterval(()=>{
                  this.setState({
                      date: new Date()
                  })
              },1000)
          }
          render(){
              console.log('render');
              return (
                      <p>
                          Hello ,<input type="text" placeholder="输入你的姓名"/>，当前时间:{this.state.date.toTimeString()}
                      </p>
              )
          }
      }
        ReactDOM.render(<HelloWorld/>,document.getElementById("example2"))
    </script>
结果就是
![](https://i.imgur.com/ODFbgXj.png)
右侧的时间一直在变化，但是我们在输入框中输入时，输入框的值并不会消失，就说明，是局部重新绘制。这个就是diff算法的高效。