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

## 一 序  

通常在后端开发中经常不直接返回实体Entity类，经过处理转换返回前端，前端提交过来的对象也需要经过转换Entity实体才做存储；通常使用的`BeanUtils.copyProperties`方法也比较粗暴，不仅效率低下（使用反射）而且仅映射相同名的属性，多数情况下还需要手动编写对应的转换方法实现。  
插件MapStruct以接口方法结合注解优雅实现对象转换，MapStruct生成器生成代码以更贴近原生的Setter、Getter方法处理属性映射更为高效。  
[https://github.com/mapstruct/mapstruct/](https://github.com/mapstruct/mapstruct/)  
[https://github.com/mapstruct/mapstruct-examples](https://github.com/mapstruct/mapstruct-examples)  

## 二 简单用例  

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

## 三 使用详解  

### 1 关于接口注解 `@Mapper`几种属性用法详解  

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


`unmappedSourcePolicy`、`unmappedTargetPolicy` 针对源类型/目标类型中未映射属性的反馈策略，主要有三种：**IGNORE**，默认值，忽略未映射的字段。**WARN**，警告。**ERROR**，报错  
`typeConversionPolicy` 针对有损转换的反馈策略，例如Long转Integer  
>example
{:.filename}
{% highlight java %}
@Mapper(unmappedSourcePolicy = ReportingPolicy.ERROR)
{% endhighlight %}


`componentModel` 指定生成映射器实例的模式，主要有四种：**default**，不主动生成实例，通常以Mappers.getMapper(Class)实例化。**cdi**，以CDI标准实例化映射器，使用@Inject注入相关实例，**spring**，Spring Bean方式实例化，**jsr330**，jsr330标准实例化
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


`implementationName` 指定实现类名称，映射生成器接口会自动生成实现类<CLASS_NAME>Impl，使用此属性可避免类冲突    
`implementationPackage` 指定实现类包路径    


`config` 指定配置类，由指定的@MapperConfig配置类，**config**导入相关配置  
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


`collectionMappingStrategy` 集合映射策略，这里注意集合映射时，如果集合中的类型已有对应转换方法，集合转换时会优先使用  

{:.filename}
{% highlight java %}
@Mappings({
        @Mapping(source = "name", target = "userName"),
        @Mapping(target = "birthday", expression = "java(DateUtil.formatDate(entity.getBirthday()))")
})
UserVO toVO(User entity);
List<UserVO> collectionCvt(List<User> entities);
{% endhighlight %}

> GENERATED CODE 生成器生成代码
{:.filename}
{% highlight java %}
public List<UserVO> collectionCvt(List<User> entities) {
    if (entities == null) {
        return null;
    } else {
        List<UserVO> list = new ArrayList(entities.size());
        Iterator var3 = entities.iterator();

        while(var3.hasNext()) {
            User user = (User)var3.next();
            // 集合转换时优先使用了已定义的toVO方法
            list.add(this.toVO(user));
        }

        return list;
    }
}
{% endhighlight %}

`nullValueMappingStrategy` **null**作为源值映射策略；**RETURN_NULL**默认返回null, **RETURN_DEFAULT**返回默认值，对于对象会通过构造器自动构造对象返回，集合会返回空集合  
>
{: .note .warning}
当值为RETURN_DEFAULT时，如果映射规则中包含Mapping.expression、Mapping.constant必须手动判空处理，否则NPE  
{:.filename}
{% highlight java %}
@MapperConfig(nullValueMappingStrategy = NullValueMappingStrategy.RETURN_DEFAULT)
{% endhighlight %}

`NullValuePropertyMappingStrategy` **null**作为源属性映射策略；**SET_TO_NULL**默认返回null，**SET_TO_DEFAULT**返回默认值，**IGNORE** 忽略该值，以目标对象已存在的值为准  


`MappingInheritanceStrategy` 继承方法级映射配置策略：**EXPLICIT** 显示使用InheritConfiguration生效。**AUTO_INHERIT_FROM_CONFIG** 自动继承正向转换的配置。**AUTO_INHERIT_REVERSE_FROM_CONFIG** 自动继承反向转换的配置。**AUTO_INHERIT_ALL_FROM_CONFIG** 都继承  
{:.filename}
{% highlight java %}
@MapperConfig(
        mappingInheritanceStrategy = MappingInheritanceStrategy.EXPLICIT
)
{% endhighlight %}

{:.filename}
{% highlight java %}
@InheritConfiguration
void cvtVO(User entity, @MappingTarget UserVO vo);
{% endhighlight %}

`nullValueCheckStrategy`  空值监测策略  


### 2 其他方法级别注解  

`@InheritInverseConfiguration` 反向转换时继承映射规则  

`@Mapping` 配置类型属性的映射规则；  
>**dateFormat** 格式化日期  
{:.filename}
{% highlight java %}
@Mapping(target = "birthday", dateFormat = "yyyy-MM-dd HH:mm:ss")
{% endhighlight %}

>**numberFormat** 数字格式化
{:.filename}
{% highlight java %}
@Mapping(target = "price", numberFormat = "$#.00")
{% endhighlight %}  

>**constant** 常量
{:.filename}
{% highlight java %}
@Mapping(target = "age", constant = "0")
{% endhighlight %}