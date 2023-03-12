## 权限

- public 公共可见
- private 自己可见(即，封装起来setter、getter)
- protected 自己、子孙（有些子孙不是本包的）、本包可见
- 默认 本包可见

## 内存的栈区和堆区

- 栈区(Stack)：一般速度快，线程内使用(线程独立)。当前方法，局部变量，基本类型。
- 堆区(Heap): 比较慢（跨线程的）。有常量池，方法代码，静态方法，对象。
> 静态变量是线程不安全，因为是跨实例共享的

## 优点

+ 简单性：是C++语法的"纯净版"，没有头文件、指针、结构、联合、操作符重载、虚基类，文件小（小型机）
+ 面向对象：重点在对象（数据），用接口(interface)取代多重继承，是没有很麻烦的多重继承的，运行时自省。

> 但是也只能写面向对象代码，除非用groovy或者kotlin等可以写脚本

+ 健壮性：消除重写内存和损坏数据可能性。

> 但是有各种OOM要处理，或者要预先配好jvm的内存占用。高版本似乎改善了

+ 体系中立：字节码，带来了可移植性(int 固定为4字节)，所有数值类型占有字节与平台无关
+ 即时编译(早期java是解释型)
+ 高性能：由编译器消除函数调用（即“内联”）
+ 动态性（但实际是静态语言）

## Hello World

1.JDK，开发工具；JRE，运行环境

2.环境变量：JAVA_HOME=.../bin;   PATH = $JAVA_HOME:$PATH

3.编译运行

```
javac Welcome.java   //生成.class文件（字节码）
java Welcome         //不要.class后缀
```

4 .源代码的文件名必须与公共类的名字相同，并用.java后缀

> kotlin似乎不用

5.一个文件只能一个公共类

> 若有多个公共类，编译成class时就混乱了。可以想像js的export default，形成默认的暴露

6.每个Java程序必须有一个main方法，而且是public static的

> idea快捷键 psvm

7.代码块{}

```java
public class ClassName{
	public static void main(String [] args){
    	program statements;}
}
```

8.所有函数都属于某个类，main必须有一个外壳类

9.println换行，print不换行

> idea快捷键 sout

10.注释： //行注释，/*...*/块注释，/\*\*.../文档字符串注释

## 八种基本类型(primitive type)

1.四种整型

+ int
+ short
+ long
+ byte

```
长整型后缀L或l
十六进制前缀0x或0X
八进制前缀0
二进制前缀0B或0b
允许下划线 (_000_000) 区分
原码(0正1负，符号位;)
反码(正不变；负符号位不变，其余反)，存储在计算机内部
补码(正不变；负的反码加一)
```

2.两种浮点型

+ float，后缀f或F
+ double，后缀d或D //默认是double型

```
三个特殊值Double.POSITIVE_INFINITY,Double.NEGATIVE_INFINITY,Double.NaN (0/0,负数平方根，判断用Double.isNaN)
```

3.char类型用单引号'A',unicode编码，占两个字节（C++中一个字节）
从 \u0000到\uffff，要当心不要在注释中出现\u
**不要在程序中使用char类型，除非确实需要处理UTF-16单元，最好用String抽象数据类型**

4.boolean类型：false和true。安全性，不能与整型隐式转换(C++中是可以)

## 变量

1.逐一声明变量可以提高可读性

2.初始化（使用前必须初始化,更安全，explicti is better than implicit）

```
int a = 12; //栈区不须new
int a; a = 12;
Map <String, Integer> map = new HashMap<>(); //堆区，非基本类型，对象。须new来实例化，模板里需要是非基本类型
```

3.常量：用final表示只能被赋值一次
一般设大写，一般是static final，让类内共用，外部再加public

> static静态（共用）：一开始就初始化，类拥有，不是对象拥有。static方法只能调static属性或者方法，因为非static是实例化后的对象所拥有的。（有特例，比如String，可以调）

4.运算符

+ 地板除（整数除）
+ 除零异常，浮点数除0得无穷大或者NaN
+ 异或A^B^A = B, A^A =0

5.数学函数与异常
在Math类中。要严格的话用StrictMath。

6.类型转换(cast)

