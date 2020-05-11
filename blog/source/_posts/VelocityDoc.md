title: Velocity模板语言(VTL) 入门指南
date: 2020-05-11 16:20:01
categories: 工具
tags: [velocity]
---
velocity模板好像没有什么比较好的文档，搜到一个，但是排版有点乱，所以美化和部分翻译以后分享给大家
<!-- more -->

原文地址 https://blog.csdn.net/weixin_34056162/article/details/90686571

Velocity模板语言 (VTL)提供了容易，简单，和清洁方式合并动态内容在你的网页上。即使一个网页开发者有少量或者没有编程经验也能很快有能力使用VTL嵌入动态内容到你的Web页面。

VTL使用引用嵌入动态内容到Web页面, 一个引用也是一个引用类型. 变量是一种引用类型能够引用Java中定义变量,或者能够获取它的值在你的Web页面中通过VTL语句。这里有一个例子 VTL语句能够被嵌入到你的HTML文档中:

```none
#set( $a = "Velocity" )
```

这个VTL 语句, 像所有的VTL语句一样，通过 *#* 字符开始并包含一个指令: *set*. 当一个用户访问你的页面时,Velocity模板 将在你的Web页面中搜索所有的*#*字符, 然后认为它是VTL语句的开始，但是#字符并没有实际意义。

字符 *#*后面紧跟着, *set*.*set* 指向一个表达式 (放在括号里面) — 一个等式为变量分配一个值。变量的定义在左边赋值在右边; 它们之间通过 *=* 分开。

在上面的实例中, 变量是 *$a* ,它的值是*Velocity。*这个变量像其它的引用一样, 通过 *$* 字符开始。字符串的值放在引号中,单引号或双引号都可以。单引号保证引号里面的值分配给变量。双引号准许你使用velocity的引用和指令,例如  “Hello $name”,  *$name* 将被定义的变量值=右边的内容所替换

下面的规则能使你更好的了解Velocity的工作原理：引用是通过*$* 开始的，用来获取值。指令*#* 用来定义值。

在上面实例中, *#set* 被用来给变量分配一个值。这个变量是 *$a*, 能够在模板中使用并输出 “Velocity”。

原文地址 https://blog.csdn.net/weixin_34056162/article/details/90686571

## Hello Velocity World实例

当一个值分配给一个变量时, 你能够引用这个变量在你的HTML 文档中.在下面的实例中, 先对 *$foo* 进行赋值，然后引用它.

```none
<html>
<body>
#set( $foo = "Velocity" )
Hello $foo World!
</body>
<html>
```

这个网页的结果是答应出 “Hello Velocity World!”.

为了是包含VTL语句指定可读性更强, 我们鼓励你开始每个VTL语句在新的一行, 但并不强制要求你这么做.*set* 指令将在后面章节详细介绍.

## 注释

注释以一种不被Velocity模板修改的原样输出描述文本。注释是一种很有用的方式，提醒你并且告诉其他人VTL语句的作用, 或者存放对你有用的其他信息。下面就是VTL注释的实例：

```none
## 这是一个单行注释.
```

*##* 表示的是单行注释表示本行都被注释。假如你需要多行注释, 不需要一行一行的注释。你可以使用多行注释, 多行注释以 `#*` 开始，以 `*#`做为结束, 可以用它来处理多行注释的情况.

```none
这是多行注释，浏览者能看到它。

#*
 Thus begins a multi-line comment. Online visitors won't
 see this text because the Velocity Templating Engine will
 ignore it.

*#

这里多行注释; 它是可见的.
```

这里有很多实例阐明多行注释和单行注释:

```none
This text is visible. ## This text is not.
This text is visible.
This text is visible. #* This text, as part of a multi-line
comment, is not visible. This text is not visible; it is also
part of the multi-line comment. This text still not
visible. *# This text is outside the comment, so it is visible.
## This text is not visible.
```

这是第三种的注释,  VTL注释块, 它被用来储存一些你需要关注的信息模板 (例如. javadoc作者版本信息):

```none
#**
This is a VTL comment block and
may be used to store such information
as the document author and versioning
information:
@author
@version 5
*#
```

## 引用

在VTL中有三种类型的引用: 变量，属性和方法。作为一个使用VTL的设计者, 你和工程师必须一起在你的模板中，指定名字以及它所代表的引用。

**变量**
VTL中变量标识符前面需要添加 “$” 来进行标记. AVTL标识符必须以字母 (a .. z or A .. Z)开始. 其他的字符可以使用下面几种:

- alphabetic (a .. z, A .. Z)
- numeric (0 .. 9)
- hyphen (“-“)
- underscore (“_”)

下面有一些可用的变量引用实例在VTL中:

```none
$foo
$mudSlinger
$mud-slinger
$mud_slinger
$mudSlinger1
```

当VTL应用一个变量时, 例如*$foo，*这个变量可以获取一个值从模板的 *set*指令中, 或者从Java代码中。例如,假如在Java中定义了一个变量*$foo*,java中定义的值Web页面中所有的 *$foo*引用. 或者, 我在页面中定义下面语句

```none
#set( $foo = "bar" )
```

$foo输出的结果将和你定义的是一样的。

**属性**
VTL中第二个特点鲜明的引用是属性引用, 属性有一个与众不同的格式. 它的标识符前面需要添加一个*$*变量标识符, 紧跟着后面一个点(“.”) . 下面是一个在VTL中属性引用的实例:

```none
$customer.Address
$purchase.Total
```

就拿第一个实例来说, *$customer.Address*.它有两个意思. 它意味着,查询被*customer* 标识的哈希表返回和它相关联的*Address* 的值。同时*$customer.Address*也能被一个方法引用 (方法的引用在下一章在讨论); *$customer.Address*是*$customer.getAddress()*的一种缩写形式。如果你的网页有需求, Velocity 将可以使这两种形式变的有意义, 然后返回正确的值。

**方法**
Java中定义的方法能够定义很多有用的事情, 像进行计算或者达到某种目的. 方法的引用是通过 “$” 标识符, 后面紧跟着方法体. VTL方法体紧跟着左括号符  (“(“), 紧跟着参数列表, 紧跟着右括号符(“)”). 下面是VTL中方法引用的实例:



```none
$customer.getAddress()
$purchase.getTotal()
$page.setTitle( "My Home Page" )
$person.setAttributes( ["Strange", "Weird", "Excited"] )
```

前两个例 — *$customer.getAddress()* 和 *$purchase.getTotal()* — 可以看着是属性的引用, *$customer.Address* 和*$purchase.*总之.这些引用在VTL中是很常见的!

