## 一. C/C++程序的产生过程
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1722676836682-4e7fd15a-29f8-44ff-80cc-b519888a9ad4.png)

### 1.1.前置知识(clangd、cmake配置等)
解决中文乱码：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1722730608450-b6116fee-313e-47ab-bc18-f809f40f43c9.png)

visual studio 2010代码补全快捷键：ctrl + j

visual studio code中配置clangd解决包含头文件报错：

```json
"clangd.arguments": [
  "--header-insertion=never",
  "--completion-style=detailed",
  "--query-driver=D:\\dev\\mingw64\\bin\\g++*"
],
"clangd.fallbackFlags": [
  "-std=c++11",
  "-Ic:/Users/17624/Desktop/inc",
  "-I${workspaceFolder}/include",
  "--target=x86_64-w64-windows-gnu"
]
```

<font style="color:rgb(49, 70, 89);">逐一解释：</font>

+ <font style="color:rgb(49, 70, 89);">header-insetion:是否自动引入头文件，这里设置的是从不。</font>
+ <font style="color:rgb(49, 70, 89);">completion-style:设置提示格式，这里设置的是展示所有细节。</font>
+ <font style="color:rgb(49, 70, 89);">query-driver：设置clangd server的驱动库路径，这里我设置了mingw的路径，但似乎没有效果（clangd仍然链接到了MSVC当中）</font>
+ <font style="color:rgb(49, 70, 89);">std：设置c++标准</font>
+ <font style="color:rgb(49, 70, 89);">-I：设置用户自定义的库路径</font>
+ <font style="color:rgb(49, 70, 89);">--target：设置链接目标，这里改成了GNU，即对应MinGW</font>
+ <font style="color:rgb(49, 70, 89);"></font>

<font style="color:rgb(49, 70, 89);">CMakeLists.txt配置：</font>

```cmake
# 配置cmake最小版本
cmake_minimum_required(VERSION 3.29.0)

# 配置项目名称
project(cpp01)

# 配置cpp标准
set(CMAKE_CXX_STANDARD 17)

# 配置外置头文件
include_directories(C:/Users/17624/Desktop/inc)

# 配置当前项目下头文件目录
include_directories(./includes)

# 配置外置库文件
link_libraries(libeasyx.a)

# 配置编译目标路径并设置变量名
aux_source_directory(./src SRC_LIST)

# 配置资源路径并将资源目录添加为一个源文件目录，即可将图片编译到build目录下
set(RESOURCE_DIR "./resources")
file(COPY ${RESOURCE_DIR} DESTINATION ${CMAKE_BINARY_DIR})

# 配置执行
add_executable(${PROJECT_NAME} ${SRC_LIST})
```

配置clangd代码格式化：

```cmake
# 语言: None, Cpp, Java, JavaScript, ObjC, Proto, TableGen, TextProto
Language:  Cpp
# BasedOnStyle:  LLVM
# 访问说明符(public、private等)的偏移
AccessModifierOffset:  -4
# 开括号(开圆括号、开尖括号、开方括号)后的对齐: Align, DontAlign, AlwaysBreak(总是在开括号后换行)
AlignAfterOpenBracket:  AlwaysBreak
# 连续赋值时，对齐所有等号
AlignConsecutiveAssignments:  true
# 连续声明时，对齐所有声明的变量名
AlignConsecutiveDeclarations:  true
# 左对齐逃脱换行(使用反斜杠换行)的反斜杠
AlignEscapedNewlinesLeft:  true
# 水平对齐二元和三元表达式的操作数
AlignOperands:  true
# 对齐连续的尾随的注释
AlignTrailingComments:  true
# 允许函数声明的所有参数在放在下一行
AllowAllParametersOfDeclarationOnNextLine:  true
# 允许短的块放在同一行
AllowShortBlocksOnASingleLine:  false
# 允许短的case标签放在同一行
AllowShortCaseLabelsOnASingleLine:  false
# 允许短的函数放在同一行: None, InlineOnly(定义在类中), Empty(空函数), Inline(定义在类中，空函数), All
AllowShortFunctionsOnASingleLine:  Empty
# 允许短的if语句保持在同一行
AllowShortIfStatementsOnASingleLine:  false
# 允许短的循环保持在同一行
AllowShortLoopsOnASingleLine:  false
# 总是在定义返回类型后换行(deprecated)
AlwaysBreakAfterDefinitionReturnType:  None
# 总是在返回类型后换行: None, All, TopLevel(顶级函数，不包括在类中的函数), 
#   AllDefinitions(所有的定义，不包括声明), TopLevelDefinitions(所有的顶级函数的定义)
AlwaysBreakAfterReturnType:  None
# 总是在多行string字面量前换行
AlwaysBreakBeforeMultilineStrings:  false
# 总是在template声明后换行
AlwaysBreakTemplateDeclarations:  false
# false表示函数实参要么都在同一行，要么都各自一行
BinPackArguments:  true
# false表示所有形参要么都在同一行，要么都各自一行
BinPackParameters:  true
# 大括号换行，只有当BreakBeforeBraces设置为Custom时才有效
BraceWrapping:   
  # class定义后面
  AfterClass:  false
  # 控制语句后面
  AfterControlStatement:  false
  # enum定义后面
  AfterEnum:  false
  # 函数定义后面
  AfterFunction:  false
  # 命名空间定义后面
  AfterNamespace:  false
  # ObjC定义后面
  AfterObjCDeclaration:  false
  # struct定义后面
  AfterStruct:  false
  # union定义后面
  AfterUnion:  false
  # catch之前
  BeforeCatch:  true
  # else之前
  BeforeElse:  true
  # 缩进大括号
  IndentBraces:  false
# 在二元运算符前换行: None(在操作符后换行), NonAssignment(在非赋值的操作符前换行), All(在操作符前换行)
BreakBeforeBinaryOperators:  NonAssignment
# 在大括号前换行: Attach(始终将大括号附加到周围的上下文), Linux(除函数、命名空间和类定义，与Attach类似), 
#   Mozilla(除枚举、函数、记录定义，与Attach类似), Stroustrup(除函数定义、catch、else，与Attach类似), 
#   Allman(总是在大括号前换行), GNU(总是在大括号前换行，并对于控制语句的大括号增加额外的缩进), WebKit(在函数前换行), Custom
#   注：这里认为语句块也属于函数
BreakBeforeBraces:  Custom
# 在三元运算符前换行
BreakBeforeTernaryOperators:  true
# 在构造函数的初始化列表的逗号前换行
BreakConstructorInitializersBeforeComma:  false
# 每行字符的限制，0表示没有限制
ColumnLimit:  200
# 描述具有特殊意义的注释的正则表达式，它不应该被分割为多行或以其它方式改变
CommentPragmas:  '^ IWYU pragma:'
# 构造函数的初始化列表要么都在同一行，要么都各自一行
ConstructorInitializerAllOnOneLineOrOnePerLine:  false
# 构造函数的初始化列表的缩进宽度
ConstructorInitializerIndentWidth:  4
# 延续的行的缩进宽度
ContinuationIndentWidth:  4
# 去除C++11的列表初始化的大括号{后和}前的空格
Cpp11BracedListStyle:  false
# 继承最常用的指针和引用的对齐方式
DerivePointerAlignment:  false
# 关闭格式化
DisableFormat:  false
# 自动检测函数的调用和定义是否被格式为每行一个参数(Experimental)
ExperimentalAutoDetectBinPacking:  false
# 需要被解读为foreach循环而不是函数调用的宏
ForEachMacros:  [ foreach, Q_FOREACH, BOOST_FOREACH ]
# 对#include进行排序，匹配了某正则表达式的#include拥有对应的优先级，匹配不到的则默认优先级为INT_MAX(优先级越小排序越靠前)，
#   可以定义负数优先级从而保证某些#include永远在最前面
IncludeCategories: 
  - Regex:  '^"(llvm|llvm-c|clang|clang-c)/'
    Priority:  2
  - Regex:  '^(<|"(gtest|isl|json)/)'
    Priority:  3
  - Regex:  '.*'
    Priority:  1
# 缩进case标签
IndentCaseLabels:  false
# 缩进宽度
IndentWidth:  3
# 函数返回类型换行时，缩进函数声明或函数定义的函数名
IndentWrappedFunctionNames:  false
# 保留在块开始处的空行
KeepEmptyLinesAtTheStartOfBlocks:  true
# 开始一个块的宏的正则表达式
MacroBlockBegin:  ''
# 结束一个块的宏的正则表达式
MacroBlockEnd:  ''
# 连续空行的最大数量
MaxEmptyLinesToKeep:  1
# 命名空间的缩进: None, Inner(缩进嵌套的命名空间中的内容), All
NamespaceIndentation:  Inner
# 使用ObjC块时缩进宽度
ObjCBlockIndentWidth:  4
# 在ObjC的@property后添加一个空格
ObjCSpaceAfterProperty:  false
# 在ObjC的protocol列表前添加一个空格
ObjCSpaceBeforeProtocolList:  true
# 在call(后对函数调用换行的penalty
PenaltyBreakBeforeFirstCallParameter:  19
# 在一个注释中引入换行的penalty
PenaltyBreakComment:  300
# 第一次在<<前换行的penalty
PenaltyBreakFirstLessLess:  120
# 在一个字符串字面量中引入换行的penalty
PenaltyBreakString:  1000
# 对于每个在行字符数限制之外的字符的penalty
PenaltyExcessCharacter:  1000000
# 将函数的返回类型放到它自己的行的penalty
PenaltyReturnTypeOnItsOwnLine:  60
# 指针和引用的对齐: Left, Right, Middle
PointerAlignment:  Left
# 允许重新排版注释
ReflowComments:  true
# 允许排序#include
SortIncludes:  true
# 在C风格类型转换后添加空格
SpaceAfterCStyleCast:  false
# 在赋值运算符之前添加空格
SpaceBeforeAssignmentOperators:  true
# 开圆括号之前添加一个空格: Never, ControlStatements, Always
SpaceBeforeParens:  ControlStatements
# 在空的圆括号中添加空格
SpaceInEmptyParentheses:  false
# 在尾随的评论前添加的空格数(只适用于//)
SpacesBeforeTrailingComments:  2
# 在尖括号的<后和>前添加空格
SpacesInAngles:  true
# 在容器(ObjC和JavaScript的数组和字典等)字面量中添加空格
SpacesInContainerLiterals:  true
# 在C风格类型转换的括号中添加空格
SpacesInCStyleCastParentheses:  true
# 在圆括号的(后和)前添加空格
SpacesInParentheses:  true
# 在方括号的[后和]前添加空格，lamda表达式和未指明大小的数组的声明不受影响
SpacesInSquareBrackets:  true
# 标准: Cpp03, Cpp11, Auto
Standard:  Cpp11
# tab宽度
TabWidth:  4
# 使用tab字符: Never, ForIndentation, ForContinuationAndIndentation, Always
UseTab:  Never
```

launch.json中配置：

```json
//此代码可以使vscode调试c++时在build目录下完成
"cwd": "${workspaceFolder}/build",
```

VA(番茄助手快捷键使用)

ALT + +/-(进入/回退到上一步)

ALT + E(提取代码为函数)

ALT + C(快速实现代码)

ALT + G(跳转到目标)

SHIFT + ALT + I(导入头文件)

CTRL + K + D(快速格式化代码/对齐)(visual studio自带)

CTRL + TAB(切换活动文件)

CTRL + F6(更改函数签名)

SHIFT + F6(重命名标识符)

CTRL + SHIFT + ENTER(快速插入下一行)

ALT + ↑/↓(上下移动代码)



### 1.2.搭建图形化开发环境-easyX入门
前期使用easyX即可

```cpp
#include <graphics.h>
#include <Windows.h>

int main(){
	//初始化图形界面
	initgraph(640, 480);

	//     x     y   半径(像素)
	circle(320, 240, 200);//构建图形 - 画圆

	//程序暂停
	system("pause");

	//关闭图形界面
	closegraph();

	return 0;
}
```

如果代码文件中有部分代码不希望采用.clang-format进行格式化，可以在这部分代码块的前后使用如下的注释进行标识：

```cpp

/* clang-format off */
// 不需要.clang-format的代码块
int a = 42;
a++;
...
    /* clang-format on */
```

#### 1.2.1.任务实施![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1722740419170-18206292-6c89-42c5-81d0-411ac1430175.png)
```cpp
#include <graphics.h>
#include <conio.h>

int main(){
	initgraph(640, 480);//初始化图形界面窗口

	setbkcolor(RGB(64, 128, 128));//设置窗口背景颜色
	cleardevice();//窗口上色

	//setlinecolor(RED);//设置画笔颜色
	setlinecolor(RGB(255, 0, 0));//设置画笔颜色

	//           实线      宽度
	setlinestyle(PS_SOLID, 10);//设置画笔样式

	circle(320, 240, 200);//通过以上样式进行画圆

	//settextcolor(YELLOW);//设置字体颜色
	settextcolor(RGB(255, 255, 0));//设置字体颜色

	//        高度 宽度0为自适应 字体名称
	settextstyle(100, 0, "微软雅黑");//设置字体样式
	outtextxy(170, 190, "禁止通行");//指定位置打印文本

	//从坐标(180,380)到(460,100)之间画线
	line(180, 380, 460, 100);//画线

	_getch();//暂停程序 - 按任意键继续

	closegraph();//关闭窗口
	return 0;
}
```

导入外部库：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723455135238-4b7ceb83-ad25-4e1c-b824-66c1ee8d5c84.png)

由于链接库是发布模式(release)生成的，所以导入到本项目后，本项目也需设置为发布模式运行

#### 1.2.2.疑难杂症
受早期微软vc编译器的影响，一些函数内的字符串参数可能由于编码问题导致识别为实际参数不符，可使用以下方式解决：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1722826358245-4f07391e-cd61-476b-9f1c-c6681ff06f75.png)

### 1.3.github与本地环境![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723117777540-e5c1f73d-85d5-41b7-80dd-1961b39ac5cd.png)
首先在github上创建一个仓库，然后在本地新建一个目录然后右键打开gitGUI输入git init来创建本地仓库：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723122705111-9c83d0fe-0fb5-4597-8ed8-45b2c9e96f74.png)

之后此目录下的所有文件会根据用户的相关git命令来报错到.git下的仓库区：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723122906550-7ab0bc05-66ce-40de-a6fb-6bea41c5d233.png)

除.git目录，当前目录下为工作区

除此之外，也可以使用git init命令来自定义.git存放的位置：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123082442-1ad80a1c-381d-4aab-aa27-b05e795b0ae4.png)

这样当前目录下的结构就成这样：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123125438-c501f25f-7c9c-491c-b6cc-c0246e0ef93c.png)

.git本地仓库目录就在此文件夹下了，进入这个目录，进行后续操作

此时有一个文件，对当前目录右键打开gitbash：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123312311-789beac9-318b-48a5-8d52-d0e0f2977ee6.png)