+ 小到大自动转换（仅在运算到时）
+ 强制类型转换（int）(Math.round(x))
+ 布尔类型转换请用三元运算  ?:

7.结合运算符 +=,*=

8.自增，自减 ++i，i++,--i,i--(先后使用i)

> 请确保i是可修改的。建议不要使用，以免产生疑惑，提高代码可读性

9.逻辑
==, equals（字符串，浮点，isNaN）,> ,>= ,&& ,|| ,?:
== 是内存地址判断，equals由自定义类的equals方法，同时修改hashcode方法

> 请特别注意equals，因为python中字符串比较用==即可

10.运算符优先级

> 建议始终使用括号，不要在脑中想半天哪个左边优先级高还是右边。提高代码可读性

## 字符串String类型

+ 是final的，不可被继承
+ 不是Primitive Type，内容在常量区(属于堆区),引用在栈区。

> 优点：编译器可以让字符串共享。(回收是自动的，有强引用，弱引用，软引用，虚引用).设计者认为共享带来的高效率远胜于提取、拼接字符串带来的低效率

+ 用双引号可以不用ne
+ 子串substring(start,end+1)创建了一个新串
+ 支持+号。将其他类型与字符串拼接，自动变字符串(或者Autoboxing后有toString方法)。常用于输出语句。
  System.out.println(1+2+"hello"+3+4); 输出3hello34（加号的结合性）
+ 多个拼接用String.join

> String.join(",","S","M","L")  //用逗号拼接

> python中用",".join(...)其中是可迭代内容就行，但是要求各元素是str

+ 单线程频繁拼接用StringBuilder

> StringBuilder -> append -> toString

+ 需要多线程的拼接用StringBuffer，是线程安全的

> StringBuffer 线程安全，但是慢

+ 检测字符串相等用equals。需要非常注意，好在IDE会提示
+ 空串与null串

```
空串检测：  str.length() == 0 或 str.equals("")
null串：   str ！= null && str.length() !=0   //在null上调用方法会出错
```

> 这个很麻烦，在kotlin上有改进

+ 码点CodePoint，用途是对一些特殊字符方便处理
+ 是UTF-16的，而不是UTF-8的
+ 需注意split、replace等函数的参数，可能要多传-1才行
+ 其他API：charAt,compareTo,equals,startswith,indexOf,lastindexof,replace,substring,tolowerCase,trim(删除头尾空格)(python中有strip)
  split(re)方法传入“”将不能去掉连续空格，而python中的split()不传参可去掉连续空格

## 输入输出

```
(1)读取输入
Scanner in = new Scanner(System.in)  //（可以获取标准输入或者shell重定向）import java.util.Scanner
//nextline,next,nextInt,nextDouble,hasNext
//密码输入用Console类
(2)格式化输出
System.out.printf("%8.2f",x); //8宽（用空格填充），2个小数点
System.out.printf(",.2f",x); //千位用逗号
//s总是可以格式化，优先用Formattable接口的formatTo方法。不行，再用toString方法。
(3)文件输入
Scanner in = new Scanner(Paths.get("myfile.txt"),"UTF-8");
//不要直接传"myfile.txt"，会当作字符串输入处理
//要记得用close关闭流
(4)文件输出
PrintWriter out = new PrintWriter("myfile.txt","UTF-8") ;
//out,print,printf,println
//要记得catch 上 IOException
```

## 控制流程

```
//没有goto，但是可以用break或者continue可以跳到指定label:{}
(1)块作用域
不能嵌套在两个块中声明同名变量，但可修改
变量声明后不可修改类型(python是可以的)
(2)条件语句
if(){;}else if {;} else{;}
//一定带括号，不能像python一样无括号
(3)循环
while(true){;}
do{;}while{;} //保证至少执行一次
(4)确定循环
for (int i =0 ; i 小于 x.length(); i++){;} 
//初始化，计数器执行前检查，更新计算器
//在外部无法使用
//若需要外部继续使用，应先在外部定义
//for循环只是while循环简化形式
(5)多重选择Switch（相当于小型map）
Switch(choice){
case 1:...;break;
case 2:...;break;
default:...;break;}
//要注意break，不然会穿透
//标签可以char，byte，short，int，enum，String
break 加标签可跳出多层循环/或任何块
continue 加标签，跳到块首
return 直接返回
> 书中建议尽量不用break，continue进行编程
```