VTL 属性引用能够被当着方法引用的简写. 属性 *$customer.Address* 引用和 *$customer.getAddress()*方法的引用效果是一样的. 一般情况下如果可以,我们通过简写的方式来引用方法.属性和方法主要不同是方法能够引用参数 .

下面的方法能够被简写

```none
$sun.getPlanets()
$annelid.getDirt()
$album.getPhoto()
```

我们可能期望方法返回属于太阳的行星的名字, 喂蚯蚓, 或者活动一张图片或者专辑. 下面方法有很长的标记.

```none
$sun.getPlanet( ["Earth", "Mars", "Neptune"] )
## Can't pass a parameter list with $sun.Planets
$sisyphus.pushRock()
## Velocity assumes I mean $sisyphus.getRock()
$book.setTitle( "Homage to Catalonia" )
## Can't pass a parameter
```

自从Velocity 1.6, 所有的数组引用被看这固定长度列表. 这意味着你能够调用java.util.Lis方法在数组引用。所以， 假如你有一个引用在数组上 ( 假定这里有一个字符数组后三个字)，你能够这样做:

```none
$myarray.isEmpty()
$myarray.size()
$myarray.get(2)
$myarray.set(1, 'test')
```

在Velocity 1.6支持可变参数方法. 一个方法 public void setPlanets(String... planets) 或者甚至是 public void setPlanets(String[] planets) (假如你使用Java 5 JDK), 现在可以接受任何数量的参数时调用的模板.

```none
sun.setPlanets('Earth', 'Mars', 'Neptune')
$sun.setPlanets('Mercury')
$sun.setPlanets()
## Will just pass in an empty, zero-length array
```

**属性调用规则**
正如前面提到的, 属性经常涉及到父类方法的引用. Velocity是十分擅长解决方法对应的属性获取，它可以根据几种不同的命名约定进行选择，准确的查找规则依赖是否属性的名字以大写开始。对于小写名字,例如 *$customer.address*, 调用的顺序是

1. getaddress()
2. getAddress()
3. get(“address”)
4. isAddress()

对于大写的属性名字像 *$customer.Address*, 它稍微不同:

1. getAddress()
2. getaddress()
3. get(“Address”)
4. isAddress()

**渲染**
每一个引用的值(变量，属性，或者方法)都被转换为一个字符串并作为最终的输出。假如这里有一个对象表示为*$foo* (例如一个整数对象), 当Velocity调用它时，Velocity会调用它的.toString() 方法转化为字符串.

**索引标识符**
使用$foo[0] 的符号形式能够访问被定义的索引对象。这个形式和通过get调用一个对象是相同的例如$foo.get(0), 提供了一种简单的语法操作。下面是这种操作的实例:

```none
$foo[0]       ## $foo takes in an Integer look up
$foo[$i]      ## Using another reference as the index
$foo["bar"]   ## Passing a string where $foo may be a Map
```

这相同的语法也能够使用在Java数组上因为由于Velocity封装了数组在访问对象上提供了一个get(Integer)方法,它能返回一个特殊的元素。

相同的语法可以在任何.get的地方使用，例如:

```none
$foo.bar[1].junk
$foo.callMethod()[1]
$foo["apple"][4]
```

一个引用也能够通过索引来进行赋值, 例如:

```none
#set($foo[0] = 1)
#set($foo.bar[1] = 3)
#set($map["apple"] = "orange")
```

指定的元素被赋值给定的值。Velocity 尝试第一次 ‘set’ 方法在元素上, 然后通过’put’ 来进行赋值.

**正式引用标识符**
上面的实例向大家展示了引用标识符的使用, 然后，Velocity也提供了正式引用标识符的引用, 请看下面实例:

```none
${mudSlinger}
${customer.Address}
${purchase.getTotal()}
```

在大部分情况下你能够使用标识符引用，但是有些情况下要求正确的符号被要求正确的处理。

如果你构建一个语句*$vice*被使用作为一个基础语句的单词。它的目的允许有人选择基础单词产生这种结果: “Jack is a pyromaniac.” 或者 “Jack is a kleptomaniac.”。使用简写标识符将不满足这种需求。思考下面的例句:

```none
Jack is a $vicemaniac.
```

它是含糊不清的, Velocity认为它是*$vicemaniac*, 而不是*$vice*. 发现变量*$vicemaniac，*没有赋值，它将返回 *$vicemaniac*. 现在使用正式标识符能够解决这种问题.

```none
Jack is a ${vice}maniac.
```

现在Velocity能够识别*$vice*, 而不误认为*$vicemaniac*. 正式引用经常被使用在引用指向邻近文本的模板中.

**静态引用标识符**
当Velocity遇到一个没有定义的引用时，正常的是输出图像引用. 例如, 假如下面的引用是VTL模板的一部分.

```none
<input type="text" name="email" value="$email"/>
```

当它初始化时, 变量 *$email*应用并没有值, 但是你宁愿”$email”是一个空白文本字段 . 使用静态引用标识符避免这种正规的行为; 通过在你的VTL模板中使用*$!email*代替$email。上面的实例可以改成下面这种形式:

```none
<input type="text" name="email" value="$!email"/>
现在当初始化时如果*$email*没有值, 一个空的字符串将输出代替”$email”。
```

正式应用和静态引用经常一起使用，就像下面实例一样.

```none
<input type="text" name="email" value="$!{email}"/>
```

## 严格引用模式

Velocity 1.6介绍了一种新的概念，就是严格引用模式，它是通过配置属性的模式来实现灵活的设置。这种设置的目的是使Velocity的特性更加严格区分没有定义或者模棱两可的实例中, 类似与编程语言, 它更适合Velocity的使用。当遇到没有定义或者模棱两可的情况Velocity将抛出异常。下面将讨论严格应用和传统引用的不同之处。

设置引用要求在上下文中或者被#set 指令定义否则Velocity将抛异常。在上下文中引用一个null值并不会产生异常。此外，假如你企图在一个对象中调用一个没有定义的方法或者属性时，它将抛异常。如果调用的方法或属性是一个null值也会产生异常。

在下面的实例中 $bar被定义了但是 $foo却没有被定义,所有这些语句将抛出异常:

```none
$foo                         ## Exception
#set($bar = $foo)            ## Exception
#if($foo == $bar)#end        ## Exception
#foreach($item in $foo)#end  ## Exception
```

同时, 你调用一个并不存在的方法或属性时，也会产生异常。在下面的实例中 $bar对象定义一个属性 ‘foo’ 返回一个字符串 , ‘retnull’ 将返回null.

