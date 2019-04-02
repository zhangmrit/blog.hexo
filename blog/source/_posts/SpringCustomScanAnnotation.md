title: Spring自定义扫描注解
date: 2016-08-23 15:24:39
categories: spring  
tags: [JAVA]
---
最近在做一个项目,首先是因为要在普通静态类里面调用Service,因为Service是Dubbo提供者的类,只能通过注解来调用.
<!-- more -->
那么问题来了,该怎么调用Service呢?
### 静态类调用Service
一般需要在一个工具类中使用@Autowired 注解注入一个service。但是由于工具类方法一般都写成static，所以直接注入就存在问题。可以这样解决:
```
@Component  
public class TestUtils {  
  
    private static Logger logger = LoggerFactory.getLogger(OpeLogUtils.class);  
  
    @Autowired  
    private TestService testService;  
    private static TestUtils testUtils ;  
  
    public void setUserInfo(TestUtils testUtils ) {  
        this.testUtils = testUtils ;  
    }  
      
    @PostConstruct  
    public void init() {  
        testUtils = this;  
        testUtils.testService= this.testService;  
    }  
  }
```
@PostConstruct  是spring标签,相当于在配置文件声明注入
### 如何让Spring扫描该静态类
这样通过`testUtils.testService`调用即可.那么问题来,这样做了以后你会发现spring依旧不加载testService,声明没有执行,
这里别忘记在上面的代码中声明一个Spring注解,如`@Component`可能有的同学到这一步是成功,因为你可能是默认配置,默认配置是扫描base-package下所有的注解,这样性能肯定不好,勤学苦练,苦思冥想,终于让我找到配置起来比较高逼格的做法,话不多说,上代码:

```
	<!--  自动扫描，完成bean的创建和依赖注入 -->
 	<context:component-scan base-package="com.example" use-default-filters="false">
 		<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller" />
 		<context:include-filter type="annotation" expression="org.springframework.stereotype.Component" />
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service" /> 	
 		<context:exclude-filter type="regex" expression="com.example.controller"/>
 		<context:exclude-filter type="regex" expression="com.example.service"/>
 	</context:component-scan>
```
稍微解释下,在xml配置了`<context:component-scan>`这个标签后，spring可以自动去扫描base-pack下面或者子包下面的java文件，如果扫描到有`@Component @Controller@Service`等这些注解的类，则把这些类注册为bean
注意：如果配置了`<context:component-scan>`那么`<context:annotation-config/>`标签就可以不用再xml中配置了，因为前者包含了后者。另外`<context:annotation-config/>`还提供了两个子标签 `<context:include-filter>` 和`<context:exclude-filter>`在说明这两个子标签前，先说一下`<context:component-scan>`有一个use-default-filters属性，改属性默认为true,这就意味着会扫描指定包下的全部的标有`@Component`的类，并注册成bean.也就是`@Component`的子注解`@Service,@Reposity`等。所以如果仅仅是在配置文件中这么写
```
<context:component-scan base-package="com.example"/>
```
 `use-default-filter`此时为true那么会对base-package包或者子包下的所有的进行java类进行扫描,并把匹配的java类注册成bean。
 
 可以发现这种扫描的粒度有点太大，如果你只想扫描指定包下面的Controller，该怎么办？此时子标签`<context:incluce-filter>`就起到了勇武之地。如下所示

```
<context:component-scan base-package="com.example.controller">  
	<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>   
</context:component-scan>  
```

这样就会只扫描base-package指定下的有`@Controller`下的java类，并注册成bean
但是因为`use-dafault-filte`r在上面并没有指定，默认就为true，所以当把上面的配置改成如下所示的时候，就会产生与你期望相悖的结果（注意base-package包值得变化）

```
<context:component-scan base-package="com.example.controller">  
	<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>   
</context:component-scan>  
```

此时，spring不仅扫描了`@Controller`，还扫描了指定包所在的子包service包下注解`@Service`的java类
此时指定的include-filter没有起到作用，只要把use-default-filter设置成false就可以了。这样就可以避免在base-packeage配置多个包名这种不是很优雅的方法来解决这个问题了。
另外在我参与的项目中可以发现在base-package指定的包中有的子包是不含有注解了，所以不用扫描，此时可以指定`<context:exclude-filter>`来进行过滤，说明此包不需要被扫描。综合以上说明
Use-dafault-filters=”false”的情况下：`<context:exclude-filter>`指定的不扫描，`<context:include-filter>`指定的扫描这个配置文件中必须声明xmlns:context 这个xml命名空间，在schemaLocation中需要指定schema：  
```
  http://www.springframework.org/schema/context
  http://www.springframework.org/schema/context/spring-context-3.0.xsd
```
这个文件中beans根节点下只有一个context:component-scan节点，此节点有两个属性base-package属性告诉spring要扫描的包，use-default-filters="false"表示不要使用默认的过滤器，此处的默认过滤器，会扫描包含Service,Component,Repository,Controller注解修饰的类，而此处我们处于示例的目的，故意将use-default-filters属性设置成了false。

context:component-scan节点允许有两个子节点`<context:include-filter>`和`<context:exclude-filter>`。filter标签的type和表达式说明如下：

| Filter Type      |     Examples Expression|   Description|
| :-------- | --------:| :------: |
| annotation|   org.example.SomeAnnotation|  符合SomeAnnoation的target class|
| assignable|   org.example.SomeClass|  指定class或interface的全名|
| aspectj|   org.example..*Service+|  	AspectJ语法|
| regex|   org\.example\.Default.*|  Regelar Expression|
| custom|   org.example.MyTypeFilter|  Spring3新增自定Type，org.springframework.core.type.TypeFilter|
在我们的示例中，将filter的type设置成了正则表达式，regex，注意在正则里面.表示所有字符，而\.才表示真正的.字符。我们的正则表示以Dao或者Service结束的类。

我们也可以使用annotaion来限定，如下：

```
<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"xmlns:context="http://www.springframework.org/schema/context"xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context-3.0.xsd"><context:component-scan base-package="cn.outofmemory.spring" use-default-filters="false"><context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/><context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/></context:component-scan></beans>
```

 这里我们指定的include-filter的type是annotation，expression则是注解类的全名。

另外context:conponent-scan节点还有`<context:exclude-filter>`可以用来指定要排除的类，其用法和include-filter一致。