## 其他类型

+ 大数值: BigInteger,BigDecimal //import java.math，有add、multiply函数
+ 数组 （[]与Array）

```
(1)int [] a = new int [100] 
//索引0-index，用[]访问 (堆上创建、栈上引用)
//固定大小，扩容须拷贝到另一个数组（0.75倍增长）
// 必须是同一类型元素，而python中则可以多种元素
(2)foreach 迭代（循环）
for(int e:a){;}  //必须是数组或实现了Iterable 接口的对象
(3)初始化
int [] small = {1,2,3,4}; //隐藏了new
重新初始化 small = new int [] {1,4}  //因为还是数组指针
//允许长度为0，与null不同
(4)数组拷贝
允许重复引用  int [] a = b;
深拷贝  int copied = Arrays.copyOf(lucky,lucky,length)
(5)命令行参数
psvm(String [] args)
(6)数组排序
Arrays.sort() 
集合类型有 Collections.sort()
//Arrays具体，Array抽象
//基本类型，三路快排/双基快排，小的部分用插入；对象类型，用归并排序。
//python中有sorted，带key的sortedd
(7)Arrays API：toString,copyOf,copyOfRange,binarySearch,fill(填充某个值),equals(比较数组各元素)
(8)多维数组
int  [][] square = new int [][] {{1,2,3,4},{1,2,3,4},{1,2,3,4},{1,2,3,4},{1,2,3,4}} ;
或者 int  [][] square =  {{1,2,3,4},{1,2,3,4},{1,2,3,4},{1,2,3,4},{1,2,3,4}} ;//用[][]访问
//axis=0，先行后列
for (double [] row = a ) for (double value:row){;}
//实际上是一维数组的
//不等长数组
int [][] odds = new int [NMAX][];
for (int n=0; n小于 NMAX;n++) odds [n] = new int[n+1];
```

## OOP 面向对象

Object Oriented Programming

### OOP三个特点

+ 封装：用private，暴露setter、getter等公开方法
+ 继承：可以使用父类的好东西
+ 多态：在不同子类可以有不同实现。比如用父类指针来引用子类对象

> 只要对象能满足要求，就不关心其功能的具体实现。

> 将数据放在第一位，操作第二位

### 什么是类/对象

+ 实例化(instance)：类构造(construct)对象的过程
+ 封装/数据隐藏(encapsulation)：将数据（对象的实例域 instance field）与方法（method）组合在某个包中，关键是不让类中方法直接访问其他类的数据。
+ 继承（inheritance）:通过扩展一个类来建立另一个类。原始类是Obeject
+ 对象的行为（behavior）：可以对对象施加哪些操作
+ 对象的状态（state）:操作时，对象如何响应
+ 对象的标识(identity):区分相同行为与状态的对象。考虑hashcode方法
+ 设计类：先设计类，再添加方法
+ 类间关系：依赖(uses-a),聚合(has-a),继承(is-a)
+ UML图（Unified Modeling Language，统一建模语言）

### 标准库中的类

+ 使用构造器(constructor)来构造新的实例，new Date().
  //在Java中，任何对象变量的值是对存储在另外一个地方的一个对象的引用（堆上创建，栈上引用）
  //所有对象在堆中
  //将一个方法用于null会运行时错误
+ 2.Local Date类
  UTC纪元：1970年1月1号 00:00:00
  Date：表时间点
  LocalDate：日历表示
  用静态方法构造(factory method)实例
  LocalDate.of(1999,12,31) //有getYear,getDay等方法
+ 3.更改器(setter,mutator method)方法修改实例域状态，访问器(getter, access method)获取值

### 用户自定义的类