```none
$bar.bogus          ## $bar does not provide property bogus, Exception
$bar.foo.bogus      ## $bar.foo does not provide property bogus, Exception
$bar.retnull.bogus  ## cannot call a property on null, Exception
```

一般严格引用使用所有情况除了特殊的 #if 指令.假如一个引用使用 #if 或者#elseif 指令没有任何方法或属性,假如它并没有和其他值进行比较,这种引用是准许的.这种方式通常用来判断一个引用是否被定义. 在下面的实例中$foo 并没有定义但是它也不会抛异常.

```none
#if ($foo)#end                  ## False
#if ( ! $foo)#end               ## True
#if ($foo && $foo.bar)#end      ## False and $foo.bar will not be evaluated
#if ($foo && $foo == "bar")#end ## False and $foo == "bar" wil not be evaluated
#if ($foo1 || $foo2)#end        ## False $foo1 and $foo2 are not defined
```

严格模式在 #if 指令中可以使用 >, <, >= or <=. 同时,参数 #foreach必须可以迭代的 (这种特性可以被属性指令.foreach.skip.invalid修改). 不过,在严格模式下没有定义的引用也将抛异常。

Velocity试图调用一个null值将导致异常. 对于简单的调用在这个实例中你可以用 ‘$!’代替 ‘$’, 这种类似于非严格模式。请记住，当你使用严格引用调用不存在的变量时会出现异常。例如, 下面的$foo 在上下文中值为null

```none
this is $foo    ## throws an exception because $foo is null
this is $!foo   ## renders to "this is " without an exception
this is $!bogus ## bogus is not in the context so throws an exception
```

## 模式替换

现在你已经熟悉了引用, 你可以在你的模板中使用它们。Velocity引用利用了一些Java的设计原则进行设计，很容易使用。 例如:

```none
$foo
$foo.getBar()
## is the same as
$foo.Bar
$data.setUser("jon")
## is the same as
#set( $data.User = "jon" )
$data.getRequest().getServerName()
## is the same as
$data.Request.ServerName
## is the same as
${data.Request.ServerName}
```

这些实例是提供了多重选择，但是效果却是一样的。Velocity利用Java的特性和Bean特性解决上下文中名字在对象和方法中的引用。它是可能的在你的模板中嵌入和引用的值.

Velocity是模仿sun公司的bean定义的规范，大小写敏感的; 然而，开发者尽可能的避免和纠正用户可能出现的错误 . 当*getFoo()* 方法在模板中被 $bar.foo引用时, Velocity首先将尝试着调用$getfoo。假如失败了, 它将尝试着调用$getFoo。同样的, 当一个模板引用 $bar.Foo, Velocity首先将尝试着调用 *$getFoo()* 然后在调用*getfoo()*.

*注意: 在模板中引用实例变量是不允许的。仅仅引用属性等同于 JavaBean getter/setter 方法是可以的 (例如. $foo.Name能够通过 Foo’s getName()实例方法来引用 , 但是不是对一个公共的Name 实例的变量Foo).*

## 指令

引用允许设计者在网页上生成动态的内容, 指令很容易使用脚本元素来创建和操纵输出的Java代码，并允许Web设计者关注网页的内容。

指令一直以 #开始. 像引用一样,指令的名字可能是相等的通过{ 和 } 符号. 这是好的方式在指令后跟文本. 例如下面的程序有一个错误:

```none
#if($a==1)true enough#elseno way!#end
```

在这实例中, 使用方括号把 #else 与其他行分开.

```none
#if($a==1)true enough#{else}no way!#end
```

**#set** *#set* 指令被用来设定一个引用的值. 这个值能够被分配一个变量引用或者属性引用,这种情况发生在括号中, 如下实例:

```none
#set( $primate = "monkey" )
#set( $customer.Behavior = $primate )
```

左边的(LHS)必须分配一个变量引用或者属性引用. 右边的(RHS)可以是以下类型:

- Variable reference
- String literal
- Property reference
- Method reference
- Number literal
- ArrayList
- Map

这些实例演示了上面的每种类型:

```none
#set( $monkey = $bill ) ## variable reference
#set( $monkey.Friend = "monica" ) ## string literal
#set( $monkey.Blame = $whitehouse.Leak ) ## property reference
#set( $monkey.Plan = $spindoctor.weave($web) ) ## method reference
#set( $monkey.Number = 123 ) ##number literal
#set( $monkey.Say = ["Not", $my, "fault"] ) ## ArrayList
#set( $monkey.Map = {"banana" : "good", "roast beef" : "bad"}) ## Map
```

注释: 对于定义的ArrayList 实例元素可以使用ArrayList 类里面定义的方法, 例如, 你能够访问上面第一个元素使用$monkey.Say.get(0).

相似的, 对于Map实例, 元素通过 { } 操作符来定义你能够使用Map类中定义的方法. 例如, 你能够访问上面实例中第一个元素通过$monkey.Map.get(“banana”) 并且返回 ‘good’, 甚至你可以使用 $monkey.Map.banana来返回一样的值.

RHS也能使用简单的算术表达式:

```none
#set( $value = $foo + 1 )
#set( $value = $bar - 1 )
#set( $value = $foo * $bar )
#set( $value = $foo / $bar )
```

假如RHS是一个null的属性和方法引用, 它将不能分配给LHS.这主要依赖你的配置,通过这种机制通常不能移除一个已经存在的引用. (注意它准许你改变Velocity配置属性). 新手对这点比较困惑. 例如:

```none
#set( $result = $query.criteria("name") )
The result of the first query is $result
#set( $result = $query.criteria("address") )
The result of the second query is $result
```

如果*$query.criteria(“name”)* 返回”bill”,  *$query.criteria(“address”)* 返回null, 上面的 VTL将输出如下内容 :

```none
The result of the first query is bill
The result of the second query is bill
```

对于入门者对于 *#foreach* 循环企图#set一个应用通过一个属性或者方法引用是很迷惑的,接着通过 *#if*指令来进行测试. 例如:

```none
#set( $criteria = ["name", "address"] )
#foreach( $criterion in $criteria )
    #set( $result = $query.criteria($criterion) )
    #if( $result )
        Query was successful
    #end
#end
```

在上面的实例中, 它并不希望通过*$result* 来决定查询是否成功. *$result* 之后通过 *#set* (增加到上下文中),它不能返回为null  (被移除从上下文中). *#if* 和 *#foreach*指令相关的细节将在之后的文档中都涉及到.

一种解决这种情况通过预设 *$result*为 *false*. 然后如果 *$query.criteria()* 调用失败, 你能够检测到.

