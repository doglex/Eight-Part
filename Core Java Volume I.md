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
