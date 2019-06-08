---
title: Bean的种类以及作用域
date: 2019-06-08 00:06:28
tags:
---
# Bean 的种类
## 普通Bean
`Person`类
```
public class Person{
    private String name;
    private int age;
    public Person(){
        System.out.println("默认构造函数");
    }
    public Person(String name){
        this.name = name;
    }
    public Person(String name,int age){
        this.name = name;
        this.age = age;
    }
    public vois setName(String name){
        this.name=name;
    }
    public String getName(
        return name;
    )
    public void setAge(int age){
        this.age = age;
    }
    public int getAge(){
        return age;
    }
}
```
bean.xml
```
<bean id="person" class="com.demo.Person"></bean>
```
直接创建Bean的实例并返回
## FactoryBean
是一个特殊的Bean，具有工厂生产Bean的能力，只能生成特定的对象，必须实现`FactoryBean`接口，此接口提供的getObject()用于返回特定的bean。
```
//必须实现FactoryBean接口
public class MyFactoryBean implements FactoryBean {
    //返回bean的实例
    @Override
    public Object getObject() throws Exception {
        return new Person;
    }
    /*返回getObject()的方法创建的bean的类型，如果不知道类型，则返回null，这样可以在不返回bean的情况下检查bean的类型，使用@autowire时会忽略返回null的bean*/
    @Override
    public Class<?> getObjectType() {
        return Person.class;
    }
    /*如果为true,则表明getObject()方法返回的bean是单例并且放入Spring的单例缓冲池，否则是多例*/
    @Override
    public boolean isSingleton() {
        return false;
    }
}
```
# Bean的作用域
类型|说明
---|--|
singleton|在Spring Ioc容器中仅存在一个实例，Bean以单例的形式存在，默认值
prototype|每次从容器中返回bean时，都会返回一个新的bean实例
request|每次HTTP请求都会创建一个新的bean，该作用域仅限于WebApplicationContext
session|每次HTTP Session共享一个bean，不同session使用不同bean，仅限于WebApplicationContext
globalSeesion|一般作用于Portlet应用环境，仅限于WebApplicationContext
bean.xml
```
<!--每次调用person都会返回相同的实例-->
<bean id="person" class="com.demo.Person"></bean>
<!--每次调用person1都会返回新的实例-->
<bean id="person1" class="com.demo.Person" scopt="prototype"></bean>
```