输入以下命令可以查看此文件的状态：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123374262-e473a60b-e4ae-42c8-997d-91bf8fe8a7f2.png)

由此可知，未提交到主分支本地仓库的文件都会标有问号(此文件不被本地仓库所管理)。可使用git add 文件名来跟踪此文件

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123538763-d4141604-b0c4-47d9-a3df-2ec9228db405.png)

此时这个文件的问号图标发生改变

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123558103-dbcb56eb-0d4a-4ef1-bf26-aa9b54f6c444.png)

再次使用git status查看：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123652556-0b1d70ee-51ae-4899-8d2d-12c97f364fff.png)

此时会该文件被显示为将要被提交的文件

现在试着提交此文件到本地仓库：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123830237-5e728121-c7a6-484e-a17a-a826cc182007.png)

-m为添加日志消息，fbd130d(前七位)为本次提交的版本号，此命令会把上述将要被提交的文件进行提交到本地仓库

此时再修改test.txt里的内容：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723123988040-284a3837-3242-458f-b816-7ac5d3993e43.png)

此时该文件的图标又发生改变：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124018152-c1d64737-41f9-42b3-bfb6-1d9a1bc9836f.png)

此时再次查看状态：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124076539-4b02b031-0e07-460a-8726-48174cca574a.png)

会发现该文件被标记为修改过但没有被添加到缓存区

可以使用git add . 来把所有修改过的文件添加到缓存区：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124217837-20f7c862-ae8d-4fbe-af9f-0ad8f96133c9.png)

再次查看状态：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124262766-1ae0f9e9-dfe6-4f87-a590-d90bf81c0951.png)

会发生git已经跟踪(添加到缓冲区)此被修改过的文件了

此时再提交：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124433882-9fc73acb-1bb9-4de7-9dbd-e27bf8046694.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124471485-9ab029f4-bd2e-4d76-b494-bb6dff037d01.png)

版本号发生了变化，文件图标也发生了变化

可以使用git log查看文件的日志：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124556187-53a7120e-7c59-4c32-874e-e4ae1fef8b59.png)

命令总结：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124688855-be524c3a-da25-44a9-b4c4-e9ba6747aa6c.png)

以上使用git命令比节繁琐，此时使用小乌龟来复现上述操作：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124858953-84249244-0d14-4e53-a6ac-36ac491e10d0.png)

右键目录，点击此选项

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124955439-d48f6328-b33b-497d-8f50-0c1a29298df4.png)

然后继续创建此文件，然后直接右键选择：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723124996457-5a7e5e3b-3651-4972-8d2a-34c29a7495d6.png)

即可完成git推送文件到本地仓库的所有命令：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125093845-48e811d7-f694-4172-b22c-97eb166f1a8d.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125162213-2a1e3e88-7b05-403d-90e7-270d08d9978e.png)

此时文件的状态直接就是对勾了：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125189722-afc0cb10-5735-435a-9139-7b7978d8b391.png)

此时再修改此文件：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125225660-fc2aa3e6-607a-4d8b-8612-feeb1629d3a8.png)

此时文件图标发生改变：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125242061-f101a5f3-0af2-440a-a923-2978295de0a5.png)

此时再右键选择提交：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125265977-99199c7c-d5cd-4a28-a22a-305c310292cf.png)

此时会自动识别出文件被修改：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125341393-af078e46-327b-4614-b23a-7720c3a5318b.png)

继续提交即可：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125380811-0ced89bb-03a3-479d-9662-99e759c4f32d.png)

点此可查看版本日志：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125444998-1900c095-5a21-40df-899f-472c358901dc.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723125473482-fe6810ca-0270-40ae-bdcc-aafa3262babd.png)

远程仓库的克隆：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723126626775-dea22643-5158-4e73-9311-3522a0932cf1.png)

输入链接，其余保持默认即可：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723126652634-598fa294-a49f-4233-b2d7-be6de8cacb3b.png)

使用ssh的方式克隆，首先需使用gitgui生成密钥：  
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723126855507-9874fb50-5ea8-4317-987e-b93067c25296.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723126906326-aa1fe7e6-edd0-42d4-804c-2b5fb1ea67d8.png)

点击generate key生成密钥

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723126952079-e669c465-5317-49a4-ae44-af66e96c42e5.png)

这里提示输入密码对密钥进行二次加密，可以不用设置即可生成

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723127010254-71337e02-61e1-4419-a4fe-a2ce79cd1d7e.png)

生成好的密钥会放在your key is in所指定的目录

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723127090163-23d266d7-aadd-420d-891a-a2a62ae97771.png)

如果后续需要重新生成密钥，需把上述两个文件删除再用gitgui重新生成即可

之后就可在github或gitee里的个人中心里配置rsa公钥了，此时就可通过ssh链接获取仓库

克隆总结：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723127654555-692dd4e9-639a-4d3e-bd0b-9e34b64373ec.png)

推送到github或gitee：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723128042595-09323679-d15b-4ed5-9436-c8ab1755a7e4.png)

点击推送即可



分支部分

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723168329985-040c5fa5-bff1-4556-ba44-4b0835061da1.png)

右键目录选择小乌龟即可看到创建分支，创建完毕选择切换分支：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723168518154-1f472bbe-8cfa-418e-8a0f-f06592912769.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723168606239-ac53669e-2b0e-4559-9c24-b4c74201a868.png)

现在可以查看现有的分支图：  
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723168698437-81082192-d2be-4f89-95d0-68fea02a2d79.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723168728544-74ce906a-8e3c-47c3-9ff3-583de85f0dee.png)

也可以直接在分支图里切换分支：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723168845490-4a9627c2-fa58-4af3-b7e7-67941c599a27.png)

以dev为当前分支的清空下在当前工作区开发文件：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723168953415-96aa82f2-33a1-4057-bc55-7148e50fa87b.png)

然后提交到此分支

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723169001641-0c5a4a02-cdb4-42d1-8bef-3fa951568fd9.png)

此时流程图发生了改变：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723169067931-309b5c42-9add-4ccb-9aa1-df188184ebdc.png)

此时将分支切换到main：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723169279188-85c791f2-8b32-4beb-b4dd-308fd212450f.png)

我们发现目录下属于dev分支的文件消失了，可以发现当前是main的分支

此时修改main分支下的文件：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723169357195-8fbd412c-febd-4941-82f0-28a962036f82.png)

然后提交到main之后刷新分支图：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723169442326-e0c1a09c-5fe3-4893-a4f8-73ee8ec210e7.png)

再次查看日志，查看所以分支：![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723169670559-dad8b50d-8dc4-4b0c-b257-9235bf8aa87f.png)

可以发现两个分支都朝着各自的方向发展

但是实际的项目开发中，子分支最终会被合并到主分支中的

再次切换到dev分支上做最后的修改：  
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723169916200-6709e80a-db09-4e4d-ac7f-e668a0483e07.png)

然后提交

最终合并到主分支，首先切换到主分支：  
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170046160-cd940e82-29c7-40f4-a34a-911582a1fa72.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170229531-9568c38e-e619-48d5-82c7-9d55ea630f24.png)

选择从dev分支合并过来

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170270992-75f5a854-61a4-43bd-859e-0d262b14b485.png)

最终属于dev上的文件合并到main上了：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170335939-3d7e646e-caff-4a03-b32c-e55d90f925b8.png)

查看版本分支图及分支流程图的变化：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170465371-9a6a44ee-26d6-492c-8bb0-b66ba3f238de.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170488474-13fee79d-bbce-46f8-8789-e818b85e9330.png)



冲突部分

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170598657-6fcce1a3-9287-4bb6-b7be-e86b211ac8e3.png)

把上个实验中的dev分支删除再重新创建：  
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170714177-489535d8-04d2-4130-a948-e51aafd95dc3.png)

修改main分支里的：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170780988-5c5f0b89-6b1a-41f9-b0bb-45d7fa351f1c.png)

然后提交到main分支，之后切换到dev分支：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170869180-bd73c8b3-c4b0-4c87-b00d-208dac6ae452.png)

dev分支里的test.txt并没有main分支里新增的第二行：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723170932527-50095846-1836-403f-80e3-69f0b90ad8fb.png)

但是此时在第二行新增dev分支的自己的内容“ddd”

之后提交：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723171066068-529f20e2-4b12-4fdf-be17-7c538809b09a.png)

然后切换到主分支上再将dev合并到main分支：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723171188483-9a664803-5a65-4d5a-962f-24b7e3fc54ae.png)

就会触发冲突，此时目录下该文件出现了感叹号：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723171254319-1d82f391-c9ec-4710-94b2-6778358612ef.png)

打开查看：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723171275900-b2947128-00f9-4507-bf99-066ab4d5986c.png)

发现有两个分支对此行同时修改发生了冲突

将里面内容修改一起后再次提交：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723171397606-461c464b-d5ac-4f4a-868d-42b4da5045a5.png)

此时该文件仍然被标记为冲突文件，点解决即可，再次提交，之后查看日志

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723171521692-042e9768-f9cc-44e5-92f5-4b0f70617422.png)

也算完成了合并

解决冲突也可使用小乌龟自带的解决工具：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723171778827-5c63875b-7275-4a6e-b5bf-b24cfbbfed05.png)





忽略

制作忽略文件

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723172278047-47bf8f47-a357-4a30-bf5a-0f138f5eb353.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723172288707-d37c17c9-f52c-491e-88cd-1cd8670c10a3.png)

此时当前工作区下的mp3文件都被忽略了

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723172428634-a8da8931-9201-449a-996b-1b256eda70e1.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723172568896-e4809f8c-b2b7-4888-85c6-628c90f0f16b.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723172601283-241d7166-17ef-4c4a-a972-828a71c0165d.png)

这样a文件下的tmp就不会被忽略

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723173143263-54615321-5bc1-430a-bef7-438110f43de8.png)

此方式也称反忽略



版本回退

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723173686774-1ad836bf-b1db-457e-b539-0e4135969955.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723174559508-59ebaad3-6657-43cb-8982-fdc939d83c36.png)

head指针指向当前分支的首节点

打开分支日志：  
 ![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723173594312-7be5297e-2937-4bf9-930b-e2e88b9b962d.png)

即可回退到各个节点的状态

创建一个文件并提交：



![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723173911761-1ab491a0-97ff-4e06-b147-9796ffd4e402.png)

再次修改为2保存并提交：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723173988352-6f41fa50-8817-4453-bf35-96180785511a.png)

如果此时想回退到当时为1的内容时，就可选择版本回退

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723174067731-722ee24e-4dd7-478b-970f-eb29ecb30f16.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723174214590-6d403bbc-a958-40ec-a63c-de1cde01783e.png)

sha-1为当次提交版本号，直接选硬重置即可

如果重置后会后悔，那么尽量再回退前在当前节点设置标签，将来后悔，可通过此标签还原回来



子模块

如果需要应用其它git仓库为本仓库的子项目(扩展)，可以使用小乌龟添加子模块，然后路径设置时，需选择当前目录下没有创建的目录，添加子模块时，会自动创建此目录

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723174845229-5234a7e5-ca67-47b5-8a5b-96415c069f53.png)

如果其它计算机需要克隆这个带有子模块的仓库时，需勾选上递归

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723175130043-72ef6aa2-f77f-41d5-be8a-fd7ab7781fb3.png)


## 二、C/C++基础部分
### 2.1.浮点数
cout函数可使用以下函数来调整控制台输出浮点数的总精度：

```cpp
    double PI = 3.1415926;
	//cout << PI << endl;//默认输出6位的精度

	cout.precision(8);//使用此函数可调整cout函数的输出精度
	cout << PI << endl;
```

而以下方式可调整小数部分的位数及恢复设定：

```cpp
    cout.precision(7);//使用此函数可调整cout函数的总输出精度

	cout.flags(cout.fixed);//调整浮点数小数部分的位数为上述precision()函数设置的
	cout << PI << endl;//最终会输出7位小数

	cout.unsetf(cout.fixed);//恢复小数部分的设定为默认
```

### 2.2.cin
对于cin函数来说：

```cpp
#include <iostream>
#include <Windows.h>

using namespace std;

int main() {

	int a, b, c;
	cout << "请依此输入三个数：";
	cin >> a >> b >> c;
	cout << "a=" << a << " b=" << b << " c=" << c << endl;
	return 0;
}
```

如果用户输入的数类型不符：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1722854209992-3f366562-929e-422e-8427-96d611a56f12.png)

导致后续的输入无效

所以可以使用以下方式解决：

```cpp
#include <iostream>
#include <Windows.h>

using namespace std;

int main() {

	int a, b, c;
	cout << "请依此输入三个数：";

	cin >> a;
	if (cin.fail())
	{
		cout << "你输入的类型有误！" << endl;
		cin.clear();//清除cin错误标志
		cin.sync();//清空输入缓冲区
	}
	cin >> b;
	if (cin.fail())
	{
		cout << "你输入的类型有误！" << endl;
		cin.clear();//清除cin错误标志
		cin.sync();//清空输入缓冲区
	}
	cin >> c;
	if (cin.fail())
	{
		cout << "你输入的类型有误！" << endl;
		cin.clear();//清除cin错误标志
		cin.sync();//清空输入缓冲区
	}
	cout << "a=" << a << " b=" << b << " c=" << c << endl;
	system("pause");
	return 0;
}
```

在c语言中，清除输入缓冲区也可用以下方法：

```cpp
char name[10];
	printf("你好，家在何地？");
	scanf("%s", name);
	printf("好的，住在%s是吧！", name);
	fflush(stdin);//清除输入缓冲区
```

读取一行可以用以下方法：

```cpp
    string str;
	cout << "请输入你想说的话：";
	getline(cin, str);//直到读到换行符截止
```

### 2.3.string类型
string类型的用法：

```cpp
#include <iostream>
#include <Windows.h>
#include <string>

using namespace std;

int main() {
	string str;//默认值为""
	string str2("str");//对象的方式赋值(初始化)
    string str3{"hello world"};//c++11新特性赋值
    
    //第一个参数为个数，第二个参数为字符：会输出6个s
    string str4(6, 's');
	cout << str << "," << str2 << endl;
	system("pause");
	return 0;
}
```

string类型的输入输出：

```cpp
string str;
cout << "请输入你想说的话：";
cin >> str;//用户输入空白字符(制表符、空字符等)会中断并停止接收
cout << str << endl;
```

string类型常用函数-getline()&empty()：

```cpp
    string str;
	cout << "请输入你想说的话：";
    //getline()函数执行完后会继续返回一个cin
	getline(cin, str);

	if (str.empty()){
		cout << "字符串为空！"<< endl;
	}
```

string类型常用函数-size()&length()：

```cpp
    string str;
	cout << "请输入你想说的话：";
	getline(cin, str);
	cout << "字符串长度为：" << str.length() << "," << str.size() << endl;
```

string类型变量的比较：

```cpp
    string str = "abc";
	string str2;
	cout << "请输入：";
	getline(cin, str2);
	if (str == str2){
		cout << str << "与" << str2 << "相等" << endl;
	}else{
		cout << str << "与" << str2 << "不相等" << endl;
	}
```

