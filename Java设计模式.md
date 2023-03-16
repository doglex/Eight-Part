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
``` java
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
### 工厂方法模式
+ 定义一个创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法模式把类实例化的过程推迟到子类。
``` java 
package com.zhy.pattern.factory.b;
 
public abstract class RoujiaMoStore
{
	public abstract RouJiaMo createRouJiaMo(String type);
	public RouJiaMo sellRouJiaMo(String type)
	{
		RouJiaMo rouJiaMo = createRouJiaMo(type);
		rouJiaMo.prepare();
		rouJiaMo.fire();
		rouJiaMo.pack();
		return rouJiaMo;
	}
 
}


public class XianRouJiaMoStore extends RoujiaMoStore
{
	@Override
	public RouJiaMo createRouJiaMo(String type)
	{
		RouJiaMo rouJiaMo = null;
		if (type.equals("Suan"))
		{
			rouJiaMo = new XianSuanRouJiaMo();
		} else if (type.equals("Tian"))
		{
			rouJiaMo = new XianTianRouJiaMo();
		} else if (type.equals("La"))
		{
			rouJiaMo = new XianLaRouJiaMo();
		}
		return rouJiaMo;
	}
}
```

## 抽象工厂模式
+ 提供一个接口，用于创建相关的或依赖对象的家族，而不需要明确指定具体类。
``` java 
package com.zhy.pattern.factory.b;
public interface RouJiaMoYLFactroy
{
	public Meat createMeat();
	public YuanLiao createYuanliao();
}

public class XianRouJiaMoYLFactroy implements RouJiaMoYLFactroy
{
 
	@Override
	public Meat createMeat()
	{
		return new FreshMest();
	}

	@Override
	public YuanLiao createYuanliao()
	{
		return new XianTeSeYuanliao();
	}
}
```

## 建造者模式
+ 对象需要由多个组件组装起来,就搞个Builder
``` java 
public class Computer {
    private String screen;
    private String mouse;
    private String cpu;
}

public abstract class Builder {
    abstract Builder buildScreen(String screen);
    abstract Builder buildMouse(String mouse);
    abstract Builder buildCpu(String cpu);
    abstract Computer build();
}

public class LenovoBuilder extends Builder {
    private Computer computer = new Computer();
    Builder buildScreen(String screen) {
        computer.setScreen(screen);
        return this;
    }
    Computer build() {
        System.out.println("构建中...");
        return computer;
    }
}
```