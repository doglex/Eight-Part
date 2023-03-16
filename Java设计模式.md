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

## 原型模式（克隆模式）
+ 原型模式是用于高性能创建重复的对象
``` java 
class Diploma implements Cloneable{
	String name; //姓名
	String term; //学期
	String school; //学校
	String month; //颁发月份
	String date; //颁发日期
	
	public Diploma(String name,String term,String school,String month,String date){
		this.name = name;
		this.term = term;
		this.school = school;
		this.month = month;
		this.date = date;
	}
	protected Object clone() throws CloneNotSupportedException {
		return (Diploma) super.clone(); // 高性能的关键代码
	}
}
public class prototypeDemo {
	public static void main(String[] args) throws CloneNotSupportedException {
		Diploma dma = new Diploma("张三", "一", "Oracle", "10", "10");
		//原型模式，提供一个clone方法
		Diploma dma2 = (Diploma) dma.clone();
		dma2.setName("李四");
    }
}
```

## 适配器模式
+ 把一个接口转成另一个接口
``` java 

public class Mobile
{
	public void inputPower(V5Power power)
	{
		int provideV5Power = power.provideV5Power();
	}
}
public interface V5Power
{
	public int provideV5Power();
}
public class V220Power
{
	public int provideV220Power()
	{
		return 220 ; 
	}
}

public class V5PowerAdapter implements V5Power
{
	private V220Power v220Power ;
	public V5PowerAdapter(V220Power v220Power)
	{
		this.v220Power = v220Power ;
	}
	public int provideV5Power()
	{
		int power = v220Power.provideV220Power() ;
		System.out.println("适配器：我悄悄的适配了电压。");
		return 5 ; 
	} 
}

public class Test
{
	public static void main(String[] args)
	{
		Mobile mobile = new Mobile();
		V5Power v5Power = new V5PowerAdapter(new V220Power()) ; 
		mobile.inputPower(v5Power);
	}
}
```

## 桥接模式
+ 把抽象与实现解耦
``` java
public interface DrawAPI {  // 抽象
   public void drawCircle(int radius, int x, int y);
}
public abstract class Shape {  // 继续抽象
   protected DrawAPI drawAPI;
   protected Shape(DrawAPI drawAPI){
      this.drawAPI = drawAPI;
   }
   public abstract void draw();  
}
public class Circle extends Shape {  // 实现
   private int x, y, radius;
   public Circle(int x, int y, int radius, DrawAPI drawAPI) {
      super(drawAPI);
      this.x = x;  
      this.y = y;  
      this.radius = radius;
   }
   public void draw() {
      drawAPI.drawCircle(radius,x,y);
   }
}
```

## 装饰模式
+ 动态地将责任附加到对象上。不断的包起来
```
// 定义抽象构件：抽象商品
public interface ItemComponent {
    public double checkoutPrice();
}

// 定义具体构件：具体商品
public class ConcreteItemCompoment implements ItemComponent {
    public double checkoutPrice() {
        return 200.0;
    }
}

// 定义抽象装饰者：创建传参(抽象构件)构造方法，以便给具体构件增加功能
public abstract class ItemAbsatractDecorator implements ItemComponent {
    protected ItemComponent itemComponent;
    public ItemAbsatractDecorator(ItemComponent myItem) {
        this.itemComponent = myItem;
    }
    public double checkoutPrice() {
        return this.itemComponent.checkoutPrice();
    }
}

// 定义具体装饰者A：增加店铺折扣八折
public class ShopDiscountDecorator extends ItemAbsatractDecorator {
    public ShopDiscountDecorator(ItemComponent myItem) {
        super(myItem);
    }
    @Override
    public double checkoutPrice() {
        return 0.8 * super.checkoutPrice();
    }
} 

public class FullReductionDecorator extends ItemAbsatractDecorator {
    public FullReductionDecorator(ItemComponent myItem) {
        super(myItem);
    }
    @Override  
    public double checkoutPrice() {
        return super.checkoutPrice() - 20;  
    }
}

// 定义具体装饰者C：增加百亿补贴券50
public class BybtCouponDecorator extends ItemAbsatractDecorator { 
    public BybtCouponDecorator(ItemComponent myItem) {
        super(myItem);
    }
    
    @Override
    public double checkoutPrice() {
        return super.checkoutPrice() - 50;
    }
}
    
//客户端调用
public class userPayForItem() {
    public static void main(String[] args) {
        ItemCompoment item = new ConcreteItemCompoment();
        System.out.println("宝贝原价：" + item.checkoutPrice() + " 元"）; 
        item = new ShopDiscountDecorator(item);
        System.out.println("使用店铺折扣后需支付：" + item.checkoutPrice() + " 元"）;
        item = new FullReductionDecorator(item);
        System.out.println("使用满200减20后需支付：" + item.checkoutPrice() + " 元"）;
        item = new BybtCouponDecorator(item);
        System.out.println("使用百亿补贴券后需支付：" + item.checkoutPrice() + " 元"）;      
    }
}
```
> 非常的不直观，为什么不设计成一个类下的几个方法呢

## 组合模式(部分整体模式)
+ 把一组相似的对象当作一个单一的对象。就是带有自身对象列表的类,可以层次构造归属关系
```
public class Employee {
   private List<Employee> subordinates;
   public Employee(String name,String dept, int sal) {
      this.name = name;
      this.dept = dept;
      this.salary = sal;
      subordinates = new ArrayList<Employee>();
   }
   public void add(Employee e) {
      subordinates.add(e);
   }
   public List<Employee> getSubordinates(){
     return subordinates;
   } 
}
```

