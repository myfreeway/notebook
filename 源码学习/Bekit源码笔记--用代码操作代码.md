在编程中，有时会用到注解来控制代码的运行。在观摩bekit源码的过程中，看到大量的这种用法，顺便记录一下。

注解能干什么呢？他算是对类或方法打的一个标记，可以帮助我们用代码的方式找到他们。注解还可以添加属性和值，意味着我们找到某个类的某个注解时，还可以得到注解的属性和值，那么就可以使用他们干很多事情。既然得到了类和方法，就可以用编程的方式动态调用。

这种操作方式可以用来写基础框架，或者业务代码。

这里用到了spring自带的工具类（aop，class）和jdk的反射和Class类。

1. 找到某种特质的实例，放到map里备用。比如某种handler。
```java
//Flow是注解类
String[] beanNames = applicationContext.getBeanNamesForAnnotation(Flow.class);
for (String beanName : beanNames) {
            applicationContext.getBean(beanName);
        }

// applicationContext.getBeansOfType(父类或接口名)也可以
```

2. 找到spring对象实例的本质class，因为有些是aop之后的类，已经变形。
```java
// AopUtils是spring的自带的工具类, flow是个普通类in spring容器
Class<?> flowClass = AopUtils.getTargetClass(flow);
```

3. 找到类的全限定名和bean的默认name。
```java
// ClassUtils是spring的自带的工具类
ClassUtils.getQualifiedName(flowClass);
ClassUtils.getShortNameAsProperty(flowClass);
```

4. 找到类的注解和注解参数值。
```java
// getAnnotation是JDK的自带的方法
Flow flowAnnotation = flowClass.getAnnotation(Flow.class);

// 注解的参数值
flowAnnotation.name();
flowAnnotation.enableFlowTx();

// 注解类的定义
@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface Flow {

    /**
     * 流程名称（默认使用被注解的类名，首字母小写）
     */
    String name() default "";

    /**
     * 是否开启流程事务（默认开启）
     */
    boolean enableFlowTx() default true;

}
```

5. 判断方法是否有某个注解。
```java
// 都是JDK的自带的方法
for (Method method : flowClass.getDeclaredMethods()) {
    method.isAnnotationPresent(StartNode.class);
}
```

6. 找到方法上的某个注解。
```java
// AnnotatedElementUtils是spring自带的工具类
for (Method method : flowClass.getDeclaredMethods()) {
    // 此处得到的@Node是已经经过@AliasFor属性别名进行属性同步后的结果
    Node nodeAnnotation = AnnotatedElementUtils.findMergedAnnotation(method, Node.class);
}
```