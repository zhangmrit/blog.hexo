title: mybatis 单参数不加@Param 报没有getter
date: 2020-03-11 13:47:16
categories: mybatis
tags: [mybatis,mapper]
---
`There is no getter for property named '*' in 'class java.lang.String'`，此错误之所以出现，是因为mybatis在对`parameterType="String"`的sql语句做了限制，假如你使用`<if test="username != null">`或`<when test="username != null">`这样的条件判断时，就会出现该错误，不过今天我们来刨根问底一下。
<!-- more -->

## 一、错误再现
想要追本溯源，就需要错误再现，那么假设我们有这样一个sql查询：

```
<select id="getObj" resultMap="BaseResultMap" parameterType="String">
	<include refid="selectVo"/>
	<where>
		<if test="username != null">
			and username = #{username} 
		</if>
	</where>
 </select>
```
1. `parameterType="String"`，这一点是必须得，参数类型必须是string。
2. 该sql对应的mapper class中对应的方法为`getObj(String username)`，也就是说，传递的参数名为`username`，正常情况下，这样的配置合情合理。
3. `<if test="username != null">`，你有一个对应的`test`判断语句，也可能是`when`。
4. 那么这个时候，项目运行该查询语句时，就会抛出`There is no getter for property named 'username' in 'class java.lang.String'`错误！


## 二、解决办法
当然了，如果你没有时间来看源码分析实例的话，我想先告诉你解决办法，免得你被问题困扰。解决办法很简单，你只需要把 `<if test="username != null">`修改为 `<if test="_parameter!= null">`就好了，其他地方不需要改动（也就是说`and username = #{username}`不需要改动为`and username = #{_parameter}`），修改后的sql语句如下：

```
<select id="getObj" resultMap="BaseResultMap" parameterType="String">
	<include refid="selectVo"/>
	<where>
		<if test="_parameter != null">
			and username = #{username} 
		</if>
	</where>
 </select>
```
## 三、源码分析
用maven下载源码，定位 `org.apache.ibatis.session.defaults.DefaultSqlSession`L 145上下方法名`selectList(String statement, Object parameter, RowBounds rowBounds)`
代码展示：

```
  @Override
  public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
    try {
      MappedStatement ms = configuration.getMappedStatement(statement);
      return executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }
```
断点可以看到Exception的内容为开头报的错

```
org.mybatis.spring.MyBatisSystemException: nested exception is org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'username' in 'class java.lang.String'
```


原因就是此时变量是没有绑定的，也就是没有变量为`username`的东西，只有两个内置对象`_parameter`, `_databaseId`

其实这里你可以发现，取值也根本用不到`#{username}`,随便用个变量就能拿到`#{value}`