```
//完整的程序若干类，一个类中有main
1.多个源文件使用
javac Employee*.java  //编译多个
或 javac EmployeeTest.java //只须主入口，相当于自动make
2.private使实例域/方法仅被自身使用
3.构造器
(1)与类同名
(2)可以多个构造器，参数不同
(3)没返回值
(4)总是伴随new操作调用
(5)不要在构造器中定义与实例域重名的局部变量
(6)一般是public，也有不是的
4.隐藏参数（对象指针this）
(1)总是有隐式参数this指向对象本身//相当于python中的self
(2)static方法没有this，因为对象还不存在(pyspark中的sc要求@staticmethod)
(3)所有方法必须在类内部定义，但是未必内联，因JVM决定
5.封装的优点
(1)改变类的内部实现，除了该类的方法外，不影响其他代码
（比如返回值类型可以固定，不像python随意）
(2)必须通过更改器修改，可执行类型检查
//若需要返回一个可变数据域的拷贝，应该进行clone
6.访问权限
私有方法（一般是辅助方法helper）不对外暴露，不应成为公共接口，也减少IDE的提示呀
7.Final 实例域
一次初始化。防止被人修改。//然而其他语言编写的Native Method是可以突破final的

```

### 静态static(共用)
```
1.静态域(也类域)
类拥有，而每个对象有一个引用拷贝(共享)，不属于任何对象。比如Dog类，白狗、黑狗，用一个static int n_dog来维护狗群大小。
2.静态常量（不用private，本身大家共用）
public static final double PI = 3.14;
3.静态方法
(1)第一次加载(ClassLoader)时初始化
(2)所有参数显式(没有this)，因为还没有实例化，不能向对象操作
(3)不能访问非静态方法，因为非静态方法有this参数，属于对象拥有
4.工厂方法(factory method)来生成新对象
LocalDate.now,LocalDate.of
原因：无法命名构造器；或者当使用构造器时，无法改变所构造的对象类型
5.main方法，也是静态方法。但是可以操作String
```

### 方法参数
```
1.值调用：方法接收的是调用者提供的值
2.引用调用：方法接收的是调查者提供的变量地址
3.Java是值调用(对象引用的时候是拷贝了一个引用，操作后删除拷贝)
> C++两种调用都有
> 事实上写代码测试一下能运行就行，不用想这么复杂
```

### 对象构造方法
```
1.重载（overload）：同名方法，返回值类型相同，参数不同
//返回类型不是方法签名(signature)一部分，必须相同
//像python不能保证防返回值类型，没有重载，但是有默认参数（靠后）
//重载不止适用于构造器(Constructor)
2.默认参数：Java不支持默认参数，但是可以通过重载来实现该功能
3.无参构造器：使用默认提供的构造器是危险的，其行为不确定
4.用this()可以调用另一个构造器，节省书写
5.初始化块：
类中有一块{},每一次被初始化会执行，不常见不必需
6.对象析构：Java不支持析构器，有finalize方法，但不知何时被调用。有关闭钩Runtime.addShutdownHook.
```

### 包 Package
> 类似using namespace，一般域名逆序，文件夹组织
```
1.类的导入
(1)一个类可以使用所属包中的所有类，及其他包中的共有类
(2)直接包全名访问，或import语句
(3)import java.util.* 没负面影响（和python不一样），但是不大明确，而且可以命名冲突
2.静态导入
import static java.lang.System，可以直接使用静态方法和静态域了。
3.将类放入包中
(1)将包名放在源文件开头
package com.hostmann.corejava ; //禁止用户用java.开头的包名
//没有这行的话是一个default package
```

### 类路径 CLASS_PATH
+ JAR包用ZIP格式组织
+ classpath不允许通配符*以防止shell命令进一步扩展
+ 采用参数 -classpath,-cp来运行
> 不建议用CLASSPATH固定的环境变量或防止jre/lib/ext下。

> 理解为环境变量env，或者PYTHON_PATH


### 文档注释
```
1.插入/\*\*....\*/，可嵌入html代码
2.类注释
3.方法注释  @param　@return @throws
4.静态常量须注释
5.通用注释 @version @author @deprecated  @see
6.包注释： 提供package.html 或 package-info.java
7.注释抽取，用javadoc工具
```
> 注释的同时方便形成文档

### 十、类实践技巧
+ 保证数据私有
+ 一定要对数据初始化
+ 不要在类中使用过多基本类型。数量过多，使用另一个类。
+ 不是所有域都需要访问器和更改器。
+ 责任过多的类进行分解（解耦，高内聚、低耦合）
+ 类名和方法名要体现他们的职责
+ DRY：不要老是重新造轮子
> 一个类一个职责