或使用compare()函数：

```cpp
    string str = "abc";
	string str2;
	cout << "请输入：";
	getline(cin, str2);
	if (!str.compare(str2)){
		cout << str << "与" << str2 << "相等" << endl;
	}else{
		cout << str << "与" << str2 << "不相等" << endl;
	}
```

string类型可通过c_str()函数来转换为c语言char类型字符串

### 2.4.bool类型
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723038599494-318cbff4-10c4-476d-89b3-2806184471f9.png)

### 2.5.位运算符
位与(&)、位或(|)、位非(~)、异或(^)

位非运算(按位取反)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723080591722-6221d0be-0766-4b1d-981a-a6f5f2f28a01.png)

异或运算

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723080817383-aab4b398-f103-4253-8d1c-890e8ed2c93b.png)

移位运算(只适用于整数型)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723081157719-a6bf18e4-dd12-4337-ba7a-a28eaf98503e.png)

如果是右移则为除(x除以2的n次方)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723081294808-b72b6bfe-d859-4cf3-9aab-6bd08e562c57.png)


 位运算各种习题：

```cpp
    int x;
	int tmp;
	cout << "请输入一个整数：";
	cin >> x;
	tmp = x;
	//先将该数的后四位二进制置零
	x = x & (~15);
	//再将该数后四位置0110(6)
	x = x | 6;
	cout << tmp << "后四位置0110(6)的结果为：" << x << endl;
```

```cpp
bool isJiShu(int n) {
	return n & 1;
}
printf("Enter a number：");
int a = 0;
scanf("%d", &a);
bool flag = isJiShu(a);
if (flag) {
    printf("%d是奇数", a);
}
else{
    printf("%d不是奇数" ,a);
}
```

```cpp
bool isPowerOf2(int n) {
	return (n & n - 1) == 0;
}
printf("Enter a number：");
int a = 0;
scanf("%d", &a);
bool flag = isPowerOf2(a);
if (flag) {
    printf("%d是2的幂", a);
}
else {
    printf("%d不是2的幂", a);
}
```

```cpp
int getLastSetBit(int n) {
	return n & (-n);
}
printf("Enter a number：");
int a = 0;
scanf("%d", &a);
int n = getLastSetBit(a);
printf("%d是%d的最低有效位", n, a);
```

```cpp
void swap(int* a, int* b) {
	*a = *a ^ *b; // a1 = a0 ^ b0, b1 = b0
	*b = *a ^ *b; // a2 = a1 = a0 ^ b0, b2 = a1 ^ b1 = a0 ^ b0 ^ b0 = a0
	*a = *a ^ *b; // a3 = a2 ^ b2 = a0 ^ b0 ^ a0 = b0, b3 = b2 = a0
}
printf("Enter two numbers：");
int a = 0;
int b = 0;
scanf("%d%d", &a, &b);
// 在实际开发中不建议使用此函数的算法，此算法只是用来学习与理解位运算。
swap(&a, &b);
printf("交换之后的结果：a=%d,b=%d", a, b);
```

```cpp
int findSingleNum(int numbers[], int length) {
	// {1, 2, 2, 3, 3}
	int result = 0;
	// 利用位运算中异或的：(a ^ b) ^ c = a ^ (b ^ c) 、
    // a ^ 0 = a 以及 a ^ a = 0的性质。
	for (int i = 0; i < length; i++){
		result ^= numbers[i];
	}
	return result;
}
int numbers[] = {1, 2, 2, 3, 3};
int result = findSingleNum(numbers, 5);
printf("singleNum is %d", result);
```

```cpp
void findTwoSingleNums(int numbers[], int length, int* a, int* b) {
	// (1, 2, 2, 3, 6, 6)
	// 利用位运算中异或的性质以及最低有效位为1
	int aAndbOfXor = 0;
	for (int i = 0; i < length; i++){
		aAndbOfXor ^= numbers[i];
	}
	// 求出a和b异或结果的最低有效位，以此进一步从数组中过滤出a和b
	int lastSetBit = aAndbOfXor & (-aAndbOfXor);
	printf("最低有效位：%d\r\n", lastSetBit);
	for (int i = 0; i < length; i++){
		if (numbers[i] & lastSetBit) {
			*a ^= numbers[i];
		}else{
			*b ^= numbers[i];
		}
	}
}
int numbers[] = {1, 2, 2, 3, 6, 6};
int a = 0, b = 0;
findTwoSingleNums(numbers, 6, &a, &b);
printf("singleNum are [%d, %d]", a, b);
```

### 2.6.逗号表达式
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723085072643-b606283d-5f8a-4895-995e-6ee179723f18.png)

逗号优先级低，所以第一个式子的结果取等号右边第一个式子

加上小括号以后，取最右边的式子为整体的值

### 2.7.运算符优先级
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723085993610-63c81239-2bf8-44ef-98dd-7f3cd2eb2c4c.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723085845892-9cf6c072-d09f-4cd7-8d15-8657cfc2f86b.png)

### 2.8.switch语句
在某些编译器下，以下代码会编译不通过：  


```cpp
switch (1){
	case 1:
		int a = 0;
		cout << "hello world" << endl;
		break;
	}
```

可以通过以下方式解决：

```cpp
switch (1)
	{
	case 1:

		{
			int a = 0;
			cout << "hello world" << endl;
		}
		break;
	}
```

### 2.9.循环练习题
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723274032875-27e71114-a276-46f8-afaf-1ceb9efd4703.png)

```cpp
#include <iostream>
#include <Windows.h>

using namespace std;

int main(){
	int bit;//个位
	int tenBit;//十位
	int hunBit;//百位
	for (int i = 100; i <= 999; i++){
		bit = i % 10;
		tenBit = (i % 100) / 10;
		hunBit = i / 100;
		if (i == ((bit * bit * bit) + (tenBit * tenBit * tenBit)
            + (hunBit * hunBit * hunBit))){
			cout << "水仙花数：" << i << endl;
		}
	}
	system("pause");
	return 0;
}
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723275690565-9af2f7b6-c413-4371-b88c-194384888b20.png)

### 2.10.函数
每个C/CPP文件需另外提供一个.h文件，以此对外提供接口，调用者不需关心函数内的具体实现，只需包含该头文件即可

如果头文件被多次包含会造成不可避免的问题，可使用下述方法：

```cpp
#pragma once
//计算两数相加
int sum(int a, int b);

//计算两数相减
int sub(int a, int b);
```

```cpp
#ifndef _TOOLS_H_
#define _TOOLS_H_

//计算两数相加
int sum(int a, int b);

//计算两数相减
int sub(int a, int b);
#endif
```

这样就保证该头文件被其他文件包含时最多只能包含一次

#### 2.10.1.数组当实参
```cpp
#include <iostream>
#include <Windows.h>

using namespace std;

void printScore(int scores[3]);
int main(){
	int scores[3] = {60, 70, 80};
	cout << "调用之前：" << sizeof(scores) << endl;

	//数组当实参传递给函数时会降为指向数组第一个元素位置的指针
	printScore(scores);
	system("pause");
	return 0;
}
void printScore(int scores[3]){
	cout << "调用之后：" << sizeof(scores) << endl;
}
```

#### 2.10.2.函数的栈空间
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723608027096-d315805e-1a67-405b-8354-481184c5f87b.png)

#### 2.10.3.内敛函数
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723621528736-df4240cb-68e6-4e2e-a36c-a8346bcb174f.png)

```cpp
#include <iostream>
#include <Windows.h>

using namespace std;

//定义内联函数
inline int sum(int a, int b);

int main(){
	cout << sum(1, 2) << endl;
	//cout << 1 + 2 << endl;//在编译时等同于此语句
	return 0;
}

inline int sum(int a, int b){
	return a + b;
}
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723622182847-ce1fb269-62b4-4979-be52-53d75a7cacb1.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723622233198-09f36f15-2e9a-454b-80c2-e653fe1f4d93.png)

#### 2.10.4.递归函数
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723624261007-6636487e-7c60-4353-96d0-8fc2cd8231cb.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723624801375-7f70030e-9e43-47f2-adc5-953ae1d3fa21.png)

```cpp
void test(int n);
int main(){
	test(1);
	system("pause");
	return 0;
}

void test(int n){
	if (n > 5){
		return;
	}
	cout << "进入第" << n << "层梦境" << endl;
	test(n + 1);
	cout << "苏醒第" << n << "层梦境" << endl;
}
```

对应栈空间图：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723626695024-d4a67d6b-a0fa-425f-aac7-3ea1b4f4c8a9.png)

斐波那契数列

1，1， 2，  3， 5， 8， 13， 21， ....

```cpp
#include <iostream>
#include <Windows.h>

using namespace std;

int fab(int n);//求斐波那契数列第n个数

int main(){
	int n;
	cout << "请输入要算出的第几个数：";
	cin >> n;
	cout << "第" << n << "个斐波那契数列的数为：" << fab(n) << endl;
	system("pause");
	return 0;
}

int fab(int n){
	//结束条件
	if (n == 1 | n == 2){
		return 1;
	}
	return fab(n - 1) + fab(n - 2);
}
```

递归函数的缺点：

性能很低！！！

<font style="color:rgb(255,0,0);">实际开发中, 极少使用!</font>

### 2.11.静态库
制作静态库文件：

在vs中创建win32项目：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723628177626-6cf72c74-8149-4e41-a3eb-206406a51fad.png)

点完成即可

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723629467853-40e6675e-2978-4724-980c-17f61ad5ccfe.png)

最终点生成解决方案即可生成对应的lib文件

注：每个库需提供一个头文件，供调用者一起与lib文件导入到项目中才可正常使用

### 2.12.二维数组
```cpp
#include <iostream>
#include <windows.h>

using namespace std;

int main(){
	
	int array[3][4];
	int i, j;
	for (i = 0; i < 12; i++){
		array[i / 4][i % 4] = i + 1;
	}

	for (i = 0; i < 3; i++){
		for (j = 0; j < 4; j++){
			cout << array[i][j] << '\t';
		}
		cout << endl;
	}
	return 0;
}
```

```cpp
#include <iostream>
#include <windows.h>

using namespace std;
int main(int argc, char* argv[])
{

	int arr1[3][];//不指定列编译不通过
	int arr2[][4];//不指定行编译也不会通过
	return 0;
}
//只有定义形式参数的时候才可省略行
void printArray(int array[][4]){

}
```

对费非预期的错误使用断言

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723793174884-2ca8a1d2-b51a-41cc-9279-a3ed00f073f0.png)

#### 2.12.1.项目练习
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723793351025-fb3e0129-34eb-4976-b6d6-6aaf59c7978f.png)

```cpp

```

### 2.13.指针
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1723795238295-4404fe10-a8b1-44ba-95ad-e7e0ceb7abae.png)

#### 2.13.1.空指针
暂时不用的指针建议设置为空指针，空指针默认值设置为0或NULL或nullptr

#### 2.13.2.坏指针
也叫野指针

#### 2.13.3.数组指针
```cpp
int array[4][3] = {	{176, 170, 166},
                    {164, 165, 156},
                    {163, 167, 168},
                    {160, 150, 171}
                  };
int (*array_p)[3] = NULL;//定义数组指针
for (int i = 0; i < 4 ; i++){
    array_p = &array[i];
    for (int j = 0; j < 3 ; j++){
        //cout << *(array_p)[j] << '\t';//数组下标访问法
        cout << *((*array_p) + j) << '\t';//指针访问法
    }
    cout << endl;
}
```



#### 2.13.14.指针数组
```cpp
int array[4][3] = {	{176, 170, 166},
								{164, 165, 156},
								{163, 167, 168},
								{160, 173, 171}
							};
	int *qishou[2];//定义指针数组
	if (array[0][0] > array[0][1]){
		qishou[0] = &array[0][0];
		qishou[1] = &array[0][1];
	}else{
		qishou[0] = &array[0][1];
		qishou[1] = &array[0][0];
	}
	for (int i = 2; i < 12 ; i++){
		if (*qishou[1] >= array[i / 3][i % 3]){
			continue;
		}
		if (*qishou[0] >= array[i / 3][i % 3]){
			*qishou[1] = array[i / 3][i % 3];
		}else if (*qishou[0] < array[i / 3][i % 3]){
			qishou[1] = qishou[0];
			qishou[0] = &array[i / 3][i % 3];
		}
	}
	cout << "首高：" << *qishou[0] << ", 次高：" << *qishou[1] << endl;
```

#### 2.13.15.指针与数组的区别
 数组：数组是用于储存多个相同类型数据的集合。 

指针：指针是一个变量，但是它和普通变量不一样，它存放的是其它变量在内存中的地址。  

1. 赋值 

数组：只能一个一个元素的赋值或拷贝

指针：指针变量可以相互赋值 

2. 表示范围 

数组有效范围就是其空间的范围，数组名使用下表引用元素，不能指向别的数组 指针可以指向任何地址，但是不能随意访问，必须依附在变量有效范围之内 

3. sizeof

数组： 数组所占存储空间的内存：sizeof（数组名） 

数组的大小：sizeof（数组名）/sizeof（数据类型） 

指针： 在32位平台下，无论指针的类型是什么，sizeof（指针名）都是4. 在64位平台下，无论指针的类型是什么，sizeof（指针名）都是8  

4. 指针数组和数组指针 

针指数组: int *qishou[2];//定义一个有两个元素的指针数组，每个元素都是一个指针变量 int girl1= 167; int girl2 = 171; qishou[0] = &girl1; qishou[1] = &girl2;

数组指针： int (*p)[3]; //定义一个指向三个成员的数组的指针

访问元素的两种方式：

int A[4][3]={

{173, 158, 166}, 

{168, 155, 171}, 

{163, 164, 165},

 {163, 164, 172}

};

p =&A[0]; 

数组法： (*p)[j] 

指针法： *((*p)+j)  

5.传参

数组传参时，会退化为指针! 

（1）退化的意义：C语言只会以值拷贝的方式传递参数，参数传递时，如果只拷贝整个数 组，效率会大大降低，并且在参数位于栈上，太大的数组拷贝将会导致栈溢出。 		（2）因此，C语言将数组的传参进行了退化。将整个数组拷贝一份传入函数时，将数组名 看做常量指针，传数组首元素的地址。  

```cpp
/*------------------<一维数组传参>-----------------------*/
/*方式一:形参不指定数组大小
用数组的形式传递参数，不需要指定参数的大小，
因为在一维数组传参时，形参不会真实的创建数组，
传的只是数组首元素的地址。
*/
void method_1(int arr[], int len){
    for(int i=0; i < len; i++){
        printf("arr[%d]=%d\n", i, arr[i]);
    }
}
 //方式二：指定数组大小
void method_2(int arr[10]){
     for(inti=0;i<10;i++){
         printf("arr[%d]=%d\n", i, arr[i]);
     }
 }
 //方式三:一维数组传参退化，用指针进行接收，传的是数组首元素的地址
void method_3(int *arr,int len)
 {
 for(int i = 0; i<len; i++){
         printf("arr[%d]=%d\n", i, arr[i]);
     }
 }

int main(){
    intarr[10]={0,1,2,3,4,5,6,7,8,9};
    method_1(arr,10);
    printf("------华丽的分隔线------\n");
    method_2(arr);
    printf("------华丽的分隔线------\n");
    method_3(arr,10);
    system("pause");
    return0;
}

```

