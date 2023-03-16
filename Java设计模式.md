## 设计模式列表
> 参考 https://github.com/youlookwhat/DesignPattern

> 参考 https://www.runoob.com/design-pattern/visitor-pattern.html
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
``` java
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
``` java
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

## 外观模式
+ 提供一个统一的接口，用来访问子系统中的一群接口.提供一键访问的功能
``` java 
public void watchMovie()
	{
		popper.on();
		popper.makePopcorn();
		light.down();
		projector.on();
		projector.open();
		computer.on();
		player.on();
		player.make3DListener();
}
```

## 享元模式
+ 减少创建的对象数量，从而减小内存和提高性能
+ 就是复用那个对象，那个对象可以更改属性后访问
+ 类似于单例模式允许实例多一点，hashmap缓存起来
``` java
public class ShapeFactory {
   private static final HashMap<String, Shape> circleMap = new HashMap<>();
   public static Shape getCircle(String color) {
      Circle circle = (Circle)circleMap.get(color);
      if(circle == null) {
         circle = new Circle(color);
         circleMap.put(color, circle);
         System.out.println("Creating circle of color : " + color);
      }
      return circle;
   }
}
```

## 代理模式
+ 一个类代表另一个类, 职责清晰
``` java 
public interface Image {
   void display();
}

public class RealImage implements Image {
   private String fileName;
   public RealImage(String fileName){
      this.fileName = fileName;
      loadFromDisk(fileName);
   }
   @Override
   public void display() {
      System.out.println("Displaying " + fileName);
   }
   private void loadFromDisk(String fileName){
      System.out.println("Loading " + fileName);
   }
}

public class ProxyImage implements Image{
   private RealImage realImage;
   private String fileName;
   public ProxyImage(String fileName){
      this.fileName = fileName;
   }
   @Override
   public void display() {
      if(realImage == null){
         realImage = new RealImage(fileName);
      }
      realImage.display();
   }
}
```

## 模板方法
+ 可以在不改变子类代码的情况下，重新定义算法的步骤
``` java 
public abstract class Worker
{
	protected String name;
 
	public Worker(String name)
	{
		this.name = name;
	}
 
	public final void workOneDay()  // 这里可以改
	{
		enterCompany();
		computerOn();
		work();
		computerOff();
		exitCompany();
	}
	public abstract void work();
	private void computerOff()
	{
		System.out.println(name + "关闭电脑");
	}
	private void computerOn()
	{
		System.out.println(name + "打开电脑");
	}
 
	public void enterCompany()
	{
		System.out.println(name + "进入公司");
	}
	public void exitCompany()
	{
		System.out.println(name + "离开公司");
	}
 
}
```

## 命令模式
+ 将请求封装成对象，将动作请求者和动作执行者解耦
+ 就是说，用的人只用记住execute这个名字
``` java
public interface Command
{
	public void execute();
}

public class LightOnCommond implements Command
{
	private Light light ; 
	public LightOnCommond(Light light)
	{
		this.light = light;
	}
	@Override
	public void execute()
	{
		light.on();
	}
}

public class ComputerOffCommond implements Command
{
	private Computer computer ; 
	public ComputerOffCommond( Computer computer)
	{
		this.computer = computer;
	}
	@Override
	public void execute()
	{
		computer.off();
	}
}
```

## 迭代器模式
+ 提供一种方法顺序访问一个聚合对象中各个元素, 而又无须暴露该对象的内部表示
``` java
public interface Iterator {
   public boolean hasNext();
   public Object next();
}
public interface Container {
   public Iterator getIterator();
}
public class NameRepository implements Container {
   public String[] names = {"Robert" , "John" ,"Julie" , "Lora"};
 
   @Override
   public Iterator getIterator() {
      return new NameIterator();
   }
 
   private class NameIterator implements Iterator {
      int index;
      @Override
      public boolean hasNext() {
         if(index < names.length){
            return true;
         }
         return false;
      }
      @Override
      public Object next() {
         if(this.hasNext()){
            return names[index++];
         }
         return null;
      }     
   }
}
```

## 观察者模式
+ 对象之间的一对多的依赖，这样一来，当一个对象改变时，它的所有的依赖者都会收到通知并自动更新
``` java 

public interface Subject
{
	public void registerObserver(Observer observer);
	public void removeObserver(Observer observer);
	public void notifyObservers();
}
public interface Observer
{
	public void update(String msg);
}
public class ObjectFor3D implements Subject
{
	private List<Observer> observers = new ArrayList<Observer>();
	private String msg;
 
	@Override
	public void registerObserver(Observer observer)
	{
		observers.add(observer);
	}
 
	@Override
	public void removeObserver(Observer observer)
	{
		int index = observers.indexOf(observer);
		if (index >= 0)
		{
			observers.remove(index);
		}
	}
 
	@Override
	public void notifyObservers()
	{
		for (Observer observer : observers)
		{
			observer.update(msg);
		}
	}
	public void setMsg(String msg)
	{
		this.msg = msg;
		notifyObservers(); // 更新时自动通知观察者
	}
}
```

