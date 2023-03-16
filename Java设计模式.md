## 思考?
+ 不同语言设计模式不同，其实现代码也不同。或者不是OOP的语言
+ 可以期望特定语言在语法层面就隐式的解决，不用显式的编写设计模式
+ 设计模式是较流行的实践

## 设计模式列表
> 参考 https://github.com/youlookwhat/DesignPattern
+ **创建型模式**：单例模式、抽象工厂模式、建造者模式、工厂模式、原型模式
+ **结构型模式**：适配器模式、桥接模式、装饰模式、组合模式、外观模式、享元模式、代理模式
+ **行为型模式**：模版方法模式、命令模式、迭代器模式、观察者模式、中介者模式、备忘录模式、解释器模式、状态模式、策略模式、责任链模式、访问者模式

## 单例模式
+ 减少资源浪费，或者需要全局(多线程)统一的一个实例
+ 饿汉式(线程安全)：一开始就创建到static上，注意static变量是在堆区
``` java
public class Singleton {
	private static Singleton instance=new Singleton();
	private Singleton(){};
	public static Singleton getInstance(){
		return instance;
	}
}
```
+ 懒汉式(线程不安全，不可用)：按需创建，不安全原因是可能两个线程同时创建
```
public class Singleton {
	private static Singleton instance=null;
	private Singleton() {};
	public static Singleton getInstance(){
		if(instance==null){ 
			instance=new Singleton();
		}
		return instance;
	}
}
```
+ 懒汉式(线程安全): 按需创建，创建时加锁。多重判定是否要创建
``` java
public class Singleton {
	private static Singleton instance=null;
	private Singleton() {};
	public static Singleton getInstance(){
		 if (instance == null) {   // 预判定
	          synchronized (Singleton.class) {  
	              if (instance == null) {   // 得锁后重新判定
	            	  instance = new Singleton();  
	              }  
	          }  
	      }  
	      return instance;  
	}
}
```

## 工厂模式
+ 为了创建对象的时候方便一点，或者要统一管理

### 静态工厂模式
+ 传一个字符串，生成对象
``` java 
package com.xiang.staticfactory;  
interface Friut {  
   public void say();  
}  
class Apple implements Friut {  
   public void say() {  
      System.out.println("I am an Apple");  
   }  
}  
class Grape implements Friut {  
   public void say() {  
      System.out.println("I am a Grape");  
   }  
} 
class FriutFactory {  
   public static Friut getFriut(String type) {  
      Friut f = null;  
      try {  
        f = (Friut) Class.forName("com.xiang.staticfactory." + type)  
              .newInstance();  
      } catch (InstantiationException e) {  
        e.printStackTrace();  
      } catch (IllegalAccessException e) {  
        e.printStackTrace();  
      } catch (ClassNotFoundException e) {  
        e.printStackTrace();  
      }  
      return f;  
   }  
}  
```
