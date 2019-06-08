---
title: Bean的生命周期
date: 2019-06-08 19:29:12
tags:
---
# Bean的生命周期
Spring Ioc对Bean的生命周期管理如下
- 通过构造函数或者工厂方法创建Bean
- 为Bean注入属性与其他Bean的引用
- 执行初始化方法
- Bean可以使用
- 容器在关闭时执行销毁方法

在xml中配置`init-method`属性执行初始化方法，配置`destroy-method`执行销毁方法,例如:
```
public class User{
    private int name;
    private Date birthday;
    public User(){
        System.out.println("执行无参构造函数");
    }
    public void setName(String name){
        System.out.println("设置name属性");
        this.name = name;
    }
    public String getName(){
        return name;
    }
    public void SetBirthday(Date birthday){
        System.out.println("设置birthday属性");
        this.birtyday = birthday;
    }
    public Date getBirtyday(){
        return birthday;
    }
    public void init(){
        System.out.println("执行初始化方法");
    }
    public void destory(){
        System.out.println("执行销毁方法");
    }
}
```
bean.xml内容如下
```
<bean id="user" class="main.demo.User" init-method="init" destroy-method="destory">
    <property name="name" value="demo">
    <property name="birthday">
        <bean class="java.util.Date"></bean>
</bean>
```
测试程序如下：
```
public class Test{
    public static void main(String[] args){
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
        User user = (User)context.getBean("user");
        System.out.println(user.getName());
        //调用close()时Spring容器才执行销毁方法
        context.close();
    }
}
```
执行结果如下:

![](结果.PNG)

# Bean的后置处理器
Bean的后置处理器允许调用初始化方法前后对Bean进行额外处理，Bean的后置处理器对Bean的所有实例逐一统一处理，而非某一个实例，典型应用就是AOP。
在使用过程中需要实现`BeanPostProcessor`接口，该接口有两个方法：

方法|说明|
---|--|
postProcessBeforeInitialization|在调用初始化方法之前调用
postProcessAfterInitialization|在调用初始化方法之后调用

具体实例如下：
```
public class MyBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + " 执行postProcessBeforeInitialization方法");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + " 执行postProcessAfterInitialization方法");
        return bean;
    }
}
```
bean.xml内容如下：
```
<bean id="user" class="main.entity.User" init-method="init" destroy-method="destory">
    <property name="name" value="demo"></property>
    <property name="birthday">
        <bean class="java.util.Date"></bean>
    </property>
</bean>
<bean class="main.entity.MyBeanPostProcessor"></bean>
```
执行结果如下：
![](后置Bean处理器执行结果.PNG)

可以看到后置处理器对xml中的`Date`和`user`做了处理,并且是在初始化方法前后分别处理