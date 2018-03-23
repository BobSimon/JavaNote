##快捷键
###	Ctrl+/ 或 Ctrl+Shift+/	注释（// 或者/*…*/ ）
###	Shift+F6	重构-重命名
###	Ctrl+X	删除行
###	Ctrl+D	复制行
###	Ctrl+G	查找行
###	Ctrl+Shift+Up/Down	代码向上/下移动。
###	F2 或Shift+F2	高亮错误或警告快速定位
###	写代码，按Tab	生成代码
###	选中文本，按Ctrl+Shift+F7	高亮显示所有该文本，按Esc高亮消失。(因为这个功能我就可以发放心的放弃sublime了)
###	Ctrl+B或Ctrl+鼠标左键单击	快速打开光标处的类或方法，（NB的功能）
###	Ctrl + Alt + B	Go to implementation(s) 跳转方法实现处
###	Ctrl + Shift + I	Open quick definition lookup 打开定义快速查找
###	Alt + Up/Down	Go to previous/next method 跳转到上一个/下一个方法
###	Ctrl+E	最近打开的文件
###	Alt+F1	查找代码所在位置
###	Ctrl+Alt+L	格式化代码
###	Ctrl+R	替换文本
###	Ctrl+F	查找文本
###	Ctrl+P	方法参数提示
###	F3	查找下一个
###	Shift+F3	查找上一个
###	alt+Shift+F	将当前文件加入收藏夹
###	ctrl+alt+s	打开配置窗口
###	ctrl+Shift+N	通过文件名快速查找工程内的文件（必记）
###	ctrl+Shift+alt+N	通过一个字符快速查找位置（必记）
###	Shift+enter	重新开始一行（无论光标在哪个位置）
###	Ctrl + Alt + T 	with…（if, else, try, catch, for, etc）用 * 来围绕选中的代码行，（ * 包括 if 、 while 、 try catch 等）
###	Ctrl + Shift + U	Toggle case for word at caret or selected block 光标所在位置大小写
###	Ctrl + Delete	Delete to word end 删除文字结束
###	Ctrl + Backspace	Delete to word start 删除文字开始
###	Ctrl + E	Recent files popup 弹出最近打开的文件
###	F11	Toggle bookmark 切换标记，我觉得叫书签更好，就是sublime text 的F2
###	Ctrl + Shift + F12	Toggle maximizing editor 切换最大化编辑器
###	Alt + Shift + F	Add to Favorites 添至收藏夹
##webstorm设置技巧
###如何更改主题（字体&配色）:
File -> settings -> Editor -> colors&fonts -> scheme name.主题下载地址

###如何让webstorm启动的时候不打开工程文件：
File -> Settings->General去掉Reopen last project on startup.

###如何完美显示中文：
File -> Settings->Appearance中勾选Override default fonts by (not recommended)，设置Name:NSimSun，Size:12

###如何显示行号：
File -> Settings->Editor，”Show line numbers”打上勾，就显示行号了

###如何代码自动换行：
File -> settings -> Editor “Use Soft Wraps in editor” 打上钩，代码就自动换行了

###如何点击光标，显示在本行末尾：
File -> Settings->Editor “Allow placement of caret after end of line”去掉勾就行了。

###如何修改快键键：
File -> Settings->Keymap，然后双击要修改快捷的功能会有提示框出来，按提示操作

###换成自己熟悉编辑器的快键键：
File ->Settings->Keymap，支持像Visual Studio、Eclipse、NetBeans这样的主流IDE。

###javascript类库提示。 
File -> settings -> Javascript -> Libraries -> 然后在列表里选择自己经常用到的javascript类库，最后Download and Install就ok了.

###在开发js时发现，需要ctrl + return 才能选候选项： 
File -> Setting -> Editor -> Code Completion -> Preselect the first suggestion: “Smart” 改为 “Always”

###js提示比较迟缓
File -> Code Completion -> Autopopup in 下 1000改为0

###git配置：
File -> settings -> Editor -> github，进去改github的账户，如果没有git则不需要.

###插件安装：
File ->plugins，然后就选择给力的插件们再安装.(“css-X-fire”插件,用于当使用firebug修改css属性时，编辑器内的css代码也会发生变化。）