```none
#set( $criteria = ["name", "address"] )
#foreach( $criterion in $criteria )
    #set( $result = false )
    #set( $result = $query.criteria($criterion) )
    #if( $result )
        Query was successful
    #end
#end
```



不像其他的Velocity指令,*#set* 指令并没有 *#end*语句.

**Literals**当你使用 *#set*指令的时候, 在双引号里面的字符串将被解析, 如下所示:

```none
#set( $directoryRoot = "www" )
#set( $templateName = "index.vm" )
#set( $template = "$directoryRoot/$templateName" )
$template
```

输出结果为

```none
www/index.vm
```

然而, 当字符串处在单引号中, 它将不被解析:

```none
#set( $foo = "bar" )
$foo
#set( $blargh = '$foo' )
$blargh
```

输出结果是:

```none
  bar
  $foo
```

默认情况, 这种特征使用单引号不解析Velocity中的可用变量. 你也可以通过改变velocity.properties 中的stringliterals.interpolate=false配置来改变这种默认设置.

或者,  *#[[*don’t parse me!*]]#* 语法准许模板设计者很容易的使用大量的语句块,而这些语句块中的变量不会被解析. 它特别有用尤其在避免有大量的指令上地方或者其他无效的内容(不想被解析的)VTL.

```none
#[[
#foreach ($woogie in $boogie)
  nothing will happen to $woogie
#end
]]#
```

显示如下:

```none
#foreach ($woogie in $boogie)
  nothing will happen to $woogie
#end
```

## 条件语句

**If / ElseIf / Else**

Velocity中 *#if* 指令可以包含一段文本当生成Web网页时, 当条件语句为true时. 例如:

```none
#if( $foo )
   <strong>Velocity!</strong>
#end
```

变量*$foo*计算出是否是true，下面三种情况哪种情况将发生:

- *如果是true值时，$foo是一个* boolean类型 (true/false)
- *如果不为空对象或空值时，$foo是一个字符串或条件表达式。*
- *如果不为空对象，且不是字符串或条件表达式时，$foo*是一个对象。

记住 Velocity上下文仅仅包含对象，所以当我们说 ‘boolean’,它将代表 Boolean (类)。这是真实的即使这个方法返回的值为布尔值，它内部其实返回的是一个Boolean 值。如果if为true *#if* 和 *#end* 内容将输出。在这个实例中, 假如 *$foo* 为 true, 它将输出为: “Velocity!”。相反的, 假如 *$foo* 有一个 null 值，或者它的布尔值为false, 这个语句的结果为false,这里将没有输出。

*#elseif* 或者 *#else* 元素能够和*#if* 元素一起使用. 注意Velocity 模板将停止在第一个表达式中当发现第一个表达式为true时, 假设*$foo*值为 15 *$bar* 值为6.

```none
#if( $foo < 10 )
    <strong>Go North</strong>
#elseif( $foo == 10 )
    <strong>Go East</strong>
#elseif( $bar == 6 )
    <strong>Go South</strong>
#else
    <strong>Go West</strong>
#end
```

在这个实例中, *$foo* 大于10，所以第一个两个比较是false。下一个*$bar* 和6相等, 所以显示true, 所以输出 **Go South。**

**关系和逻辑操作符**

Velocity 使用等号决定两个变量之间的关系. 下面简单实例展示了等会的怎么使用.

```none
#set ($foo = "deoxyribonucleic acid")

#set ($bar = "ribonucleic acid")
#if ($foo == $bar)
  In this case it's clear they aren't equivalent. So...
#else
  They are not equivalent and this will be the output.
#end
```

注意 *==* 语法和Java中的语法是不同,Java中==仅仅表示对象是否相等. Velocity中的等号操作符仅仅表示两个数字,字符串,或对象的比较。当两个类对象是不同时, 字符串是通过调用toString()来获取的然后来比较.

Velocity的逻辑操作符有AND, OR 和NOT 操作符. 下面的逻辑操作符显示了 AND, OR 和NOT 操作符的使用.

```none
## logical AND
#if( $foo && $bar )
   <strong> This AND that</strong>
#end
```

*#if()* 指令结果为true 假如 *$foo* 和*$bar*都为true. 如果 *$foo*为false,表达式的结果为 false; *$bar*将不会计算. 如果 *$foo* 为 true,Velocity模板将计算*$bar*的值; 如果 *$bar*为 true, 然后整个表达式为true输出 **This AND that** . 如果 *$bar*为 false,如果表达式为false这里将没有输出.

逻辑表达OR 操作符也是这种情况,仅仅只有一种情况需要计算总个表达式,就是整个表达式为true.思考下面的实例.

```none
## logical OR
#if( $foo || $bar )
    <strong>This OR That</strong>
#end
```

如果*$foo*为 true, Velocity模板不需要计算*$bar*; 不管*$bar* 是否为 true 或 false, 这整个表达式为true,**This OR That** 将被输出. 如果 *$foo* 为 false, 然而, *$bar*必须需要计算. 在这个实例中, 如果 *$bar* 也为 false,这表达式结果为 false 这里没有任何输出. 另一方面, 如果*$bar* 为 true, 然而整个表达式为true, 输出的结构是 **This OR That**

逻辑运算符NOT ,这里仅仅有一个参数 :

```none
##logical NOT
#if( !$foo )
  <strong>NOT that</strong>
#end
```

这里, 如果*$foo*为 true, 然后*!$foo*计算结果为false,这里没有输出。如果 *$foo*为 false, 然而 *!$foo*计算结果为true 输出结果为 **NOT that** . 注意不要把它和静态引用 *$!foo* which混淆，它们是不同的。*（ifeve.com校对注：一个!在前，一个在后）*

这里也有字符形式的逻辑运算符如 *eq*, *ne*, *and*, *or*, *not*, *gt*, *ge*, *lt*, 和*le*.

温馨提示.当你希望在*#else* 指令后面包含一个文本你需要把else放在一个大括号 里面来表示它与后面的文本是不同的. (任何指令都能被放在括号里面, 通常用在*#else*中)

```none
#if( $foo == $bar)it's true!#{else}it's not!#end</li>
```

## 循环

**Foreach Loop**The *#foreach*元素用来循环操作. 例如:

```none
<ul>
#foreach( $product in $allProducts )
    <li>$product</li>
#end
</ul>
```

*#foreach*循环将把 *$allProducts*列表里面的值赋值给products . 每次遍历, *$allProducts* 里面的值将赋值给*$product*.

*$allProducts* 变量的内容是一个数组, Hashtable 或者 Array. 分配给*$product* 变量的值是Java的对象和一个变量的引用. 例如, 假如 *$product*是Java里面一个真实的Product类, 它的名字能够被引用 *$product.Name* 方法(ie: *$Product.getName()*).