```cpp
/*--------------------<指针数组传参>-----------------------*/
//方式一:指针数组传参，声明成指针数组，不指定数组大小
void method_4(int *arr[], int len){
    for(int i = 0; i < len; i++){
        printf("arr[%d]=%d\n", i, *arr[i]);
    }
}
//方式二:指针数组传参，声明成指针数组，指定数组大小
void method_5(int *arr[10]){
    for(int i = 0; i < 10; i++){
        printf("arr[%d]=%d\n", i, *arr[i]);
    }
}
//方式三:二维指针传参
//传过去是指针数组的数组名，代表首元素地址，而数组的首元素又是一个指针，
//就表示二级指针，用二级指针接收
void method_6(int **arr, int len){
    for(int i = 0; i < len; i++){
        printf("arr[%d]=%d\n", i, *(*(arr+i)));
    }
}
int main(){
    int arr[10] = {0,1,2,3,4,5,6,7,8,9};
    int *arr_p[10] = {0};
    for(int i = 0; i < 10; i++){
        arr_p[i] = &arr[i];
    }
    method_4(arr_p, 10);
    printf("------华丽的分隔线------\n");
    method_5(arr_p);
    printf("------华丽的分隔线------\n");
    method_6(arr_p, 10);
    system("pause");
    return0;
}
```

#### 2.13.16.void类型指针
 void =>空类型

 void* =>空类型指针，只存储地址的值，丢失类型，无法访问，要访问其值，我们必须对这个指 针做出正确的类型转换，然后再间接引用指针。 所有其它类型的指针都可以隐式自动转换成void类型指针，反之需要强制转换  

#### 2.13.17.函数指针与qsort的使用
```cpp
int compare_int( const void *a, const void *b );

int main() {
   system( "chcp 65001" );
   int a = 10;
   int b = 20;
   int ( *compare_int_p )( const void *, const void * );  // 定义函数指针
   compare_int_p = &compare_int;                          // 将函数地址赋值给函数指针

   // reinterpret_cast<uintptr_t>(ptr) 
   //将指针 ptr 转换为 uintptr_t 类型，以确保在不同平台上都能正确处理指针的位数。
   cout << "compare_int's address is 0x" << setfill( '0' ) << setw( 8 ) << hex << reinterpret_cast< uintptr_t >( compare_int_p ) << endl;
   int result  = ( *compare_int_p )( &a, &b );  // 第一种调用方式
   int result2 = compare_int_p( &a, &b );       // 第二种调用方式
   cout << "result：" << result << endl;
   cout << "result2：" << result2 << endl;
   system( "pause" );
   
   return 0;
}

int compare_int( const void *a, const void *b ) {
   int *al = ( int * )a;
   int *bl = ( int * )b;
   return *al - *bl;
}
```

```cpp
#include <iostream>
#include <stdlib.h>
#define SIZE( ARR ) ( sizeof( ARR ) / sizeof( ARR[ 0 ] ) )
using namespace std;
int compare_int( const void *a, const void *b );
int main() {
   system( "chcp 65001" );
   int array[]{ 32, 56, 10, 99, 29, 66 };
   qsort( array, SIZE( array ), sizeof( int ), &compare_int );
   for ( int i = 0; i < SIZE( array ); i++ ) {
      cout << array[ i ] << '\t';
   }
   cout << endl;
   system( "pause" );

   return 0;
}
//此处函数形参定义为void*类型，既是为qsort里的算法配合
//以至于能排序任何类型的数组
int compare_int( const void *a, const void *b ) {
   int *al = ( int * )a;
   int *bl = ( int * )b;
   return *bl - *al;
}
```

### 2.14.引用
 变量名回顾

变量名实质上是一段连续存储空间的别名，是一个标号(门牌号) 程序中通过变量来申请并命名内存空间 通过变量的名字可以使用存储空间 

问题1：对一段连续的内存空间只能取一个别名吗？  

#### 2.14.1.引用的概念
a)在C++中新增加了引用的概念 

b)引用可以看作一个已定义变量的别名 

c)引用的语法：Type&name=var; 

d）引用做函数参数那？（引用作为函数参数声明时不进行初始化）  

#### 2.14.2.引用做函数参数
 普通引用在声明时必须用其它的变量进行初始化， 引用作为函数参数声明时不进行初始化  

```cpp
void swap( int &a, int &b );

int main() {
   int a = 10, b = 200;
   swap( a, b );
   cout << a << "," << b << endl;
   system( "pause" );
   return 0;
}
void swap( int &a, int &b ) {
   int temp = a;
   a = b;
   b = temp;
}
```

#### 2.14.3.引用的意义
 1）引用作为其它变量的别名而存在，因此在一些场合可以代替指针

 2）引用相对于指针来说具有更好的可读性和实用性  

```cpp
int main(){
    int a = 10;
    int &z = a;
    int b = 20;
    int c = 30;
    printf("a: %d b: %d c: %d\r\n", &a, &b, &c);
    system("pause");
    return 0;
}
```

运行结果：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1726666116337-0358ad2e-e145-42f6-a3c8-cb096c71bbc4.png)

综上： 引用可以定义多个，引用有自己的空间 引用别名很像常指针  

也就是说，引用底层就是常量指针

#### 2.14.4.引用结论
 1)当实参传给形参引用的时候，只不过是c++编译器帮我们程序员手工取了一个实参地址，传给了形参引用（常量指针）

 2）当我们使用引用语法的时，我们不去关心编译器引用是怎么做的 当我们分析奇怪的语法现象的时，我们才去考虑c++编译器是怎么做的 

#### 2.14.5.指针引用
```cpp
void boy_home2( int *&meiPo );

int main() {
   int *meiPo = nullptr;
   boy_home2(meiPo);
   cout << *meiPo << endl;
   system( "pause" );
   return 0;
}
void boy_home2( int *&meiPo ) {
   static int boy = 20;
   meiPo = &boy;
}
```

#### 2.14.6.常量引用
```cpp
int main(){
   int a = 6;
   const int &b = a;
   int c = 9;
   // b = c;常量引用不能变更引用的值
   return 0;
}
```

### 2.15.C++程序内存分布
#### 2.15.1.C++内存分区
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1727251020062-d225dd58-6020-4fcc-b774-b7aae26799a8.png)

 1、栈区（stack）：由编译器自动分配释放，存放函数的参数值，局部变量值等。

 2、堆区（heap）：一般由程序员分配释放，随叫随到，挥之即走。

 3、全局/静态区（static）：全局变量和静态变量的存储是放在一起的，在程序编译时分配。   4、文字常量区：存放常量字符串。

 5、程序代码区：存放函数体（包括类的成员函数、全局函数）的二进制代码  

```cpp
int globalVariables = 6;

// 1.栈区
int main() {
   system( "chcp 65001" );
   static int staticValue = 9;

   // 4.字符常量
   // 注：像"abc"这种属于字面量常量
   const char *STR1 = "字符串常量";
   const char *STR2 = "字符串常量";
   // 地址相同，表示都指向常量区中同一块内存
   printf( "STR1:0x%p STR2:0x%p\r\n", STR1, STR2 );

   // 3.全局变量/静态变量
   cout << "全局/静态变量：" << globalVariables << "/" << staticValue << endl;
   int num = 0;
   int *mem = nullptr;

   // 2.堆区(动态内存分配)
   mem = new int( 6 );  // 在堆区开辟单个变量并赋初值
   cout << "mem's single value is " << *mem << endl;
   cout << "请输入要分配的数量：";
   cin >> num;
   mem = new int[ num ];  // 会在堆区开辟空间
   for ( int i = 0; i < num; i++ ) {
      *( mem + i ) = i + 1;
   }
   for ( int i = 0; i < num; i++ ) {
      cout << mem[ i ] << '\t';
   }
   cout << endl;
   // 如果不及时释放堆区内存，会造成内存泄漏，最终耗尽堆区内存，操作系统会崩溃。
   delete mem;    // 释放堆区单个变量
   delete[] mem;  // 释放堆区内存
   system( "pause" );
   return 0;
}
```

#### 2.15.2.动态内存分配
为什么要使用动态内存分配？

1）按需分配，根据需要分配内存，不浪费

c++仍然保留c语言的malloc()和free()与new delete对应

 注意：<font style="color:#DF2A3F;">new和delete是运算符，不是函数，因此执行效率高。</font>

<font style="color:#DF2A3F;">基础类型的指针可以new free malloc delete混用，但对象类型不行</font>

new运算符的例子：

newint; //开辟一个存放整数的存储空间，返回一个指向该存储空间的地址(即指针) newint(10); //开辟一个存放整数的空间，并指定该整数的初值为10，返回一个指向该存 储空间的地址 

newchar[100]; //开辟一个存放字符数组(包括100个元素)的空间，返回首元素的地址 newint[5][4]; //开辟一个存放二维整型数组(大小为5*4)的空间，返回首元素的地址 float*p=newfloat(3.14159); //开辟一个存放单精度数的空间，并指定该实数的 初值为//3.14159，将返回的该空间的地址赋给指针变量p  

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1727340148491-80790d03-e6c7-4575-9524-241cbd70f1f9.png)

内存拷贝函数：void*memcpy(void*dest,const void*src,size_t n);  #include <string.h>

```cpp
#define SIZE( ARR ) ( sizeof( ARR ) / sizeof( ARR[ 0 ] ) )

using namespace std;

int main() {
   system( "chcp 65001" );
   int before[] = { 18, 19, 20, 17, 22, 66, 99 };
   int num = 0;
   int *now = nullptr;
   cout << "请输入要分配的数量：";
   cin >> num;
   now = new int[ num ];
   for ( int i = 0; i < num; i++ ) {
      *( now + i ) = i + 1;
   }
   // 注：size_t n参数为多少个字节，并不是个数
   memcpy( now, before, sizeof( before ) );  // 将数组拷贝到另外一个数组中
   // 其余空间赋值18
   for ( int i = SIZE( before ); i < num; i++ ) {
      *( now + i ) = 18;
   }
   cout << "src:";
   for ( int i = 0; i < SIZE( before ); i++ ) {
      cout << before[ i ] << '\t';
   }
   cout << endl;
   cout << "dest:";
   for ( int i = 0; i < num; i++ ) {
      cout << now[ i ] << '\t';
   }
   cout << endl;
   delete[] now;  // 释放堆区内存
   system( "pause" );
   return 0;
}
```

对于内存结构图：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1727274748357-896b05c1-e807-4582-94f0-723032b12312.png)

可突破栈内存限制

```cpp
void testStack();

int main() {
   system( "chcp 65001" );
   testStack();
   system( "pause" );
   return 0;
}
void testStack() {
   cout << "start---" << endl;
   // windows默认分配给栈区内存为1~2m
   // int array[ 102400 * 2 ];  // 100k * 2 * 4 = 800kb
   int array[ 102400 * 6 ];  // 100k * 2 * 4 = 2.4m
   cout << "end---" << endl;
}
```

```cpp
void testHeap();

int main() {
   system( "chcp 65001" );
   testHeap();
   system( "pause" );
   return 0;
}
void testHeap() {
   int *ptr = nullptr;
   cout << "start test heap..." << endl;
   // windows默认分配给堆区内存为1~2g
   ptr = ( int * )malloc( ( int )2 * 1024 * 1000 * 1000 );  // 2G
   *ptr = 6;
   cout << "end test heap..." << endl;
}
```

所以当我们需要分配大内存时，可以选择在堆区上分配

#### 2.15.3.变量的存储类型
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1727342881417-0ebccb5d-a1e2-449b-ae34-2fbcda6473ad.png)

#### 2.15.4.变量的作用域和生命周期
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1727349250895-eeaef88d-b22c-4628-8fe9-11c56578fe66.png)

#### 2.15.5.函数的返回值使用指针
```cpp
int *add1( int a, int b );
int *add2( int a, int b );
int *add3( int a, int b );

int main() {
   int *sum = nullptr;
   sum = add1( 1, 2 );
   add2(1, 2);//这里执行完后，sum里的值为野地址
   cout << *sum << endl;
   system( "pause" );
   return 0;
}
int *add1( int a, int b ) {
   int sum = a + b;
   return &sum;
}

int *add2( int a, int b ) {
   int *sum = new int;
   *sum = a + b;
   return sum;
}

int *add3( int a, int b ) {
   static int sum = 0;
   cout << "sum:" << sum << endl;
   sum = a + b;
   return &sum;
}
```

堆内存便捷初始化函数memset(void *Dst, int Val, size_t Size)

```cpp
int main() {
   int *p = new int[ 20 ];
   // 对p指向的堆内存空间初始化
   memset( p, 0, 20 * sizeof( int ) );
   for ( int i = 0; i < 20; i++ ) {
      cout << *( p + i ) << endl;
   }
   system( "pause" );
   return 0;
}
```

### 2.16.宏
它是一种预处理器指令，在预编译阶段将宏名替换为后面的替换体

可提高代码的可读性

避免函数调用，提高程序效率

宏的定义：

#define            HONG             666

预处理器指令    宏名                 替换体(多行可用 \ 延续)

### 2.17.结构体
结构体就是程序员自定义的一种数据类型

```cpp
struct Student{
	char name[20];
	int age;
	char tel[12];
};

int main() {
	Student student = {"zachariah", 20, "16666666666"};
	cout << student.tel << endl;
	return 0;
}

```

```cpp
struct Student{
	char name[20];
	int age;
	char tel[12];
};

int main() {
    //使用此方式，其他属性可缺省，但vs的C++标准需C++20
	Student student = {.name = "zachariah", .age = 20};
	cout << student.tel << endl;
	return 0;
}

```

```cpp
struct Student{
	char name[20];
	int age;
	char tel[12];
};

int main() {
	Student student;
    //在vs高版本中，需取消项目的sdl检测，才能使用不安全函数
	strcpy(student.name, "zhangsan");
	student.age = 18;
	strcpy(student.tel, "16666666666");
	return 0;
}
```

```cpp
struct Student{
	char name[20];
	int age;
	char tel[12];
};

struct Class{
	Student student1;
	Student student2;
	Student student3;
};
struct Class2{
	Student students[3];
};

int main() {
	Class c1 = { {"zhangsan", 18, "16666666666"},
					 {"lisi", 20, "16666666666"},
					 {"zhaoliu", 22, "16666666666"}
	};
	Class2 c2 = { {{"zhangsan", 18, "16666666666"},
						{"lisi", 20, "16666666666"},
						{"zhaoliu", 22, "16666666666"}
					}
	};
	cout << c2.students[1].name << endl;
    c1 = c2;//结构体变量之间可以相互赋值，也可以使用memcpy()函数
	return 0;
}
```

