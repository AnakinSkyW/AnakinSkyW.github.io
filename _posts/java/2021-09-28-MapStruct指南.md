---
layout: post
title: MapStruct指南
date: 2021-09-28 15:24:56
description: 优雅的对象属性映射利器MapStruct插件
share: true
tags:
 - notes
comments: true
toc: false
---

## 序  

通常在后端开发中经常不直接返回实体Entity类，经过处理转换返回前端，前端提交过来的对象也需要经过转换Entity实体才做存储；通常使用的`BeanUtils.copyProperties`方法也比较粗暴，不仅效率低下（使用反射）而且仅映射相同名的属性，多数情况下还需要手动编写对应的转换方法实现。  
插件MapStruct以接口方法结合注解优雅实现对象转换，MapStruct生成器生成代码以更贴近原生的Setter、Getter方法处理属性映射更为高效。  
https://github.com/mapstruct/mapstruct/  
https://github.com/mapstruct/mapstruct-examples  

## 简单用例  

>实体对象User
{:.filename}
{% highlight java %}
@Data
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private int age;
    private String address;
}
{% endhighlight %}
>转换对象UserVO
{:.filename}
{% highlight java %}
@Data
public class UserVO {
    private String name;
    private int age;
}
{% endhighlight %}
>转换接口
{:.filename}
{% highlight java %}
@Mapper
public interface UserConvert {
    UserConvert INSTANCE = Mappers.getMapper(UserConvert.class);
    UserVO toVO(User entity);
}
{% endhighlight %}
>使用示例
{:.filename}
{% highlight java %}
    @Test
    public void contextLoads() {
        User user = new User(0, "Tester", 1, "上海市徐汇区");
        UserVO userVO = UserConvert.INSTANCE.toVO(user);
    }
{% endhighlight %}
