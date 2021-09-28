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
[https://github.com/mapstruct/mapstruct/](https://github.com/mapstruct/mapstruct/)  
[https://github.com/mapstruct/mapstruct-examples](https://github.com/mapstruct/mapstruct-examples)  

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
    @Mapping(source = "name", target = "userName")
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

## 使用详解  

### 关于接口注解 `@Mapper`  

`uses` 使用其他手动编写的或者其他Mapper接口覆写相关的转换方法，不能循环引用  
{% highlight java %}
@Mapper(uses=DateMapper.class)
{% endhighlight %}

`imports` 引用相关类，允许通过`mapping.expression()`、`mapping.defaultExpression()`直接使用这些类型  
>example
{:.filename}
{% highlight java %}
@Mapper(imports = DateUtil.class)
public interface UserConvert {
    UserConvert INSTANCE = Mappers.getMapper(UserConvert.class);
    @Mappings({
            @Mapping(source = "name", target = "userName"),
            // 以指定方法转换属性，这里如果不使用imports，则需要写全引用类包路径
            @Mapping(target = "birthday", expression = "java(DateUtil.formatDate(entity.getBirthday()))")
    })
    UserVO toVO(User entity);
}
{% endhighlight %}

`unmappedSourcePolicy`、`unmappedTargetPolicy` 针对源类型/目标类型中未映射属性的反馈策略，主要有三种：`IGNORE`，默认值，忽略未映射的字段。`WARN`，警告。`ERROR`，报错
`typeConversionPolicy` 针对有损转换的反馈策略，例如Long转Integer  
>example
{:.filename}
{% highlight java %}
@Mapper(unmappedSourcePolicy = ReportingPolicy.ERROR)
{% endhighlight %}

`componentModel` 指定生成映射器实例的模式，主要有四种：`default`，不主动生成实例，通常以`Mappers.getMapper(Class)`实例化。`cdi`，以CDI标准实例化映射器，使用`@Inject`注入相关实例，`spring`，Spring Bean方式实例化，`jsr330`，jsr330标准实例化
>example
{:.filename}
{% highlight bash %}
@Mapper(componentModel = "spring")
public interface UserConvert {
    @Mapping(source = "name", target = "userName")
    UserVO toVO(User entity);
}
{% endhighlight %}

{% highlight java %}
@Autowired
private UserConvert userConvert;
{% endhighlight %}

`implementationName` 指定实现类名称，映射生成器接口会自动生成实现类`<CLASS_NAME>Impl`，使用此属性可避免类冲突  
`implementationPackage` 指定实现类包路径  

`config` 指定配置类，由指定的`@MapperConfig`配置类，`config`导入相关配置  
>配置类
{:.filename}
{% highlight java %}
@MapperConfig(
        uses = DateUtil.class,
        unmappedSourcePolicy = ReportingPolicy.WARN
)
public interface UserConfig {
}
{% endhighlight %}  
>导入配置类
{:.filename}
{% highlight java %}
@Mapper(componentModel = "spring", config = UserConfig.class)
{% endhighlight %}
