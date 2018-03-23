webstrom 使用itar + tab 按键 可以方便的生成foreach 的代码片段


经常写function 第一行经常是var that =this; 重复敲很麻烦，
可以添加一个代码片段 方法如下：
alt+ctrl+s 调出setting，搜索live template
在javascrpt 模板线面点击"+" 添加一个模板


模板内容如下
$FUNCTIONNAME$:function(){
    var that =  this
},
 


$XX$ 是变量
重要：需要选择下面的“define” 按钮，选择会呼出这个代码片段的文档类型（我们这里选择 javascript）
点击apply 保存，


在javascript 代码中键入 kfun+"tab" 则可以出现刚才的代码片段，光标定位在函数名变量位置，可以对其进行修改