#### 2.17.1.结构体指针
```cpp
Student student1 = {"zhangsan", 20, "16666666666"};
Student* s_p = &student1;
//方式一(直接解引用)
cout << (*s_p).name << endl;

//方式二(使用指针访问 ->)
cout << s_p->name << endl;
```

#### 2.17.2.使用结构体传值
```cpp
struct Programer{
	char name[10];
	int age;
	double salary;
};

void add_salary(Programer *p, double monery);//使用指针传递
void add_salary2(Programer &p, double monery);//使用引用传递
Programer& add_salary3(Programer p, double monery);//使用返回值传递

int main() {
	Programer zachariah = {"zachariah", 22, 20000};
	zachariah = add_salary3(zachariah, 50000);
	cout << zachariah.salary << endl;
	return 0;
}

void add_salary(Programer *p, double monery){
	p->salary += monery;
}

void add_salary2(Programer& p, double monery){
	p.salary += monery;
}

Programer& add_salary3(Programer p, double monery){
	p.salary += monery;
	return p;
}
```

### 2.18.枚举
```cpp
enum Season{
	SPRING,
	SUMMER,
	AUTUMN,
	WINTER
};

int main() {
	Season s1 = static_cast<Season>(2);
	cout << (s1 == AUTUMN? "当前季节为秋季":"") << endl;
	return 0;
}
```

### 2.19.面向对象
面向对象编程，最重要的第一个概念：类

“人类”是一个抽象的概念，不是具体的某个人。

“类”，是看不见，摸不着的，是一个纯粹的概念.

“类”，是一种特殊的“数据类型”，不是一个具体的数据。

注意：类，和基本数据类型（char/int/short/long/long long/float/double）不同

类的构成：方法和数据

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1729435391717-e28041a6-ebef-49f7-acea-147f020f21f0.png)



#### 2.19.1.类的设计
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1729435426246-780cf727-ab3e-4c57-8fda-dbff68238287.png)

```cpp
class People{
//公有成员
public:
    void test(){
        //如果具体实现在类的内部，此函数被称为内联函数
    }
	void smoke();
	void drink();
	void perm();
	string getName();
	int getAge();
	double getSalary();

//私有成员
private:
    int test = 123;//类内初始值(C++11开始支持)
	string name;
	int age;
	double salary;
};

int main() {

	return 0;
}

//成员函数/方法实例化(具体实现)
void People::smoke(){
	cout << "夜深人静，孤独又寂寞的点燃了一根香烟..." << endl;
}
void People::drink() {
	cout << "夜深人静，孤独又寂寞的喝了一杯香槟..." << endl;
}
void People::perm(){
	cout << "今天心情不错，烫了一个韩式微分..." << endl;
}
string People::getName(){
	return name;
}
int People::getAge(){
	return age;
}
double People::getSalary(){
	return salary;
}
```

####  2.19.2.实例化对象
```cpp
class People {
	//公有成员
public:
	void smoke();
	void drink();
	void perm();
	string getName();
	int getAge();
	double getSalary();

	//私有成员
private:
	string name;
	int age;
	double salary;
};

int main() {
	People p1;
	People *p2;
	p2 = &p1;
	p1.smoke();
	p1.drink();
	p1.perm();
	//指针方式调用对象内部实例函数
	p2->smoke();
	p2->drink();
	p2->perm();
	return 0;
}

//成员函数/方法实例化(具体实现)
void People::smoke() {
	cout << "夜深人静，孤独又寂寞的点燃了一根香烟..." << endl;
}
void People::drink() {
	cout << "夜深人静，孤独又寂寞的喝了一杯香槟..." << endl;
}
void People::perm() {
	cout << "今天心情不错，烫了一个韩式微分..." << endl;
}
string People::getName() {
	return name;
}
int People::getAge() {
	return age;
}
double People::getSalary() {
	return salary;
}
```

#### 2.19.3.构造函数
构造函数的分类：

默认构造函数

自定义的构造函数

拷贝构造函数

赋值构造函数

##### 2.19.3.1.默认构造函数
1.合成的默认构造函数

但没有手动定义默认构造函数时，编译器自动为这个类定义一个构造函数。

1）如果数据成员使用了“类内初始值”，就使用这个值来初始化数据成员。【C++11】

2）否则，就使用默认初始化（<font style="color:#DF2A3F;">实际上，不做任何初始化</font>）不建议使用

<font style="color:#DF2A3F;">仅当数据成员全部使用了“类内初始值”，才宜使用“合成的默认构造函数</font>

##### 2.19.3.2.自定义的构造函数
```cpp
class People {
//公有成员
public:
	//自定义的重载默认构造函数
	People();
	//自定义的带参数的重载构造函数
	People(string name, int age, double salary);
	void smoke();
	void drink();
	void perm();
	string getName();
	int getAge();
	double getSalary();
//私有成员
private:
	string name;
	int age;
	double salary;
};

int main() {
	People p1("Zachariah", 18, 20000);
    cout << p1.getName() << endl;
    cout << p1.getAge() << endl;
    cout << p1.getSalary() << endl;
    return 0;
}

People::People(){

}
//this为指向该对象的特殊指针
People::People(string name, int age, double salary){
	this->name = name;
	this->age = age;
	this->salary = salary;
}
```

##### 2.19.3.3.拷贝构造函数
```cpp
public:
	//自定义的带参数的重载构造函数
	People(string name, int age, double salary);
	//拷贝构造函数
	People(const People&);
	void smoke();
	void drink();
	void perm();
	string getName();
	int getAge();
	double getSalary();
    void toString();
private:
	string name;
	int age;
	double salary;
};

int main() {
	People p1("Zachariah", 18, 20000);
	People p2 = p1;//此时会触发拷贝构造函数
	People p3(p1);//此时也会触发拷贝构造函数
    p2.toString();
    p3.toString();
	return 0;
}
People::People(string name, int age, double salary){
	cout << "自定义带参重载构造函数被调用！" << endl;
	this->name = name;
	this->age = age;
	this->salary = salary;
}
People::People(const People&){
	cout << "拷贝构造函数被调用!" << endl;
    this->name = copy.name;
    this->age = copy.age;
    this->salary = copy.salary;
}
void People::toString(){
	cout << "People[name=" << name << ",age=" << age << 
		",salary=" << salary << "]" << endl;
}
```

此外以下情况也可触发拷贝构造函数

```cpp
//但参数传递进来时：People people = p1
void People::show(People people){
	cout << people.getName() << endl;
}
```

如果不自定义拷贝构造函数，程序会执行合成拷贝构造函数(缺省拷贝构造函数)

合成的拷贝构造函数的缺点: 使用“<font style="color:rgb(255,0,0);">浅拷贝</font>”

如果要拷贝的对象里有指针类型的属性，可能会被浅拷贝影响

所以为了数据的安全性，一般使用自定义拷贝构造函数，在其自定义初始化

```cpp
People::People(const People& copy){
	cout << "拷贝构造函数被调用!" << endl;
	this->name = copy.name;
	this->age = copy.age;
	this->salary = copy.salary;
	//为指针类型属性单独开辟堆内存，以避免浅拷贝带来的影响
	addr = new char[64];
	//深拷贝
	strcpy(addr, copy.addr);
}
```

什么时候调用拷贝构造函数？

 1. 调用函数时，实参是对象，形参不是引用类型

<font style="color:#DF2A3F;">(如果函数的形参是引用类型，就不会调用拷贝构造函数)</font>

2. 函数的返回类型是类，而且不是引用类型

3. 对象数组的初始化列表中，使用对象。

##### 2.19.3.4.赋值构造函数
```cpp
int main() {
	People p1("Zachariah", 18, 20000);
	People p2;
	//此时会触发默认赋值构造函数(位拷贝/浅拷贝)
	p2 = p1;
	return 0;
}
```

此时会造成跟默认拷贝函数一样的影响

所以需要自定义赋值构造函数

```cpp
//赋值构造函数(运算符重载函数)
People& operator=(const People& copy);

//比如p2 = p1，则会调用:p2.operator=(p1);
People& People::operator=(const People& copy){
	if (this == &copy){
		return *this;
	}
	this->name = copy.name;
	this->age = copy.age;
    //如果有必要，需要先释放堆内存的资源
    //delete this->addr;
    //this->addr = new char[ADDR_LEN];
	//深拷贝
	this->addr = new char[ADDR_LEN];
	strcpy(addr, copy.addr);
    //为了便于连续使用(p1 = p2 = p3)(链式处理)
    //需返回当前对象
    return *this;
}
```

```cpp
People p1("Zachariah", 18, 20000);
People& p3 = p1;
People* ptr = &p1;
```

#### 2.19.4.析构函数
```cpp
//析构函数
~People();

People::~People(){
	//构造函数中只要申请了堆内存空间，就需在析构函数内释放
	delete this->addr;
}
```

#### 2.19.5.类的静态成员
```cpp
//People.h
static int peopleCount;

//people.cpp
//对类的静态成员进行初始化
int People::peopleCount = 0;
```

```cpp
//静态成员函数的声明
static int getPeopleCount();

//静态成员函数的定义
int People::getPeopleCount(){
	return peopleCount;
}
//静态成员函数的调用(也可以使用对象进行调用)
cout << People::getPeopleCount() << endl;
```

1）静态数据成员

对象的成员函数（没有static的成员函数）内部，可以直接访问“静态数据成员”

类的静态成员函数（有static的成员函数）内部，可以直接访问“静态数据成员”

即：所有的成员函数，都可以访问静态数据成员。

类不能直接访问普通的静态数据成员（Human::humanCount 非法）

2）静态成员函数

对象可以直接访问静态成员函数

类可以直接访问静态成员函数（Human::getHumanCount()）

在类的静态成员函数（类的静态方法）内部，不能直接访问this指针和对象的数据成员！

在类的静态成员函数（类的静态方法）内部，只能访问类的数据成员

#### 2.19.6.const数据成员
```cpp
//定义常量成员数据
const string bloodType = "未知";//类内初始值仅对C++11及以上版本支持
//对与低版本C++，可在构造函数后加初始化列表的方式

//初始化列表：:属性名(属性值)
People::People():bloodType("未知") {
	People::peopleCount++;
}
//或
People::People(string name, int age, double salar, string bloodType):bloodType(bloodType) {
	People::peopleCount++;
	cout << "自定义带参重载构造函数被调用！" << endl;
	this->name = name;
	this->age = age;
	this->salary = salary;
	this->addr = new char[64];
	strcpy(this->addr, "China");
}
```

<font style="color:#DF2A3F;">const修饰的对象只能调用const修饰的方法/成员函数</font>

C++的成员函数设置建议：

<font style="color:#DF2A3F;">如果一个对象的成员函数，不会修改任何数据成员，那么就强烈：</font>

<font style="color:#DF2A3F;">把这个成员函数，定义为const成员函数！</font>

const成员函数内不能修改任何成员数据

注意：非const引用,  不能对const变量进行引用

注意: const引用, 可以对非const变量进行引用

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1729852678817-fa3fd989-5e34-4c16-9984-decfb9a6b714.png)

#### 2.19.7.组合
```cpp
#pragma once
#include "Cpu.h"


class Computer{
public:

private:
    //一个类里，包含另一个类，称为组合
	Cpu cpu;

	int disk;
	int memory;
};
```

被拥有的对象（芯片）的生命周期与其拥有者（计算机）的生命周期是一致的。

计算机被创建时，芯片也随之创建。

计算机被销毁时，芯片也随之销毁。

拥有者需要对被拥有者负责，是一种比较强的关系，是整体与部分的关系。(has a)

UML中的组合表示：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1729748128337-4f5d4c68-1c9a-4686-96f6-6a591753f1f2.png)

注意包含者使用实心菱形

#### 2.19.8.聚合
```cpp
#pragma once
#include "Cpu.h"

//.h文件中，除组合所需要的类文件要导入，其余应定义为普通类声明即可
class Speaker;

class Computer{
public:
	Computer(const char* brand, const char* version, int disk, int memory);
	~Computer();
    //使用此函数，来减少Speaker与Computer的组合度，使其构成聚合关系
	void addSpeaker(Speaker* speaker);
private:
	Cpu cpu;
	int disk;
	int memory;
	//Speaker与Computer是聚合关系
	Speaker *speaker;
};


```

聚合不是组成关系，被包含的对象，也可能被其他对象包含。

拥有者，不需要对被拥有的对象的生命周期负责。

UML中的组合表示：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1729758516139-0589ecb4-cf80-4376-9c8c-fae7bf6261eb.png)

#### 2.19.9.继承与派生
继承和派生在UML中的表示

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730024748753-3783d8aa-970f-4161-b37a-2ba1ade562b7.png)

注意是“空心三角箭头”，从子类【派生的类】指向父类【被继承的类】

父类，也称为“基类”

**<font style="color:#DF2A3F;">除了“构造函数”和“析构函数”，</font>**

**<font style="color:#DF2A3F;">父类的所有成员函数，以及数据成员，都会被子类继承！</font>**

```cpp
class Father{
public:
	Father(const string& name, const int age);
	~Father();

	string getName();
	int getAge();
	string description();
private:
	string name;
	int age;
};
```

```cpp
#include "Father.h"

class Son : public Father{
public:
   Son(const string& name, const int age, const string& game);
   ~Son();

   string getGame();
private:
   string game;
};

//Son如果有父类时，会先调用父类的构造方法先初始化父类的数据之后再调用Son的构造方法再进行初始化Son的数据
Son::Son(const string& name, const int age, const string& game):Father(name, age) {
	//如果子类构造方法中没有体现出父类的构造时，会调用父类的默认构造函数
	cout << __FUNCTION__ << "被调用！" << endl;
	this->game = game;
}
```

如果子类要重写父类的部分方法需进行如下操作：

```cpp
std::string Father::description(){
	stringstream description;
	description << "name=" << name << ",age=" << age;
	return description.str();
}
```

```cpp
std::string Son::description(){
	stringstream description;
    //子类成员函数中，不能直接访问从父类继承过来的私有成员/属性
	description << "name=" << __super::getName() << ",age=" << __super::getAge() << ",game=" << game;
	return description.str();
}
//或
std::string Son::description(){
	stringstream description;
    //子类成员函数中，不能直接访问从父类继承过来的私有成员/属性
	description << "name=" << this->getName() << ",age=" << this->getAge() << ",game=" << game;
	return description.str();
}
```

注：子类在调用任意属性/方法时，都会默认在自己内存中区寻找，如果找不到，再去从父类继承中的内存去寻找

子类, 一般会添加自己的数据成员/成员函数, 

或者, 重新定义从父类继承的方法!!! 子类对象就会调用自己重新定义的方法, 不会调用父类的同名方法

#### 2.19.20.子类对象内存分布
设置vs编译器：