比方说 *$allProducts* 是一个Hashtable. 假如你想引用Hashtable 里面的key, 你的代码可以这样写:

```none
<ul>
#foreach( $key in $allProducts.keySet() )
    <li>Key: $key -> Value: $allProducts.get($key)</li>
#end
</ul>
```

Velocity提供了一种简单的循环计数以至于你能够做一些事情,如下所示:

```none
<table>
#foreach( $customer in $customerList )
    <tr><td>$foreach.count</td><td>$customer.Name</td></tr>
#end
</table>
```

Velocity也提供了一种简单的方式来判断是否是最后一次迭代 :

```none
#foreach( $customer in $customerList )
    $customer.Name#if( $foreach.hasNext ),#end
#end
```

如果你想从零开始的#foreach循环, 你可以使用 $foreach.index 代替$foreach.count. 同样的, $foreach.first 和 $foreach.last 也提供了$foreach.hasNext方式.如果你想访问 #foreach外面的这些属性, 你能够引用它们通过 $foreach.parent或 $foreach.topmost 属性 (e.g. $foreach.parent.index 或者 $foreach.topmost.hasNext).

你也可以设置最大的循环执行次数. 默认情况下没有设置 (可以指定一个值 0 或者更小的), 可以设置一个数字在velocity.properties 的配置文件里面. This is useful as a fail-safe.

```none
# The maximum allowed number of loops.
directive.foreach.maxloops = -1
```

如果你想停止一个foreach循环在你的模板中, 你可以使用 #break指令在任何时候停止循环:

```none
## list first 5 customers only
#foreach( $customer in $customerList )
    #if( $foreach.count > 5 )
        #break
    #end
    $customer.Name
#end
```

## 引入

*#include*脚本元素准许设计者引入一个本地文件, 然后插入到你#include 指令所在的地方。文件的内容不经过模板引擎处理. 由于安全的原因,这个文件仅仅能够放在 TEMPLATE_ROOT下面.

```none
#include( "one.txt" )
```

*#include* 指定指向的文件放在括号之内. 假如包含多个文件,通过逗号进行分开.

```none
#include( "one.gif","two.txt","three.htm" )
```

被引用的文件不需要通过名字引用; 实际上，通常使用一个变量来代替文件名。当你请求输出标准页面时非常方便。下面实例展示了文件名和变量.

```none
#include( "greetings.txt", $seasonalstock )
```

## 解析

*#parse*脚本元素准许模板设计者引用一个包含VTL的本地文件。Velocity将解析其中的VTL并输出里面的元素。

```none
#parse( "me.vm" )
```

向 *#include*指令, *#parse*可以被看着携带一个变量而不是模板. 任何被 *#parse* 指令引用的模板必须放在TEMPLATE_ROOT下面. 不像 *#include*指令, *#parse* 仅仅携带了一个参数.

VTL模板中*#parse*应用的文件能够够嵌套引用包含*#parse*的模板. 默认为 10, 用户可以通过velocity.properties 中directive.parse.max.depth来自定义设置单个文件中*#parse*引用文件个数. (注意: 如果 velocity.properties文件中*directive.parse.max.depth*没有设置, Velocity默认为10.) 准许递归调用, 例如,如果dofoo.vm 模板包含下面的语句:

```none
Count down.
#set( $count = 8 )
#parse( "parsefoo.vm" )
All done with dofoo.vm!
```

如果你引用parsefoo.vm模板, 它可以包含下面的VTL文件:

```none
$count
#set( $count = $count - 1 )
#if( $count > 0 )
    #parse( "parsefoo.vm" )
#else
    All done with parsefoo.vm!
#end
```

“Count down.” 之后的被显示, Velocity能够解析parsefoo.vm, 把count设置为8. 当count大于0, 它将显示 “All done with parsefoo.vm!” . 本实例中, Velocity将返回 dofoo.vm 并输出 “All done with dofoo.vm!” 语句.

## Break

*#break*指令停止任何正在执行的作用域中的输出. 执行作用域是本质上是 with content (i.e. #foreach, #parse, #evaluate, #define, #macro, 或者 #@somebodymacro) 或者 任何”root” 作用域(例如. template.merge(…), Velocity.evaluate(…) or velocityEngine.evaluate(…)). 不像#stop, #break 它仅仅停止的是循环内部, 作用域内部, 而不是整个程序.

如果你希望停止一个正在执行的作用域, 你可以使用作用域控制引用(例如. $foreach, $template, $evaluate, $define, $macro, or $somebodymacro) 作为一个参数#break. (例如. #break($macro)). 它将停止输出所有的到指定的一个. 在同一类型的嵌套范围内, 你能够访问它的父亲通过 $<scope>.parent 或者 $<scope>.最高的和传递这些到#break 代替(例如. #break($foreach.parent) o或者 #break($macro.topmost)).

## Stop指令

*#stop* 指令停止任何正在执行的输出。这是真实的即使嵌套另一个模板通过 #parse 指令或者位于一个velocity macro. 合并输出将包括所有的内容到遇到 #stop 指令的位置. 它是很方便的随时终止模板解析.出入调试的目的, 你可以提供一个将被写到stop命令执行前日志 (DEBUG 级别, 当然是)消息参数 (例如. #stop(‘$foo 并不在内容中’) ).

## Evaluate指令

*#evaluate*指令用来动态的计算。它可以在加载的时候适时计算一个字符串. 例如一个字符串可能被用来国家化或则包括一部分数据库模板.

下面的实例将显示abc.

```none
#set($source1 = "abc")
#set($select = "1")
#set($dynamicsource = "$source$select")
## $dynamicsource is now the string '$source1'
#evaluate($dynamicsource)
```

## Define指令

*#define*指令可以指定对VTL一个块的引用.

下面的实例将显示: Hello World!.

```none
#define( $block )Hello $who#end
#set( $who = 'World!' )
$block
```

## 宏调用

*#macro* 脚本元素准许模板设计者定义一个可重用的VTL模板片段. Velocimacros(宏调用)被广泛用在简单和复杂的场景中.Velocimacro可以用来保存用户的点击次数和最少的板式错误, 提供一个简单的概念介绍。

```none
#macro( d )
<tr><td></td></tr>
#end
```

上个实例中Velocimacr被定义为*d*, 它可以像其他VTL方式一样被调用:

```none
#d()
```

当*#d()*被调用的时, Velocity将用定义的一行内容来替换*#d()*.如果你想在行里面添加内容, 你也可以向下面这样来改变macro的定义:

```none
#macro( d )
<tr><td>$!bodyContent</td></tr>
#end
```

