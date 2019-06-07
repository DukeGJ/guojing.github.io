---
title: Bean实例化方式
date: 2019-06-07 22:33:08
tags:
---
# SpringBean实例化的方式
主要介绍三种实例化Bean的方式
- 构造函数
- 静态工厂
- 实例工厂

## 构造函数
最常用的实例化方式，Bean必须提供默认的构造函数
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
bean.xml内容
```
<bean id="person" class="com.demo.Person"></bean>
<bean id="person1" class="com.demo.Person">
    <constructor-arg name="name" value="张三"/>
</bean>
<bean id="person2" class="com.demo.Person">
        <constructor-arg name="name" value="张三"/>
        <constructor-arg name="age" value="18"/>
</bean>
```
## 静态工厂

当采用静态工厂的方式创建Bean时，除了要指定`class`属性之外，还需要指定`factory-method`属性，并且工厂方法必须是静态的
```
public class StaticFactory{
    //方法必须是静态的
    public static Person createPerson(){
        return new Person();
    }
    public static Person createPerson(String name){
        return new Person(name);
    }
}
```
bean.xml内容:
```
<bean id="person" class="com.demo.StaticFactory" factory-mathod="createPerson"></bean>
<bean id="person1" class="com.demo.StaticFactory" factory-mathod="createPerson">
    <constructor-arg name="name" value="张三"/>
</bean>
```
## 实例工厂
与静态工厂方式相比，采用实例工厂创建Bean时，`class`属性必须为空，`factory-bean`属性必须指定为当前类或者父类中包含工厂方法的名称，而该工厂Bean工厂方法必须通过`factory-mathod`属性指定。*必须先有工厂实例，通过实例对象创建实例*提供的所有方法都是非静态的
```
//实例工厂，所有方法非静态
public class Factory{
    public Person createPerson(){
        return new Person()
    }
    public Person createPerson(String name){
        return new Person(name);
    }
}
```
bean.xml内容
```
<bean id="factory" class="com.demo.Factory"></bean>
<bean id="person" factory-bean="factory" factory-mathod="createPerson"></bean>
<bean id="person1" factory-bean="factory" factory-mathod="createPerson">
    <constructor-arg name="name" value="张三"/>
</bean>
```