在命令行中添加选项：（打印指定类的内存分布）

/d1 reportSingleClassLayout<font style="color:rgb(255,0,0);">Father</font>      /d1 reportSingleClassLayout<font style="color:rgb(255,0,0);">Son</font>

```cpp
1>class Son	size(60):
1>	+---
1> 0	| +--- (base class Father)
1> 0	| | ?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@ name
1>28	| | age
1>	| +---
1>32	| ?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@ game
1>	+---
1>class Son	size(60):
1>	+---
1> 0	| +--- (base class Father)
1> 0	| | ?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@ name
1>28	| | age
1>	| +---
1>32	| ?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@ game
1>	+---
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730103778758-e191b85b-7987-4531-9044-2d92b4882744.png)

#### 2.19.21.protected访问修饰符
<font style="background-color:rgb(255,255,255);">一个类, 如果希望, 它的成员, 可以被自己的子类(派生类)直接访问,  </font>

<font style="background-color:rgb(255,255,255);">但是, 又不想被外部访问那么就可以把这些成员, 定义为 protected访问权限!!!</font>

效果：

类的成员函数中，可以直接访问它的父类的protected成员。

但是在外部，别人又不能直接通过子类对象来访问这些成员。

<font style="background-color:rgb(255,255,255);">访问权限总结</font><font style="background-color:rgb(255,255,255);">:</font>

<font style="background-color:rgb(255,255,255);">public</font>

<font style="background-color:rgb(255,255,255);"></font><font style="background-color:rgb(255,255,255);">外部可以直接访问</font><font style="background-color:rgb(255,255,255);">.</font>

<font style="background-color:rgb(255,255,255);">	</font><font style="background-color:rgb(255,255,255);">可以通过对象来访问这个成员</font>

<font style="background-color:rgb(255,255,255);">	Father  wjl("王健林", 65);</font>

<font style="background-color:rgb(255,255,255);">	</font><font style="background-color:rgb(255,255,255);">wjl.getName();</font>

<font style="background-color:rgb(255,255,255);">private</font>

<font style="background-color:rgb(255,255,255);"></font><font style="background-color:rgb(255,255,255);">外部不可以访问</font>

<font style="background-color:rgb(255,255,255);">	</font><font style="background-color:rgb(255,255,255);">自己的成员函数内</font><font style="background-color:rgb(255,255,255);">, </font><font style="background-color:rgb(255,255,255);">可以访问</font>

<font style="background-color:rgb(255,255,255);">	Father  wjl("王健林", 65);</font>

<font style="background-color:rgb(255,255,255);">	</font><font style="background-color:rgb(255,255,255);">wjl.name; // </font><font style="background-color:rgb(255,255,255);">错误</font><font style="background-color:rgb(255,255,255);">!!!</font>

<font style="background-color:rgb(255,255,255);">	</font><font style="background-color:rgb(255,255,255);">Father</font><font style="background-color:rgb(255,255,255);">内的所有成员函数内</font><font style="background-color:rgb(255,255,255);">, </font><font style="background-color:rgb(255,255,255);">可以直接访问</font><font style="background-color:rgb(255,255,255);">name</font>

  子类对象不能访问

<font style="background-color:rgb(255,255,255);">protected</font>

<font style="background-color:rgb(255,255,255);">protected</font><font style="background-color:rgb(255,255,255);">和</font><font style="background-color:rgb(255,255,255);">private</font><font style="background-color:rgb(255,255,255);">非常相似</font>

<font style="background-color:rgb(255,255,255);">	</font><font style="background-color:rgb(255,255,255);">和</font><font style="background-color:rgb(255,255,255);">private</font><font style="background-color:rgb(255,255,255);">的唯一区别</font><font style="background-color:rgb(255,255,255);">: </font>

<font style="background-color:rgb(255,255,255);">	</font><font style="background-color:rgb(255,255,255);">   protecte: </font><font style="background-color:rgb(255,255,255);">子类的成员函数中可以直接访问</font>

<font style="background-color:rgb(255,255,255);">	   private:  子类的成员函数中不可以访问</font>

#### <font style="background-color:rgb(255,255,255);">2.19.22.派生和继承的各种方式(继承方式)</font>
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730105176797-1b225ce8-2086-4744-8d22-591f47c4779c.png)

小结：

public 继承全不变

private继承全变私

protected继承只把public降级为protected

#### 2.19.23.**子类和父类的构造函数的调用顺序**
```cpp
class M {
public:
	M() {
		cout << __FUNCTION__ << endl;
	}
};

class N {
public:
	N() {
		cout << __FUNCTION__ << endl;
	}
};

class A {
public:
	A() {
		cout << __FUNCTION__ << endl;
	}
};

class B : public A {
public:
	B() {
		cout << __FUNCTION__ << endl;
	}
private:
	M m1;
	M m2;
	static N ms;
};

N B::ms;  //静态成员

int main(void) {
	B b;
	system("pause");
}
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730197064022-982f57e4-9efc-4b9d-82cb-c83b52a3cc33.png)

当创建子类对象时, 构造函数的调用顺序：

静态数据成员的构造函数->  父类的构造函数->  非静态的数据成员的构造函数-> 自己的构造函数

注意: 

无论创建几个对象, 该类的静态成员只构建一次, 所以静态成员的构造函数只调用1次!!!

子类的析构函数执行顺序恰好和构造函数的执行顺序相反

#### 2.19.24.子类型关系
公有继承时，派生类的对象可以作为基类的对象处理，派生类是基类的子类型。

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730200592684-91deda81-411e-4e52-9497-af725abb6737.png)

B类就是A类的子类型.

子类型关系具有单向传递性。

C类是B类的子类型 

B类是A类的子类型

子类型的作用：

在需要父类对象的任何地方，可以使用”公有派生”的子类的对象来替代，

从而可以使用相同的函数统一处理基类对象和公有派生类对象

<font style="color:#DF2A3F;">即：形参为基类对象时，实参可以是派生类对象(解耦合)</font>

#### 2.19.25.子类型的应用
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730201380458-9da00e29-614a-4fbf-bfb5-431e902a821d.png)

#### 2.19.26.多重继承
```cpp
#include "Father.h"
#include "Mother.h"

class Son : public Father, public Mother{
public:
	Son(const string lastName, const string firstName, const string food, const string game);
	~Son();
	void playGame();
private:
	string game;
};
//具体实现
Son::Son(const string lastName, const string firstName, const string food, const string	game) :Father(lastName, firstName), Mother(food){
	this->game = game;
}
```

#### 2.19.27.多重继承的二义性问题
当继承的两个父类有同名方法时：

```cpp
#include "Son.h"

int main(){
	Son ch("chen", "he", "川菜", "LOL");
	ch.playBasketball();
    //使用类名::进行指定调用从哪个基类继承的方法
	ch.Father::dance();
	ch.Mother::dance();
	ch.playGame();
	return 0;
}
```

子类可指定限定类名去调用即可，避免调用不明确问题

也可在子类中重写这个方法来避免多重继承的二义性问题

#### 2.19.28.虚基类和虚继承
多重继承在”菱形继承”中的重大缺点

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730254443640-75321a7a-1976-4d16-99c6-f89d51c02e94.png)

```cpp
class Tel {
public:
	Tel() {

	}
	Tel(const string number) {
		this->number = number;
	}
	~Tel() {

	}
protected:
	string number;
};

class FixedLine : public Tel {
public:
	FixedLine() {

	}
	FixedLine(const string number) {
		this->number = number;
	}
	~FixedLine() {

	}
};

class Phone : public Tel {
public:
	Phone() {

	}
	Phone(const string number) {
		this->number = number;
	}
	~Phone() {

	}
};
class WirelessTel : public FixedLine, public Phone {
public:
	WirelessTel() {

	}
	WirelessTel(const string number) {
        //此处会报错：number不明确
		this->number = number;
	}
	~WirelessTel();
};
```

这是由于此类继承了两个基类，两个基类都有number这个属性

内存分布图：

```cpp
class WirelessTel	size(56):
	+---
0	| +--- (base class FixedLine)
0	| | +--- (base class Tel)
0	| | | ?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@ number
	| | +---
	| +---
28	| +--- (base class Phone)
28	| | +--- (base class Tel)
28	| | | ?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@ number
	| | +---
	| +---
	+---
```

解决方法1：子类可以加上类名限定，来指定调用哪个number

解决方法2：改用虚继承

```cpp
//此时共同的基类Tel称为"虚基类"
class Tel {
public:
	Tel() {

	}
	Tel(const string number) {
		this->number = number;
	}
	~Tel() {

	}

protected:
	string number;
};

//虚继承
class FixedLine : virtual public Tel {
public:
	FixedLine() {

	}
	FixedLine(const string number) {
		this->number = number;
	}
	~FixedLine() {

	}

};
//虚继承
class Phone : virtual public Tel {
public:
	Phone() {

	}
	Phone(const string number) {
		this->number = number;
	}
	~Phone() {

	}
};
class WirelessTel : public FixedLine, public Phone {
public:
	WirelessTel() {

	}
	WirelessTel(const string number) {
		this->number = number;
	}
	~WirelessTel() {

	}
};
```

虚继承的内存结构：

添加命令行选项:  
/d1 reportSingleClassLayout<font style="color:rgb(43,145,175);background-color:rgb(255,255,255);">WirelessTel</font>

```cpp
class WirelessTel	size(36):
	+---
 0	| +--- (base class FixedLine)
 0	| | {vbptr}
	| +---
 4	| +--- (base class Phone)
 4	| | {vbptr}
	| +---
	+---
	+--- (virtual base Tel)
 8	| ?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@ number
	+---
WirelessTel::$vbtable@FixedLine@:
 0	| 0
 1	| 8 (WirelessTeld(FixedLine+0)Tel)
WirelessTel::$vbtable@Phone@:
 0	| 0
 1	| 4 (WirelessTeld(Phone+0)Tel)
vbi:	   class  offset o.vbptr  o.vbte fVtorDisp
             Tel       8       0       4 0
```

此时number属性从两个基类那里继承过来就只有一个副本了

### 2.20.C++流
IO: 向设备输入数据和输出数据

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730280926296-67e2085a-b654-4e7d-9651-bd6253a196c3.png)

设备: 

1) 文件

2) 控制台

3) 特定的数据类型(stringstream)

c++中,必须通过特定的已经定义好的类, 来处理IO(输入输出)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730281040499-fbd23779-5dab-4f68-9648-62c0090be5ce.png)文件打开方式:

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730284979470-09c670a8-27cd-423e-add1-85a0d05a3544.png)

以上打开方式，可以使用位操作|  组合起来

#### 2.20.1.对文本文件进行读写操作
```cpp
#include <iostream>
#include <fstream>

using namespace std;

int main() {
	string name;
	int age;
	ofstream outfile;
	//文件输出流打开文件
	outfile.open("user.txt");
	while (true) {
		cout << "请输入姓名：";
		cin >> name;
		//是否遇到文件结束符
		if (cin.eof()){
			break;
		}
		//写入姓名到文件
		outfile << name << '\t';
		cout << "请输入年龄：";
		cin >> age;
		//写入年龄到文件
		outfile << age << endl;
	}
	//关闭文件流
	outfile.close();
	return 0;
}
```

```cpp
#include <iostream>
#include <fstream>

using namespace std;

int main() {
	ifstream infile;
	string name;
	int age;
	infile.open("user.txt");

	while (true) {
		infile >> name;
		if (infile.eof()){
			break;
		}
		infile >> age;
		cout << name << '\t' << age << endl;
	}
    infile.close();
	return 0;
}
```

#### 2.20.2.对二进制文件进行读写操作
```cpp
int main() {
	ofstream outfile;
	string name;
	int age;
	outfile.open("user.data", ios::out | ios::binary);

	while (true) {
		cout << "请输入姓名：";
		cin >> name;
		if (cin.eof()) {
			break;
		}
		outfile << name << '\t';
		cout << "请输入年龄：";
		cin >> age;
        //这种方式写入文件中类型无法保持
		//outfile << age << endl;
        //这种可保留age的类型并写入二进制文件中
        outfile.write((char *)&age, sizeof(age));
        
	}
	outfile.close();
	return 0;
}
```

```cpp
int main() {
	ifstream infile;
	string name;
	int age;
	infile.open("user.data", ios::in | ios::binary);
	while (true) {
		infile >> name;
		if (infile.eof()) {
			break;
		}
		cout << name << '\t';
		char tmp;
        //在内存中，name与age之间多一字节表示'\t'，需要跳过
		infile.read(&tmp, sizeof(tmp));
		infile.read((char *)&age, sizeof(age));
		cout << age << endl;
	}
	infile.close();
	return 0;
}
```

```cpp
int main() {
	ofstream outfile;
	string name;
	int age;
	outfile.open("user.txt");
	while (true) {
		stringstream str;
		cout << "请输入姓名：";
		cin >> name;
		if (cin.eof()){
			break;
		}
		cout << "请输入年龄：";
		cin >> age;
		str << name << "\t\t\t" << age << endl;
		outfile << str.str();
	}

	outfile.close();
	return 0;
}
```

```cpp
#include <fstream>
#include <iostream>
#include <string>
#include <sstream>
#include <Windows.h>

using namespace std;

int main(void)
{
	char name[32];
	int age;
	string line;
	ifstream infile;
	infile.open("user.txt");

	while (1) {
		getline(infile, line);
		if (infile.eof()) { //判断文件是否结束
			break;
		}
		sscanf_s(line.c_str(), "姓名:%s 年龄:%d", name, sizeof(name),&age);
		cout << "姓名:" << name << "\t\t年龄:" << age << endl;
	}

	infile.close();

	system("pause");
	return 0;
}
```

#### 2.20.3.文件流的状态检查
s.is_open( )

文件流是否打开成功, 

s.eof( )  流s是否结束

s.fail( ) 

流s的failbit或者badbit被置位时, 返回true

failbit: 出现非致命错误，可挽回, 一般是软件错误

badbit置位, 出现致命错误, 一般是硬件错误或系统底层错误, 不可挽回

s.bad( ) 

流s的badbit置位时, 返回true

s.good( ) 

流s处于有效状态时, 返回true

s.clear( )

流s的所有状态都被复位

#### 2.20.4.文件流的定位
seekg( off_type offset,      //偏移量

ios::seekdir origin );  //起始位置

作用：设置输入流的位置

参数1： 偏移量

参数2： 相对位置

 beg  	相对于开始位置

 cur  	相对于当前位置

 end	相对于结束位置

```cpp
int main(void) {
	system("chcp 65001");
	ifstream infile;
	string line;
	infile.open("user.txt");
	if (!infile.is_open()) {
		return 1;
	}
	infile.seekg(-50, infile.end);
	while (!infile.eof()) {
		getline(infile, line);
		cout << line << endl;
	}
	infile.close();
	system("pause");
	return 0;
}
```

tellg

返回该输入流的当前位置(距离文件的起始位置的偏移量)

