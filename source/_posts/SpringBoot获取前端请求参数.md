---
title: SpringBoot获取前端请求参数
date: 2019-06-13 17:32:25
tags: SpringBoot
---
## SpringBoot获取前端请求参数
SpringBoot提供了三个注解用于获取前端请求的参数，分别是`@PathVariable`，`@RequestParam`，`@RequestBody`,下面介绍三个参数的用法以及区别

| 注解 | 支持类型 | 支持的请求类型 | 支持的Content-Type | 请求示例 |
| :---: | :---: | :---: | :---: | :---: |
| `@PathVariable` | URL | Get | all | /orders/{id} |
| `@RequestParam`  | URL | Get | all | /orders?id=1 |
| `@RequestParam`  | Body | Post/Put/Delete/Patch | form-data,x-www.form-urlencoded | /orders?id=1 |
| `@RequestBody`  | Body | Post/Put/Delete/Patch | json | {"id":1,"name":"aa"} |

##### `@PathVariable`示例
例如有一个接口获得`User`信息：
前端请求路由：`localhost:8080/getUserInfo/1`
后端`@Controller`层代码：
```
@GetMapping("/getUserInfo/{id}")
public User getUserInfo(@PathVariable int id){
    int userId = id;
    return new User(userId);
}
```
`User`代码:
```
public class User{
    private String name;
    private int age;
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name = name;
    }
    public int getAge(){
        return age;
    }
    public void setAge(int age){
        this.age = age;
    }
}
```
##### `@RequestParam`示例
依然是获得`User`信息
前端请求路由：`localhost:8080/getUserInfo/?id=1`
后端`@Controller`层代码：
```
@GetMapping("/getUserInfo")
public User getUserInfo(@RequestParam int id){
    int userId = id;
    return new User(userId);
}
```
##### `@RequestBody`示例
前端请求路由：localhost:8080/getUserInfo
请求参数:`body={"id":0}`
后端`@Controller`层代码
```
@GetMapping("/getUserInfo")
public User getUserInfo(@RequestBody Body body{
    int userId = body.getId();
    return new User(userId);
}
```
Json对象代码:
```
public class Body{
    private int id;
    public int getId(){
        return id;
    }
    public int setId(int id){
        this.id = id;
    }    
}
```
>前端在传递Json对象时，Java方面必须有对应的类去接收该Json对象，其中类属性可以比Json对象中的多，但是不可以少