> 一个职责不要分解到搞多个类，那样可能Over Design

## 继承 Inheritance
+ 继承（Inheritance）于已存在的类创建一个新类。
+ 反射（Reflection序运行期间发现更多的类及其属性的能力。
> python有type、inspect等反射功能

### 超类(父类，superclass)、子类(subclass)
1.extends 定义子类
+ 扩展超类的时候，仅需要指出子类和超类的不同之处。
+ 通用的方法在超类中，特殊用途的方法在子类中。

2.覆盖(Override)
+ 子类要重新设计某些方法

3.子类构造器
public B(a,b) {super(a);this.b=b;}
+ this两个用途：隐式参数，调用类的其他构造器。如python的self调用
+ super两个用途：调用超类方法，调用超类构造器。如super()

4.多态：一个变量(使用一个超类或者接口的应用)可以指示多种实际类型，不同的行为。
比如 Map map = new HashMap(); Map map = new TreeMap(); 
这样调用map.get()时遇到不同类型行为不同。
+　调用哪个方法是动态绑定的（dynamic binding）。
+　置换法则：任何地方可以用子类的置换。除非声明final、static、private进行静态绑定。
>　C++中要做动态绑定就需要声明为虚方法

5.继承层次：
+ 多个继承链 
+ Java不支持多重继承，但有接口，来代替

6.调用过程
+ JVM为继承链创建方法表
+ JVM搜索方法相应的类，知道调用哪个(动态绑定或者静态绑定)
+ 调用方法
> 反正是交给jvm去解释

7.阻止继承：final 类和final方法。确保子类不会改变语义。比如String是final的。
> 没有被覆盖的代码且简短，可以被JVM用内联(inling)处理，内联以提高运行效率

8.类型转换
+ 进行转换的唯一原因：在暂时忽略对象的实际类型后，使用对象的全部功能
+ 只能在继承层次内进行类型转换
+ 超类转子类前进行instaceof检查

9.抽象类
```
(1)abstract
为了提高程序的清晰度，包含一个或多个抽象方法的类，本身必须声明为抽象。
(2)除抽象方法外，抽象类还可以包含具体的数据和方法。
(3)通用的域和方法放在超类中
(4)抽象方法充当着占位的角色，具体实现在子类中
(5)抽象类不能被实例化。因为没有实现抽象方法，实例化了再调用这个方法会出问题。
//为什么要声明抽象方法，因为这样可以从父类引用来调用。
```
> 就是带属性的接口？在高版本中区别不大了。

10.受保护访问
- private 仅本类可见
- public 所有类可见
- 默认无修饰符 对本包可见
- protected 对本包和所有子类可见
> Java中protected安全性比C++差

> 慎用protected属性。因为对这个类的实现修改，就必须通知所有使用这个类的程序员。违背了OOP数据封装性。

### Object类，所有类的超类
```
1.在Java中，只有基本类型不是对象，其他都是。
2.equals方法
(1)getClass()返回一个对象所属的类，不相同的不能进行比较。
(2)equals方法应该具有的特性
- 自反性：x.equals(x)返回true
- 对称性: x.equals(y) 则 y.equals(x)
- 传递性：x.equals(y)，y.equals(x) 则x.equals(z)
- 一致性：反复调用结果应该相同
- 对于任意非空引用x，x.equals(null)应该是false

(3).备注
- 若子类能够拥有自己的相等概念，则对称性需求将强制采用getClass检测
- 若由超类决定相等的概念，那么就可以用instanceof进行检测，这样可以在不同子类对象之间进行相等的比较
- 对于数组类型的域，可以使用静态的Arrays.equals方法检测数组元素是否相等

3.hashCode方法
(1)散列码是由对象导出的一个整型值，默认的散列码是对象的存储地址
(2)Equal是与hashCode定义一致
4.toString方法
(1)getClass().getName()获得类名的字符串
(2)只要用+号与String相连，就会调用toString方法
> python中有__str__()可读性好，__repr__()执行好
(3)print会自动调用toSring
(4)数组打印会得到一堆字符乱码，应该改用Arrays.toString(luckynums)
```