现在, 如果你想调用macro会和之前有点不同, 名字之前需要加上#@ 加上一个内容体,同时以#end调用, 当Velocity加载时遇见 $!bodyContent会进行显示:

```none
#@d()Hello!#end
```

如果你一直坚持像之前那种方式调用, 我们使用隐形的引用代替body引用($!bodyContent 代替$bodyContent), 它将一直显示一个空的单元格.

Velocimacro也可以携带许多参数 — 甚至是不带参数, 如同第一个实例中, 参数是可选项– 但是当Velocimacro执行时, 它必须调用和你定义的方法的参数匹配. 被执行的Velocimacros要多于上面定义的. 下面实例所示一个Velocimacro携带两个参数一个是color和一个数组.

```none
#macro( tablerows $color $somelist )
#foreach( $something in $somelist )
    <tr><td bgcolor=$color>$something</td></tr>
#end
#end
```

上面的Velocimacro实例中, 定义一个*tablerows*,带两个参数. 第一个参数是*$color*, 第二个参数是 *$somelist*.

任何可以被放到VTL模板中都可以作为Velocimacro的函数体.  *tablerows* 是Velocimacro的一个 *foreach*语句. 这里有两个 *#end*语句被定义在*#tablerows*中; 第一个是属于 *#foreach*, 第二个是属于Velocimacro .

```none
#set( $greatlakes = ["Superior","Michigan","Huron","Erie","Ontario"] )
#set( $color = "blue" )
<table>
    #tablerows( $color $greatlakes )
</table>
```

注意*$greatlakes* 代替 *$somelist*. 当 *#tablerows*被调用时, 将输出以下内容:

```none
<table>
    <tr><td bgcolor="blue">Superior</td></tr>
    <tr><td bgcolor="blue">Michigan</td></tr>
    <tr><td bgcolor="blue">Huron</td></tr>
    <tr><td bgcolor="blue">Erie</td></tr>
    <tr><td bgcolor="blue">Ontario</td></tr>
</table>
```

Velocimacros能够定义在Velocity模板的行内，意味着在同一个网页上它不可以使用其他的Velocity模板。定义一个Velocimacro并在所有模板中共享,这使它具有明显的优势: 它避免了在各个页面中重复定义Velocimacro, 减少工作量并且可以避免错误, 这种也可以通过改变一个地方而达到其他地方一起修改的目的.

*#tablerows($color $list)* Velocimacro 被定义在 Velocimacros 模板的依赖包中, 它可以保证在其他地方被大量使用. 它能够被多次重复使用来解决不同的问题. 在模板 mushroom.vm 中专注与正真的事情,*#tablerows* Velocimacro 能够被遍历,显示所有部分:

```none
#set( $parts = ["volva","stipe","annulus","gills","pileus"] )
#set( $cellbgcol = "#CC00FF" )
<table>
#tablerows( $cellbgcol $parts )
</table>
```

当你请求mushroom.vm, Velocity 将在模板依赖包中查询*#tablerows* (在 velocity.properties 定义的) 并输出下面内容:

```none
<table>
    <tr><td bgcolor="#CC00FF">volva</td></tr>
    <tr><td bgcolor="#CC00FF">stipe</td></tr>
    <tr><td bgcolor="#CC00FF">annulus</td></tr>
    <tr><td bgcolor="#CC00FF">gills</td></tr>
    <tr><td bgcolor="#CC00FF">pileus</td></tr>
</table>
```

**Velocimacro Arguments**Velocimacros可以携带下面的VTL元素作为参数 :

- Reference : anything that starts with ‘$’
- String literal : something like “$foo” or ‘hello’
- Number literal : 1, 2 etc
- IntegerRange : [ 1..2] or [$foo .. $bar]
- ObjectArray : [ “a”, “b”, “c”]
- boolean value true
- boolean value false

当传递一个引用作为Velocimacros的参数时,注意引用是通过名字传递的. 这意味着它们的值是在Velocimacro中已经生成了. 这个特性准许你传递一个方法的调用引用在方法中也可以使用方法调用. 例如, 当我们调用下面的Velocimacro将显示

```none
     #macro( callme $a )
         $a $a $a
     #end
     #callme( $foo.bar() )
```

在方法中 bar() 的结果将被调用三次.

第一次看到这个特性会发现很惊奇, 当你仔细的思考Velocimacros背后的动机–消除VTL中公共的重复使用 –它变的有意义. 它准许你做一些事情,比如传递一个对象的状态, 例如在Velocimacro中重复生成有颜色的表格对象.

如果你不想使用这个特性,你就想从一个引用的方法中获取值,你可以这样做 :

```none
     #set( $myval = $foo.bar() )
     #callme( $myval )
```