## 中介者模式
+ 用一个中介对象来封装一系列的对象交互，中介者使各对象不需要显式地相互引用
``` java 
public class ChatRoom {
   public static void showMessage(User user, String message){
      System.out.println(new Date().toString()
         + " [" + user.getName() +"] : " + message);
   }
}
public class User {
   public User(String name){
      this.name  = name;
   }
   public void sendMessage(String message){
      ChatRoom.showMessage(this,message);
   }
}
public class MediatorPatternDemo {
   public static void main(String[] args) {
      User robert = new User("Robert");
      User john = new User("John");
      robert.sendMessage("Hi! John!");
      john.sendMessage("Hello! Robert!");
   }
}
```

## 备忘录模式
+ 在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态
``` java 
public class Memento {
   private String state;
   public Memento(String state){
      this.state = state;
   }
   public String getState(){
      return state;
   }  
}
public class Originator {
   private String state;
   public void setState(String state){
      this.state = state;
   }
   public String getState(){
      return state;
   }
   public Memento saveStateToMemento(){
      return new Memento(state);
   }
   public void getStateFromMemento(Memento Memento){
      state = Memento.getState();
   }
}
public class CareTaker {
   private List<Memento> mementoList = new ArrayList<Memento>();
   public void add(Memento state){
      mementoList.add(state);
   }
   public Memento get(int index){
      return mementoList.get(index);
   }
}
```

## 解释器模式
+ 给定一个语言，定义它的文法表示，并定义一个解释器，这个解释器使用该标识来解释语言中的句子
``` java
public interface Expression {
   public boolean interpret(String context);
}
```

## 状态模式
+ 状态影响行为
+ 类内定义多个状态，方法根据状态调整行为

## 策略模式
+ 定义了算法族，分别封装起来，让它们之间可相互替换，此模式让算法的变化独立于使用算法的客户
+ 把行为类set到类里即可 https://blog.csdn.net/lmj623565791/article/details/24116745

## 责任链模式
+ 将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。
``` java 
public abstract class AbstractLogger {
   public static int INFO = 1;
   public static int DEBUG = 2;
   public static int ERROR = 3;
   protected int level;
   protected AbstractLogger nextLogger; // 最重要的地方
   public void setNextLogger(AbstractLogger nextLogger){
      this.nextLogger = nextLogger;
   }
 
   public void logMessage(int level, String message){
      if(this.level <= level){
         write(message);
      }
      if(nextLogger !=null){
         nextLogger.logMessage(level, message);
      }
   }
   abstract protected void write(String message);
}

private static AbstractLogger getChainOfLoggers(){
      AbstractLogger errorLogger = new ErrorLogger(AbstractLogger.ERROR);
      AbstractLogger fileLogger = new FileLogger(AbstractLogger.DEBUG);
      AbstractLogger consoleLogger = new ConsoleLogger(AbstractLogger.INFO);
 
      errorLogger.setNextLogger(fileLogger); // 连起来
      fileLogger.setNextLogger(consoleLogger); // 连起来
      return errorLogger;  
   }
```

## 访问者模式
+ 将数据结构与数据操作分离。稳定的数据结构和易变的操作耦合问题
``` java 
public interface ComputerPart {
   public void accept(ComputerPartVisitor computerPartVisitor);
}
public class Keyboard  implements ComputerPart {
   public void accept(ComputerPartVisitor computerPartVisitor) {
      computerPartVisitor.visit(this);
   }
}
public class Monitor  implements ComputerPart {
   public void accept(ComputerPartVisitor computerPartVisitor) {
      computerPartVisitor.visit(this);
   }
}
public class Mouse  implements ComputerPart {
   public void accept(ComputerPartVisitor computerPartVisitor) {
      computerPartVisitor.visit(this);
   }
}

public class Computer implements ComputerPart {
   ComputerPart[] parts;
   public Computer(){
      parts = new ComputerPart[] {new Mouse(), new Keyboard(), new Monitor()};      
   } 
   @Override
   public void accept(ComputerPartVisitor computerPartVisitor) {
      for (int i = 0; i < parts.length; i++) {
         parts[i].accept(computerPartVisitor);
      }
      computerPartVisitor.visit(this);
   }
}
public interface ComputerPartVisitor {
   public void visit(Computer computer);
   public void visit(Mouse mouse);
   public void visit(Keyboard keyboard);
   public void visit(Monitor monitor);
}

public class ComputerPartDisplayVisitor implements ComputerPartVisitor {
   @Override
   public void visit(Computer computer) {
      System.out.println("Displaying Computer.");
   }
   @Override
   public void visit(Mouse mouse) {
      System.out.println("Displaying Mouse.");
   }
   @Override
   public void visit(Keyboard keyboard) {
      System.out.println("Displaying Keyboard.");
   }
   @Override
   public void visit(Monitor monitor) {
      System.out.println("Displaying Monitor.");
   }
}
public class VisitorPatternDemo {
   public static void main(String[] args) {
      ComputerPart computer = new Computer();
      computer.accept(new ComputerPartDisplayVisitor());
   }
}
```