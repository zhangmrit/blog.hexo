title: 用markdown来写api
date: 2016-06-15 14:54:28
categories: 工具
tags: [markdown]
---
一直在找寻最合适的方法来实现api接口文档的书写，直达发现了markdown,虽然也用markdown，却从来没想过可以这么优雅地来写api哈。那么开始get新技能吧。
<!-- more -->

##接口文档

[TOC]

***

### 查询指定项目属性
 **接口功能**
> 获取制定项目的分类信息

 **URL**
> http://www.api.com/index.php

**请求参数**
> |参数|必选|类型|说明|
|:-----  |:-------|:-----|-----                               |
|name    |ture    |string|请求的项目名                          |
|type    |true    |int   |请求项目的类型。1：类型一；2：类型二 。|

**返回字段**
> |返回字段|字段类型|说明                              |
|:-----   |:------|:-----------------------------   |
|status   |int    |返回结果状态。0：正常；1：错误。   |
|company  |string | 所属公司名                      |
|category |string |所属类型                         |

**接口示例**
> 地址：[http://www.api.com/index.php?name="可口可乐"&type=1](http://www.api.com/index.php?name="可口可乐"&type=1)
``` javascript
{
    "statue": 0,
    "company": "可口可乐",
    "category": "饮料",
}
```

下面是源代码
```
##接口文档

[TOC]

***

### 查询指定项目属性
 **接口功能**
> 获取制定项目的分类信息

 **URL**
> http://www.api.com/index.php

**请求参数**
> |参数|必选|类型|说明|
|:-----  |:-------|:-----|-----                               |
|name    |ture    |string|请求的项目名                          |
|type    |true    |int   |请求项目的类型。1：类型一；2：类型二 。|

**返回字段**
> |返回字段|字段类型|说明                              |
|:-----   |:------|:-----------------------------   |
|status   |int    |返回结果状态。0：正常；1：错误。   |
|company  |string | 所属公司名                      |
|category |string |所属类型                         |

**接口示例**
> 地址：[http://www.api.com/index.php?name="可口可乐"&type=1](http://www.api.com/index.php?name="可口可乐"&type=1)

``` javascript
{
    "statue": 0,
    "company": "可口可乐",
    "category": "饮料",
}
```
最后的```自己补上

但是markdown还是有缺陷，比如toc问题，兼容性问题等。
最佳效果是wiki配上markdown，这样内部沟通问题很好解决。
