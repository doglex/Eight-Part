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
+ 单例模式主要是为了避免(多个线程)因为创建了多个实例造成资源的浪费，且多个实例由于多次调用容易导致结果出现错误，而使用单例模式能够保证整个应用中有且只有一个实例。
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
+ 懒汉式(线程安全): 按需创建，创建时加锁
```
public class Singleton7 {
	private static Singleton instance=null;
	public static Singleton getInstance() {
		if (instance == null) {
			synchronized (Singleton.class) {
				instance = new Singleton();
			}
		}
		return instance;
	}
}
```