```cpp
#include <fstream>
#include <iostream>
#include <string>

using namespace std;

long long getFileSize(const string fileName);

int main(void) {
	cout << getFileSize("user.txt") << endl;
	system("pause");
	return 0;
}

long long getFileSize(const string fileName) {
	ifstream infile;
	infile.open(fileName);
	if (!infile.is_open()) {
		return 1;
	}
	infile.seekg(0, infile.end);
	long long ret = infile.tellg();
	return ret;
}
```

seekp输出流专属的，可在文件的位置插入数据

```cpp
#include <fstream>
#include <iostream>
#include <string>

using namespace std;

int main(void) {
	ofstream outfile;
	outfile.open("user.txt");
	if (!outfile.is_open()){
		return 1;
	}
	outfile << "123456789";
	outfile.seekp(4, outfile.beg);
	outfile << "abc";

	outfile.close();
	system("pause");
	return 0;
}
```

### 2.21.友元
为什么要使用友元

C++是面向对象的，目的之一：封装

封装：

优点之一，就是安全。

缺点：在某些特殊的场合，不是很方便。

使用前提：

某个类需要实现某种功能，但是这个类自身，因为各种原因，无法自己实现。

需要借助于“外力”才能实现。

#### 2.21.1.友元函数
```cpp
class Computer {
public:
	Computer();
	~Computer();

	string description();

private:
	string cpu;
    //将外部函数的声明提取到类中，即可成为该类的友元函数
	friend void upgrade(Computer *computer);
};

void upgrade(Computer *computer);
int main() {
	Computer computer;
	cout << computer.description();
	upgrade(&computer);
	cout << computer.description();
	return 0;
}

void upgrade(Computer *computer) {
	//此时，在外部就能访问Computer类中的私有属性
	computer->cpu = "i9";
}
```

```cpp
class Computer {
public:
	Computer();
	~Computer();

	string description();
	
private:
	string cpu;
	//将外部类的成员函数设为本类的友元函数，外类即可通过该函数访问该类的私有属性
	friend void ComputerService::upgrade(Computer *computer);
};

int main() {
	Computer computer;
	ComputerService service;
	cout << computer.description();
	service.upgrade(&computer);
	cout << computer.description();
	return 0;
}
```

功能上，这两种形式，都是相同，应用场合不同。

一个是，使用普通的全局函数，作为自己的朋友，实现特殊功能。

一个是，使用其他类的成员函数，作为自己的朋友，实现特殊功能。

#### 2.21.2.友元类
为什么要使用友元类

一个独立的咨询师，给其他企业做服务时，这个咨询师作为企业的“友元函数”即可。

一个大型的咨询服务公司，比如IBM（IT事务）, 普华永道（会计事务），给其他企业做服务时，使用友元函数就不是很方便了，因为需要设计很多友元函数，不方便。

解决方案：使用“友元类”

```cpp
class Computer {
public:
	Computer();
	~Computer();

	string description();
	
private:
	string cpu;
	
	//将外部类设为该类的友元类，即可通过外部类的任意属性访问该类中的私有属性
	friend class ComputerService;
};

//ComputerService.cpp
void ComputerService::upgrade(Computer *computer) {
	computer->cpu = "i9";
}
//main.cpp
int main() {
	Computer computer;
	ComputerService service;
	cout << computer.description();
	service.upgrade(&computer);
	cout << computer.description();
	return 0;
}
```

### 2.22.运算符重载
为什么要使用运算符重载

C/C++的运算符，支持的数据类型，仅限于基本数据类型。

问题：一头牛+一头马 = ？（牛马神兽？）

     一个圆+一个圆 = ？（想要变成一个更大的圆）

	  一头牛– 一只羊= ？(想要变成4只羊，原始的以物易物：1头牛价值5只羊)

解决方案：

使用运算符重载

```cpp
class Cow {
public:
   Cow();
   Cow(const int weight = 0);
   ~Cow();

   // 1斤牛肉 = 2斤猪肉 一斤羊肉 = 3斤猪肉
   // 牛 + 羊 = 几斤猪肉?
   Pork operator+(const Goat &goat);  // 牛 + 羊
   Pork operator+(const Cow &cow);    // 牛 + 牛

private:
   int weight;
};

Pork Cow::operator+(const Goat &goat) {
   int porkWeight = (this->weight) * 2 + (goat.getWeight()) * 3;
   return Pork(porkWeight);
}

Pork Cow::operator+(const Cow &cow){
   int porkWeight = (this->weight) * 2 + (cow.weight) * 2;
   return Pork(porkWeight);
}

//main.cpp
int main() {
   system("chcp 65001");
   Cow c1(6);
   Goat g1(3);
   Pork pork = c1 + g1;//等同于：Pork pork = c1.operator+(g1);
   cout << pork.description() << endl;
   system("pause");
   return 0;
}
```

```cpp
class Cow {
public:
   Cow();
   Cow(const int weight = 0);
   ~Cow();

private:
   int weight;
   // 1斤牛肉 = 2斤猪肉 一斤羊肉 = 3斤猪肉
   // 牛 + 羊 = 几斤猪肉?
   friend Pork operator+(const Cow &cow1, const Cow &cow2);
   friend Pork operator+(const Cow &cow, const Goat &goat);
};

//main.cpp
Pork operator+(const Cow &cow1, const Cow &cow2);
Pork operator+(const Cow &cow, const Goat &goat);
int main() {
   system("chcp 65001");
   Cow c1(100);
   Cow c2(200);
   Pork pork = c1 + c2;
   cout << pork.description() << endl;
   system("pause");
   return 0;
}
Pork operator+(const Cow &cow1, const Cow &cow2) {
   int porkWeight = (cow1.weight + cow2.weight) * 2;
   return Pork(porkWeight);
}
Pork operator+(const Cow &cow, const Goat &goat) {
   int porkWeight = (cow.weight * 2) + (goat.getWeight()) * 3;
   return Pork(porkWeight);
}
```

两种方式的区别

区别：

1. 使用成员函数来实现运算符重载时，少写一个参数，因为第一个参数就是this指针。

两种方式的选择：

1. 一般情况下，单目运算符重载，使用成员函数进行重载更方便（不用写参数）

2. 一般情况下，双目运算符重载，使用友元函数更直观

方便实现a+b和b+a相同的效果，成员函数方式无法实现。

  例如：100 + cow;   只能通过友元函数来实现

cow +100;    友元函数和成员函数都可以实现

1. 为了防止对标准类型进行运算符重载，  
C++规定重载运算符的操作对象至少有一个不是标准类型，而是用户自定义的类型

比如不能重载 1+2 

但是可以重载  cow + 2 和 2 + cow   // cow是自定义的对象

2.不能改变原运算符的语法规则， 比如不能把双目运算符重载为单目运算

1. 不能改变原运算符的优先级

2. 不能创建新的运算符，比如 operator**就是非法的, operator*是可以的

3. 不能对以下这四种运算符，使用友元函数进行重载

= 赋值运算符，（）函数调用运算符，[ ]下标运算符，->通过指针访问类成员

6. 不能对禁止重载的运算符进行重载

不能被重载的运算符

| 成员访问 | . |
| --- | --- |
| 域运算 | ：： |
| 内存长度运算 | sizeof |
| 三目运算 | ? : : |
| 预处理 | # |


可以被重载的运算符

| 双目运算符 | +    -    *    /    % |
| --- | --- |
| 关系运算符 | ==  !=   <   <=   >   >=  |
| 逻辑运算符 | &&  ||    ! |
| 单目运算符 | +(正号)   -（负号）  *（指针）  &（取地址）  ++   -- |
| 位运算 | &    |   ~    ^    <<（左移）    >>（右移） |
| 赋值运算符 | =   +=   -=    *=   /=   %=  &=  |=  ^=  <<=   >>= |
| 内存分配 | new   delete  new[ ]   delete[ ] |
| 其他 | ( )  函数调用<br/>-> 成员访问   [ ]   下标<br/>， 逗号 |


#### 2.22.1.重载赋值运算符
```cpp
// 注意返回类型 和参数类型
Boy& Boy::operator=(const Boy& boy)
{
	if (name) {
		delete name;  //释放原来的内存
	}
	name = new char[strlen(boy.name) + 1]; //分配新的内存
	strcpy_s(name, strlen(boy.name)+1, boy.name);

	this->age = boy.age;
	this->salary = boy.salary;
	this->darkHorse = boy.darkHorse;
	//this->id = boy.id;  //根据需求来确定是否要拷贝id
	return *this;
}
```

#### 2.22.2.重载关系运算符
```cpp
bool Boy::operator>(const Boy& boy)
{
	// 设置比较规则：
	// 薪资 * 黑马系数 + (100-年龄）*100
	if (power() > boy.power()) {
		return true;
	}
	else {
		return false;
	}
}

bool Boy::operator<(const Boy& boy)
{
	if (power() < boy.power()) {
		return true;
	}
	else {
		return false;
	}
}

bool Boy::operator==(const Boy& boy)
{
	if (power() == boy.power()) {
		return true;
	}
	else {
		return false;
	}
}
```

#### 2.22.3.重载运算符[](下标运算符的重载)
```cpp
//Boy.h
typedef enum {
	AGE,
	SALARY,
	DARK_HORSE,
	POWER
}BOY_KEY_TYPE;
int Boy::operator[](int index)
{
	if (index == 0) {
		return age;
	}
	else if (index == 1) {
		return salary;
	}
	else if (index == 2) {
		return darkHorse;
	}
	else if (index == 3) {
		return power();
	}
	else {
		return -1;
	}
}

//main.cpp
int main(void) {
	Boy boy1("Rock", 38, 58000, 5);
	Boy boy2("Jack", 25, 50000, 10);
	
	std::cout << "age:" << boy1["age"] << std::endl;
	std::cout << "salary:" << boy1["salary"] << std::endl;
	std::cout << "darkHorse:" << boy1["darkHorse"] << std::endl;
	std::cout << "power:" << boy1["power"] << std::endl;
}
```

#### 2.22.4.重载输入输出运算符
```cpp
friend ostream& operator<<(ostream& os, const Boy& boy);
friend istream& operator>>(istream& is, Boy& boy);

//main.cpp
ostream& operator<<(ostream& os, const Boy& boy) {
	os << "ID:" << boy.id << "\t姓名:" << boy.name << "\t年龄:" << boy.age << "\t薪资:"
		<< boy.salary << "\t黑马系数:" << boy.darkHorse;
	return os;
}
istream& operator>>(istream& is, Boy& boy){
	string name2;
	is >> name2 >> boy.age >> boy.salary >> boy.darkHorse;
	boy.name = (char*)malloc((name2.length()+1) * sizeof(char));
	strcpy_s(boy.name, name2.length() + 1, name2.c_str());
	return is;
}
```

#### 2.22.5.重载类型运算符
普通类型=> 类类型

调用对应的只有一个参数【参数的类型就是这个普通类型】的构造函数

```cpp
//Boy.h
Boy(int salary);
Boy(const char *name);
//Boy.cpp
Boy::Boy(int salary) {
	const char *defaultName = "未命名";
	name = new char[strlen(defaultName) + 1];
	strcpy_s(name, strlen(defaultName) + 1, defaultName);

	age = 0;
	this->salary = salary;
	darkHorse = 0;
	this->id = ++LAST_ID;
}
Boy::Boy(const char *name) {
	this->name = new char[strlen(name) + 1];
	strcpy_s(this->name, strlen(name) + 1, name);

	age = 0;
	this->salary = 0;
	darkHorse = 0;
	this->id = ++LAST_ID;
}
//main.cpp
int main() {
	Boy b1 = 100;
	Boy b2 = "zhangsan";
	cout << b1.description() << endl;
	cout << b2.description() << endl;
	return 0;
}
```



类类型=> 普通类型

调用特殊的运算符重载函数，类型转换函数，不需要写返回类型

类型转换函数：operator  普通类型( )

```cpp
//Boy.h
//类型运算符，不需要返回类型
operator int() const;
operator string() const;
//Boy.cpp
Boy::operator int() const {
	return power();
}
Boy::operator string() const {
	return name;
}
//main.cpp
int main() {
	Boy b1 = 20000;
	Boy b2 = "zhangsan";
	int salary = b1;
	string name = b2;
	return 0;
}
```



类类型A => 类类型B

调用对应的只有一个参数【参数的类型就是类类型A】的构造函数

也可以使用类型转换函数，但是使用对应的构造函数更合适。

实例：

把Boy类型，转换为Man类型

```cpp
//Man.h
Man(const Boy &boy);
// 下标运算符的重载
int operator[](std::string index) const;
int operator[](int index) const;

//Man.cpp
Man::Man(const Boy &boy) {
	int len = strlen((char *)boy) + 1;
	name = new char[len];
	strcpy_s(name, len, (char *)boy);
	age = boy[AGE];
	salary = boy[SALARY];
}

//main.cpp
int main() {
	Boy b1 = "zhangsan";
	Man m1 = b1;
	cout << m1 << endl;
	return 0;
}
```

### 2.23.多态
```cpp
//Father.h
class Father {
public:
	Father();
	~Father();

	void play();
private:

};
//Father.cpp
void Father::play() {
	cout << "喝下午茶" << endl;
}
//Son.h
class Son : public Father {
public:
	Son();
	~Son();
	void play();
private:

};
//Son.cpp
void Son::play() { 
	cout << "打LOL" << endl;
}
//main.cpp
#define SIZE(ARR)(sizeof(ARR) / sizeof(ARR[0]))

using namespace std;

void party(Father** men, int n);

int main() {
	
	Father father;
	Son s1, s2;
	Father* men[] = {&father, &s1, &s2};
	party(men, SIZE(men));
	system("pause");
	return 0;
}
void party(Father** men, int n) {
	for (int i = 0; i < n; i++) {
		men[i]->play();
	}
}
```

最终执行结果：

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730902482467-d097255e-21a2-4a63-bd20-d944c87e7653.png)

可以发现并没有执行Son自己的play方法

解决方法：

```cpp
//Father.h
virtual void play();
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1730902594031-87243351-2d78-47dd-a495-a69f419ad994.png)

即可实现C++中的多态机制

#### 2.23.1.虚函数的使用
多态的本质：

形式上，使用统一的父类指针做一般性处理，

但是实际执行时，这个指针可能指向子类对象，

形式上，原本调用父类的方法，但是实际上会调用子类的同名方法。

【注意】

程序执行时，父类指针指向父类对象，或子类对象时，在形式上是无法分辨的！

只有通过多态机制，才能执行真正对应的方法。



虚函数的定义：

在函数的返回类型之前使用virtual

<font style="color:rgb(255,0,0);">只在成员函数的声明中添加virtual, 在成员函数的实现中不要加virtual</font>

虚函数的继承：

如果某个成员函数被声明为虚函数，那么它的子类【派生类】，以及子类的子类中，所继承的这个成员函数，也自动是虚函数。

**<font style="color:rgb(255,0,0);background-color:rgb(255,255,0);">如果在子类中重写这个虚函数，可以不用再写virtual, 但是仍建议写virtual, 更可读！</font>**

#### 2.23.2.虚函数表
```cpp
#include <iostream>
using namespace std;