**Velocimacro Properties**在 velocity.properties 文件中准许灵活的实现Velocimacros. 这里也提供了开发者文档[Developer Guide](http://velocity.apache.org/engine/devel/developer-guide.html).

velocimacro.library – 一个通用的和其他依赖包分开的 Velocimacro模板依赖包. 默认, Velocity看起来想一个单独的 函数库: *VM_global_library.vm*. 配置模板的路径用来使用找到Velocimacro 函数库.

velocimacro.permissions.allow.inline – 这个属性的值可以是true或者false, 决定是否可以在模板中定义Velocimacros. 默认情况是, true, 准许模板设计者可以自行定义Velocimacros.

velocimacro.permissions.allow.inline.to.replace.global -它的值可以设定为true或者false, 这个设置准许用户指定一个模板中定义的Velocimacro能代替全局定义, 它在启动的时候被 velocimacro.library属性加载. 默认是, false, 避免在启动时候模板中定义的Velocimacros成为全局Velocimacros.

velocimacro.permissions.allow.inline.local.scope – 这个属性可能值为true 或者 false, 默认为 false, 控制 定义的Velocimacros对定义中的模板是可见的.简而言之, 如果这个属性设置为 true, 一个模板能够定义能够被定义的模板使用的VMs内 . You can use this for fancy VM tricks – if a global VM calls another global VM, 在行内的作用域内, 一个模板能够定义一个私有的实现第二个VM,它能被第一个VM调用. 但是对其他模板没有影响.

velocimacro.library.autoreload – 这个属性控制Velocimacro依赖包自动加载. 这个默认是的是false.当把属性设置为 true,当Velocimacro被执行时它将检查Velocimacro是否改变, 它重载很有必要的.它准许你改变和测试Velocimacro 不需要重庆你的应用和服务器容器, 仅仅像你可以用普通的模板. 这个模式需要关闭缓存才起作用(e.g.file.resource.loader.cache = false ). 这个特征主要用于开发模式, 而不是生产模式.

## 获取字面量

VTL使用了特殊字符,例如 *$* 和 *#*, 做为关键字, 所以一些使用者关注在模板中怎么使用这些字符.这章将讨论转义字符 .

**Currency(通用)**
你这样写是没有问题的 “I bought a 4 lb. sack of potatoes at the farmer’s market for only $2.50!” 像上面提到的一样, VTL 标识符一直都是以大写或者小写的字母开始的, 所以 $2.50 不会被错误引用.

**Escaping Valid VTL References(屏蔽VTL中的引用)**
你可能遇到这种情况,在Velocity中不想有一个引用输出. 屏蔽特殊的字符是输出特殊字符的最好的方式, 你可以使用反斜杠字符,当遇见这些特殊的字符是VTL引用的一部分时. [*](http://velocity.apache.org/engine/devel/user-guide.html#escapinginvalidvtlreferences)

```
#set( $email = "foo" )
$email
```

假如在你的Velocity 模板中遇到一个 *$email*,它将在上下文中查询并输出正确的值. 上实例中将输出 *foo*, 因为*$email* 已经被定义了.如果 *$email*is没有被定义, 它将输出*$email*.

加入 *$email* 已经定义了 (例如, 它的值为 *foo*), 但是你想输出 *$email*. 这里有几种方式可以实现, 最简单的方式是使用转义字符. 请看下面实例:

```none
## The following line defines $email in this template:
#set( $email = "foo" )
$email
\$email
```

输出

```none
foo
$email
```

如果,由于某种原因, 你需要在它之前添加一个反斜杠, 你需要按照下面实例做:

```none
## The following line defines $email in this template:
#set( $email = "foo" )
\\$email
\\\$email
```

输出结果

```none
\foo
\$email
```

注意 *\* 字符绑定到*$* 左边的字符. 左边绑定规则导致 *\\\$email* 输出为*\$email*. 比较下面实例看看*$email*是否被定义.

```none
$email
\$email
\\$email
\\\$email
```

输出

```none
$email
\$email
\\$email
\\\$email
```

注意Velocity定义的引用不同于那些没有定义的引用 . 本实例中*$foo* 定义值为*gibbous*.

```none
#set( $foo = "gibbous" )
$moon = $foo
```

输出结果为: *$moon = gibbous* — *$moon*将作为字符输出而$foo的地方将输出*gibbous*.

**Escaping Invalid VTL References(转义不可用VTL引用)**
有时候你可能会遇到这种情况,当你的Velocity解析你的模板时遇见了一个你从来都没有打算把它当着引用的不可用的引用. 屏蔽特殊的字符, 最好的方式是你能够处理把握这种情况,在这种情况下, 反斜杠可能会失败. 你可以使用简单的方式来屏蔽关于 $ 或 #问题, 你可能仅仅是替换一下:

```none
${my:invalid:non:reference}
```

可以想这样

```none
#set( $D = '$' )
${D}{my:invalid:non:reference}
```

你也可以通过Java代码把  $ 或 #字符串指令放到你的文本中  (例如 context.put("D","$");)来避免在你的模板中添加额外的 #set() 指令. 或者, 你也可以使用[VelocityTools](http://velocity.apache.org/tools/devel/), 你可以使用EscapeTool:

```none
${esc.d}{my:invalid:non:reference}
```

有效的和无效的VTL 指令使用同样的方式进行转义的; 在指令章节将对此进行详细描述.

**Escaping VTL Directives(转义VTL指令)**
VTL 指令能够被屏蔽通过反斜杠(“\”) 字符和疲敝VTL引用方式是一样的.

```none
## #include( "a.txt" ) renders as <contents of a.txt>
#include( "a.txt" )
## \#include( "a.txt" ) renders as #include( "a.txt" )
\#include( "a.txt" )
## \\#include ( "a.txt" ) renders as \<contents of a.txt>
\\#include ( "a.txt" )
```

需要特别关心的是转义在一个单独的指令中包含多个脚本元素VTL 指令(例如在一个 if-else-end语句). 这里是一个典型的VTL if语句:

```none
#if( $jazz )
    Vyacheslav Ganelin
#end
```

如果 *$jazz*为 true, 输出结果为

```none
Vyacheslav Ganelin
```

如果*$jazz* 为false,这里没有任何输出. Escaping 脚本元素修改输出结果. 考虑下面实例:

```none
\#if( $jazz )
    Vyacheslav Ganelin
\#end
```

它将导致指令被忽略, 但是变量 *$jazz* 正常显示. 所以, 如果*$jazz* 为 true,输出结果是

```none
 #if( true )
     Vyacheslav Ganelin
 #end
```

如果脚本之前的元素被反斜杠转义:

```none
\\#if( $jazz )
   Vyacheslav Ganelin
\\#end
```

在本实例中, 如果*$jazz* 为 true,输出为

```none
\ Vyacheslav Ganelin
\
```

为了了解这一点,注意 #if( arg ) 如果在新的一行结束时,输出时将省略这一行. 另外,  #if() 的语句块将紧跟着 ‘\’,  在#if()之前加入两个'\\'. 最后的 \和文本在不同的行因为在 ‘Ganelin’之后有一个新行, 所以最终\\, #end 之前的部分是程序体.

如果*$jazz*是 false, 输出结果为

```none
\
```

注意一些执行将被中断如果元素没有属性被转义.

```none
\\\#if( $jazz )
    Vyacheslave Ganelin
\\#end
```

这里 *#if* 被转义, 但是这里保留一个 *#end* 标签; 多余的标签将导致解析错误.

## 格式化问题

虽然VTL的用户使用指南中经常显示换行符和空格,VTL显示如下

```none
#set( $imperial = ["Munetaka","Koreyasu","Hisakira","Morikune"] )
#foreach( $shogun in $imperial )
    $shogun
#end
```

上面实例和下面实例是等效的, Geir Magnusson Jr. 写给用户的邮件来说明这点完全无关:

```none
Send me #set($foo=["$10 and ","a pie"])#foreach($a in $foo)$a#end please.
```

Velocity可以去掉多余的空格. 前面的指令可以写成:

```none
Send me
#set( $foo = ["$10 and ","a pie"] )
#foreach( $a in $foo )
$a
#end
please.
```

输出

```none
Send me
#set($foo       = ["$10 and ","a pie"])
                 #foreach           ($a in $foo )$a
         #end please.
```

在这些实例中输出结果将是一样的.

## 其他的特性和杂记

### Math(算术操作)

Velocity中内置了一个算术函数,这些函数你可以在VTL模板中进行调用 .下面的实例分别展示加减乘数的使用:

```none
#set( $foo = $bar + 3 )
#set( $foo = $bar - 4 )
#set( $foo = $bar * 6 )
#set( $foo = $bar / 2 )
```

当两个整数相除时,结果是整数, 小数部分被丢弃. 模板中也可以使用求模运算符 (*%*).

```none
#set( $foo = $bar % 5 )
```

### 范围操作符

范围操作符能够结合着 *#set* 和 *#foreach* 语句使用. 用它能够产生一个证书数组,它的语法结果如下:

```none
[n..m]
```

产生一个n到m的整数. 不论m大于或者小于n都不重要;在这个实例中这个范围就是简单的计算.下面的实例展示了范围操作符的使用:

```none
第一个实例:
#foreach( $foo in [1..5] )
$foo
#end

第二个实例:
#foreach( $bar in [2..-2] )
$bar
#end

第三个实例:
#set( $arr = [0..1] )
#foreach( $i in $arr )
$i
#end

第四个实例:
[1..3]
```

输出结果为:

```none
第一个实例:
1 2 3 4 5

第二个实例:
2 1 0 -1 -2

第三个实例:
0 1

第四个实例:
[1..3]
```

注意范围操作符仅仅产生数组当结合 *#set* 和 *#foreach* 指令, 正如在第四个例子中展示的.

网页设计者关系一个制作标准大小的表格, 有些情况下没有足够的数据填充表格, 将发现范围操作符特别有用.

### 高级问题:转义和!

当一个引用前面加上*!* 字符和*!* 字符前面加上一个 *\* 转义字符, 这种引用需要一种特殊的处理方式. 注意正规的转义符和特殊的\之后紧跟 *!*实例的不同:

```none
#set( $foo = "bar" )
$\!foo
$\!{foo}
$\\!foo
$\\\!foo
```

输出结果为:

```none
$!foo
$!{foo}
$\!foo
$\\!foo
```

对比这个正规 *\* 优先*$*的转义:

```none
\$foo
\$!foo
\$!{foo}
\\$!{foo}
```

输出结果为:

```none
$foo
$!foo
$!{foo}
\bar
```

### 杂记

这一章节是关于Velocimacros一些小疑问.本章节以后还会经常改变 , 所以它很值得你关注.

Note : 贯穿整个章节, ‘Velocimacro’ 将被简写成 ‘VM’.

**我可以使用一个指令或者一个可以用的指令参数吗?**例如 : #center( #bold("hello") )

No. 一个指令不是一个可用的指令参数, 出于实用的目的, 一个VM是一个指令.

*然而…*, 这里有很多事情你需要做. 一个容易的解决方案是利用编译器 ‘doublequote’ (“) 输出显示它的内容. 你可以想下面方式来做

```none
#set($stuff = "#bold('hello')" )
#center( $stuff )
```

输出结果为

```none
#center( "#bold( 'hello' )" )
```

在这个实例中参数是在VM中计算的,而不是在调用时 . 简而言之, the argument to the VM的参数传递到VM中进行计算 . 它准许你这么操作:

```none
#macro( inner $foo )
  inner : $foo
#end
#macro( outer $foo )
   #set($bar = "outerlala")
   outer : $foo
#end
#set($bar = 'calltimelala')
#outer( "#inner($bar)" )
```

输出结果为

```none
Outer : inner : outerlala
```

因为 “#inner($bar)” 的计算发生在 #outer()的内部, 所以 $bar 的值是在 #outer() 内部进行赋值的.

这是一种有意的保护功能 – 参数通过名字传递到VMs, 所以你能够传递VMs像状态引用,例如

```none
#macro( foo $color )
  <tr bgcolor=$color><td>Hi</td></tr>
  <tr bgcolor=$color><td>There</td></tr>
#end
#foo( $bar.rowColor() )
```

rowColor()被重复调用,而不是仅仅调用一次.为了避免这种情况, 在VM外面执行方法, 并把值传递给VM.

```none
#set($color = $bar.rowColor())
#foo( $color )
```

**我可以注册一个宏变量 #parse() 吗?**

 **可以在 Velocity 1.6 中实现**

如果你使用之前的版本, 你的Velocimacros必须第一次使用之前就已经被定义 .它的意思是有的当你使用  #macro()之前都已经被定义了.

这是很重要的如果你试图 #parse() 一个包含#macro() 指令模板. 因为 #parse() 在运行时才被调用,解析时 解析器决定是否在你的模板中 VM-looking 元素是一个 VM, #parse()-ing 设置 VM 声明并不向期望那样工作. 为了避开这一点, 可以使用 velocimacro.library来使Velocity启动时重新加载VMs.

**什么是宏自动加载?**

这个属性用在开发模式下,  而不是生成环境 :

velocimacro.library.autoreload

默认是false. 当设置为true时

<type>.resource.loader.cache = false

（<type> 是你使用资源的名字, 例如 ‘file’) 然后Velocity将自动加载已经修改的Velocimacro依赖包文件当你配置该选项时, 所以你不比清空你的服务器(或者引用的)或者通过其他方式来是Velocimacros重载.

这里是一个简单的配置文件设置.

```none
file.resource.loader.path = templates
file.resource.loader.cache = false
velocimacro.library.autoreload = true
```

在生产环境不能使用这种配置.

### 字符串的拼接

开发者经常会问一个很普通的问题,就是怎么去拼接两个字符串?是不是类似于Java里面的’+’*?.* 

为了实现在VTL中两个字符串的拼接VTL, 你仅仅需要把它们放在一起. 你需要把他们放在一起当你需要的时候, 请看下面实例.

在一个有规则的 ‘schmoo’ 模板 (当你添加一个有规则的内容时) :

```none
#set( $size = "Big" )
#set( $name = "Ben" )

The clock is $size$name.
```

输出结果为 ‘The clock is BigBen’. 这里有更多有趣的实例, 例如当你想拼接字符串并传递给一个方法时, 或者定义一个新的引用,你只需要这样做

```none
#set( $size = "Big" )
#set( $name = "Ben" )

#set($clock = "$size$name" )

The clock is $clock.
```

它们的输出结果是一样的. 作为最后一个实例, 当你想在你的引用中添加一个 ‘static’字符串时 , 你需要使用正式的引用 :

```none
#set( $size = "Big" )
#set( $name = "Ben" )

#set($clock = "${size}Tall$name" )

The clock is $clock.
```

现在输出的是 ‘The clock is BigTallBen’。正式引用解析能够把 ‘$size’ 和’$sizeTall’ 通过 ‘{}’ 进行区分开.