参考 https://www.zhihu.com/question/47449512/answer/106034220
## 注解的格式
```
public @interface 注解名称{
    属性(方法)列表;
}
```
+ 本质是接口interface，默认继承于Annotation类(java.lang.annotation.Annotation)
+ 因为接口默认就是 public abstract，因此属性列表中不用写这个
+ 使用位置：在类、方法、成员变量、形参
+ 分类：JDK内置注解、第三方框架提供的注解、自定义注解
+ 作用：告诉代码各个地方做什么，也是注释给程序员看的
+ 可以给getValue属性赋值(getValue是以方法表示的属性)


## 例子
> 可以通过反射获取注解的属性列表的内容
``` java 
// MyAnnotation.java 
package x;
public @interface MyAnnotation {
    String getValue() default "";
}

// UseMyAnnotation.java
package x;

@MyAnnotation(getValue = "anno on class")
public class UseMyAnnotation {

    @MyAnnotation(getValue = "anno on field")
    public String name;

    @MyAnnotation(getValue = "anno on method")
    public void hello(){}

    @MyAnnotation() // 自动填入默认值
    public void defaultMethod(){}

}

// ReadAnnotation.java
package x;

import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ReadAnnotation {
    public static void main(String[] args) throws Exception {
        Class <UseMyAnnotation> clazz = UseMyAnnotation.class;
        MyAnnotation annotationOnClass = clazz.getAnnotation(MyAnnotation.class);
        System.out.println(annotationOnClass.getValue());

        Field name = clazz.getField("name");
        MyAnnotation annotationOnField = name.getAnnotation(MyAnnotation.class);
        System.out.println(annotationOnField.getValue());

        Method hello = clazz.getMethod("hello");
        MyAnnotation annotationOnMethod = hello.getAnnotation(MyAnnotation.class);
        System.out.println(annotationOnMethod.getValue());

        Method defaultMethod = clazz.getMethod("defaultMethod");
        MyAnnotation annotationOnDefaultMethod = defaultMethod.getAnnotation(MyAnnotation.class);
        System.out.println(annotationOnDefaultMethod.getValue());
    }
}
```

## 元注解
+ 加在注解上的注解
+ @Documented 用于制作文档，没什么用
+ @Target 限定该注解的使用位置（如@Target({ElementType.TYPE, ElementType.FIELD,ElementType.METHOD})），不写则没有限定
+ @Retention 注解的保留策略，RetentionPolicy.RUNTIME(保留到运行时)、RetentionPolicy.CLASS 保留到字节码磁盘文件、RetentionPolicy.SOURCE 保留到源代码
> 反射读不到注解内容时，应该使用@Retention(RetentionPolicy.RUNTIME)