class Father {
public:
virtual void func1() { cout << "Father::func1" << endl; }
virtual void func2() { cout << "Father::func2" << endl; }
virtual void func3() { cout << "Father::func3" << endl; }
void func4() { cout << "非虚函数：Father::func4" << endl; }
public:  //为了便于测试，特别该用public
int x = 100;
int y = 200;
static int z;
};

typedef void (*func_t)(void);
int Father::z = 1;
int main(void) {
    Father father;

    // 含有虚函数的对象的内存中，最先存储的就是“虚函数表”
    cout << "对象地址：" << (int*)&father << endl;

    int* vptr = (int*)*(int*)&father;
    cout << "虚函数表指针vptr：" << vptr << endl;

    cout << "调用第1个虚函数: ";
    ((func_t) * (vptr + 0))();

    cout << "调用第2个虚函数：";
    ((func_t) * (vptr + 1))();

    cout << "调用第3个虚函数: ";
    ((func_t) * (vptr + 2))();


    cout << "第1个数据成员的地址: " << endl;
    cout <<  &father.x << endl;
    cout << std::hex << (int)&father + 4 << endl;
    cout << "第1个数据成员的值：" << endl;
    cout << std::dec <<  father.x << endl;
    cout << *(int*)((int)&father + 4) << endl;

    cout << "第2个数据成员的地址: " << endl;
    cout << &father.y << endl;
    cout << std::hex << (int)&father + 8 << endl;
    cout << "第2个数据成员的值：" << endl;
    cout << std::dec << father.y << endl;
    cout << *(int*)((int)&father + 8) << endl;
    cout << "sizeof(father)==" << sizeof(father) << endl;
    Father father2;
    cout << "father的虚函数表：";
    cout << *(int*)(*(int*)&father) << endl;
    cout << "father2的虚函数表：";
    cout << *(int*)(*(int*)&father2) << endl;
    system("pause");
    return 0;
}
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1731065448373-4dc4963c-52c2-4ab2-9c2a-ad91bf1db368.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1731066460359-afae3fd9-cc1d-4b52-8cfa-d22e4ba79678.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1731065410705-b826ddfc-1df2-4fe3-ac65-45801a836775.png)

```cpp
typedef void(*func_t)(void);

using namespace std;

class Father {
public:
	virtual void func1() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; }
	virtual void func2() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; }
	virtual void func3() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; }
	void func4() { cout << "非虚函数：" << __FUNCTION__ << "被调用" << endl; };
public:
	int x = 1;
	int y = 2;
	static int a;
};
int Father::a = 10;

class Son : Father {
public:
	void func1() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; };
	virtual void func5() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; };
};

int main() {
	Son son;
	cout << "son的地址：" << (int*)&son << endl;
	int* vptr = (int*)*(int*)&son;
	((func_t) * (vptr + 0))();//调用第一个虚函数
	((func_t) * (vptr + 1))();//调用第二个虚函数
	((func_t) * (vptr + 2))();//调用第三个虚函数
	((func_t) * (vptr + 3))();//调用第四个虚函数
	/*
		在C++中，*(int*)&son 和 son 是两种不同的表达式，代表不同的意义和使用方式。让我们详细看看它们的区别：
		son
		类型: son 是一个对象，具体类型是 Son。
		用途: 直接使用对象，可以调用其成员函数和访问其成员变量（取决于访问权限）。
		处理: 当你使用 son 时，你是在直接处理 Son 对象的实例。
		*(int*)&son
		类型: 这是通过地址和类型转换操作得到的结果，最终类型是 int 的引用。

		首先，&son 得到了 son 对象的地址，其类型是 Son*。
		然后，(int*)&son 将这个指针类型从 Son* 转换为了 int*。这一步是未经检查的类型转换，意味着我们告诉编译器，把这个对象地址当作一个指向 int 的指针。
		最后，*(int*)&son 解引用这个指针，获取 Son 对象在内存中的起始位置存储的内容，并将其视为一个 int。
		用途: 这种表达方式用于访问 Son 对象内存布局中的数据，而不经过类型安全的访问。特别是在处理对象内存内容或进行底层优化时可能会用到。
		普通情况下的差异
		安全性和类型检查: 使用 son 是类型安全的，编译器会进行类型检查，确保你只能进行符合类型语义的操作。而 *(int*)&son 则是绕过了类型系统的检查，直接操作内存，这可能导致未定义行为。
		用途和意图: 直接使用 son 表达了面向对象编程的意图，使用对象的方法和属性。而 *(int*)&son 则更像是底层操作，通常用在需要直接操控内存或者进行某些特定优化时。
		小结
		*(int*)&son 主要用于探索或修改对象内存布局（如虚函数表指针），但这种操作很少在现代C++中使用，主要是因为它破坏了C++的类型安全特性，并可能导致未定义行为。正常情况下，应通过类提供的接口（成员函数和变量）来访问和操作对象。
	*/
	cout << *(int*)((int)&son + 4) << endl;//调用第一个属性
	cout << *(int*)((int)&son + 8) << endl;//调用第二个属性
	return 0;
}
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1731074945088-8fc9661d-fdca-4e52-aa0f-7b0b7d5356ea.png)

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1731076227962-0bd71e4f-e37b-48c1-bb5e-768258f654b6.png)

#### 2.23.3.多继承的虚函数表
```cpp
#include <iostream>

typedef void(*func_t)(void);

using namespace std;
/*!
 * \class Father
 *
 * \brief Father类
 *
 * \author Zachariah
 * \date 2024年11月9日 22:07:33
 */
class Father {
public:
	/*!
	*@brief  父类的虚函数
	*@author Zachariah
	*@date   2024年11月9日 22:12:51
	*@param[in]  void  
	*@return     void  
	*/
	virtual void func1() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; }
	virtual void func2() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; }
	virtual void func3() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; }
	void func4() { cout << "非虚函数：" << __FUNCTION__ << "被调用" << endl; };
public:
	int x = 1;
	int y = 2;
	static int a;
};
int Father::a = 10;

/*!
 * \class Mother
 *
 * \brief Mother类
 *
 * \author Zachariah
 * \date 2024年11月9日 22:13:29
 */
class Mother {
public:
	/*!
	*@brief  父类的虚函数
	*@author Zachariah
	*@date   2024年11月9日 22:13:53
	*@param[out] 
	*@param[in]   void  
	*@return     void  
	*/
	virtual void handle1() { cout << "虚函数：" << __FUNCTION__ << "被调用！" << endl; }
	virtual void handle2() { cout << "虚函数：" << __FUNCTION__ << "被调用！" << endl; }
	virtual void handle3() { cout << "虚函数：" << __FUNCTION__ << "被调用！" << endl; }

public:
	int x = 5;
	int y = 6;

};

class Son : public Father, public Mother {
public:
	void func1() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; };
	void handle1() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; }
	virtual void func5() { cout << "虚函数：" << __FUNCTION__ << "被调用" << endl; };
};


int main() {
	//不使用面向对象封装的方式访问对象内的属性，使用地址的方式打破封装
	Son son;
	int* vptr = (int*)*(int*)&son;//父类(Father)拷贝过来的虚函数表(双重地址转换)
	cout << "虚函数表地址：0x" << vptr << endl;
	//使用虚函数指针调用虚函数
	((func_t)* (vptr + 0))();
	((func_t)* (vptr + 1))();
	((func_t)* (vptr + 2))();
	((func_t)* (vptr + 3))();
	//问题：用类似上述的方式调用从Father类继承的func4方法
	
	//使用地址的方式访问对象属性
	cout << *(int*)((int)&son + 4) << endl;
	cout << *(int*)((int)&son + 8) << endl;
	int *vptr2 = (int*)*((int*)&son + 3);//父类(Mother)拷贝过来的虚函数表(双重地址转换)
	//使用虚函数指针调用虚函数
	((func_t)*(vptr2 + 0))();
	((func_t)*(vptr2 + 1))();
	((func_t)*(vptr2 + 2))();
	//使用地址的方式访问对象属性
	cout << *(int*)((int)&son + 16) << endl;
	cout << *(int*)((int)&son + 20) << endl;

	return 0;
}
```

![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1731205255025-55538735-31ef-4f56-95a6-690e98ac124d.png)

```cpp
1>class Son	size(24):
1>	+---
1> 0	| +--- (base class Father)
1> 0	| | {vfptr}
1> 4	| | x
1> 8	| | y
1>	| +---
1>12	| +--- (base class Mother)
1>12	| | {vfptr}
1>16	| | x
1>20	| | y
1>	| +---
1>	+---
1>Son::$vftable@Father@:
1>	| &Son_meta
1>	|  0
1> 0	| &Son::func1
1> 1	| &Father::func2
1> 2	| &Father::func3
1> 3	| &Son::func5
1>Son::$vftable@Mother@:
1>	| -12
1> 0	| &Son::handle1
1> 1	| &Mother::handle2
1> 2	| &Mother::handle3
1>Son::func1 this adjustor: 0
1>Son::handle1 this adjustor: 12
1>Son::func5 this adjustor: 0
```

#### 2.23.4.final
```cpp
class XiaoMi {
public:
	XiaoMi(){}
};

class XiaoMi2 final : public XiaoMi  {
	XiaoMi2(){}
};

class XiaoMi3 : public XiaoMi2 {  //不能把XiaoMi2作为基类

};
```

```cpp
class XiaoMi {
public:
	virtual void func() final;
};

void XiaoMi::func() { //不需要再写final
	cout << "XiaoMi::func" << endl; 
}

class XiaoMi2 : public XiaoMi  {
public:
	void func() {}; // 错误！不能重写func函数
};
```

#### 2.23.5.override
override仅能用于修饰虚函数。

作用：

1. 提示程序的阅读者，这个函数是重写父类的功能。

2. 防止程序员在重写父类的函数时，把函数名写错。

```cpp
#include <iostream>
using namespace std;

class XiaoMi {
public:
	virtual void func() { cout << "XiaoMi::func" << endl; };
};

class XiaoMi2 : public XiaoMi  {
public:
	void func() override {}
	//void func() override;  告诉程序员func是重写父类的虚函数
	//void func1() override{} 错误！因为父类没有func1这个虚函数
};

int main(void) {
	XiaoMi2 xiaomi;
	return 0;
}
```

override只需在函数声明中使用，不需要在函数的实现中使用。

#### 2.23.6.消失的子类析构函数


```cpp
#include <iostream>
#include <Windows.h>
#include <string.h>

using namespace std;

class Father {
public:
	Father(const char* addr ="中国"){
		cout << "执行了Father的构造函数" << endl;
		int len = strlen(addr) + 1;
		this->addr = new char[len];
		strcpy_s(this->addr, len, addr);
	}

	// 把Father类的析构函数定义为virtual函数时，
	// 如果对 Father类的指针使用delete操作时，
	// 就会对该指针使用“动态析构”：
	// 如果这个指针，指向的是子类对象，
	// 那么会先调用该子类的析构函数，再调用自己类的析构函数
	virtual ~Father(){
		cout << "执行了Father的析构函数" << endl;
		if (addr) {
			delete addr;
			addr = NULL;
		}
	}
private:
	char* addr;
};

class Son :public Father {
public:
	Son(const char *game="吃鸡", const char *addr="中国")
		:Father(addr){
		cout << "执行了Son的构造函数" << endl;
		int len = strlen(game) + 1;
		this->game = new char[len];
		strcpy_s(this->game, len, game);
	}
	~Son(){
		cout << "执行了Son的析构函数" << endl;
		if (game) {
			delete game;
			game = NULL;
		}
	}
private:
	char* game;
};

int main(void) {
	cout << "----- case 1 -----" << endl;
	Father* father = new Father();
	delete father;

	cout << "----- case 2 -----" << endl;
	Son* son = new Son();
	delete son;

	cout << "----- case 3 -----" << endl;
	father = new Son();
	delete father;

	system("pause");
	return 0;
}
```

【注意】

<font style="color:rgb(255,0,0);background-color:rgb(255,255,0);">为了防止内存泄露，最好是在基类析构函数上添加virtual关键字，使基类析构函数为虚函数</font>

目的在于，当使用delete释放基类指针时，会实现动态的析构：

如果基类指针指向的是基类对象，那么只调用基类的析构函数

如果基类指针指向的是子类对象，那么先调用子类的析构函数，再调用父类的析构函数

#### 2.23.7.纯虚函数与抽象类
什么时候使用纯虚函数？

某些类，在现实角度和项目实现角度，都不需要实例化（不需要创建它的对象），

这个类中定义的某些成员函数，只是为了提供一个形式上的接口，准备让子类来做具体的实现。

此时，这个方法，就可以定义为“纯虚函数”， 包含纯虚函数的类，就称为抽象类。

```cpp
class Shape {
public:
	Shape(const string& color = "white") { this->color = color; }
	virtual float area() = 0; //不用做具体的实现
	string getColor() { return color; }
private:
	string color;
};

class Circle : public Shape {
public:
	Circle(float radius = 0, const string& color="White")
		:Shape(color), r(radius){}
	float area();
private:
	float r; //半径
};


float Circle::area() {
	return 3.14 * r * r;
}

int main() {
	//使用抽象类创建对象非法！
	//Shape s;  

	Circle c1(10);
	cout << c1.area() << endl;

	Shape* p = &c1;
	cout << p->area() << endl;

	system("pause");
	return 0;
}
```

注意事项：

父类声明某纯虚函数后，

那么它的子类，

1）要么实现这个纯虚函数（最常见）

2）要么继续把这个纯虚函数声明为纯虚函数，这个子类也成为抽象类

3）要么不对这个纯虚函数做任何处理，等效于上一种情况（该方式不推荐）

### 2.24.函数模板
```cpp
//template 关键字告诉C++编译器 我要开始泛型编程了,请你不要随意报错
//T - 参数化数据类型
template <typename T>
T compare(T a, T b) {
	return a > b ? a : b;
}
int main() {
	cout << compare(1, 2) << endl;//自动推导(隐式调用)
	cout << compare<int>(1, 2) << endl;//显式调用
	cout << compare(3.14, 2.2) << endl;
	cout << compare('a', 'b') << endl;
	return 0;
}
```

<font style="color:rgb(255,0,0);">所谓函数模板，实际上是建立一个通用函数，其函数类型和形参类型不具体指定，用一个虚拟的类型来代表。这个通用函数就称为函数模板。</font>凡是函数体相同的函数都可以用这个模板来代替，不必定义多个函数，只需在模板中定义一次即可。在调用函数时系统会根据实参的类型来取代模板中的虚拟类型，从而实现了不同函数的功能。

#### 2.24.1.模板函数
![](https://cdn.nlark.com/yuque/0/2024/png/27614469/1731247496236-5faada79-d349-46ce-89e2-b9e0954113df.png)

