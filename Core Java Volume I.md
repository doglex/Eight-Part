## 权限
- public 公共可见
- private 自己可见(即，封装起来setter、getter)
- protected 自己、子孙（有些子孙不是本包的）、本包可见
- 默认 本包可见

## 内存的栈区和堆区
- 栈区(Stack)：一般速度快，线程内使用。当前方法，局部变量，基本类型。
- 堆区(Heap): 比较慢（要保证多线程间安全）。有常量池，方法代码，静态方法，对象。

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
>static静态（共用）：一开始就初始化，类拥有，不是对象拥有。static方法只能调static属性或者方法，因为非static是实例化后的对象所拥有的。（有特例，比如String，可以调）

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



