# 9、动态SQL

> Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题。

空串和null的区别

```none
空串:提交,但是里面是空内容
	eg.网页中空着的表格
null:没有提交的网页元素,但是服务器通过某种方式获取,里面什么东西都没有
	eg.未选中的单选框/多选框
```

## 9.1、if

> if标签可通过test属性的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之
>
> 标签中的内容不会执行

```xml
<!--List<Emp> getEmpListByCondition(Emp emp);-->
<select id="getEmpListByMoreTJ" resultType="Emp">
    select * from t_emp where 1=1
    <!--
		if,通过test判断当前标签中的内容是否有效
		(是否会被拼接到sql中)
		test:判断当前的条件
		标签中不需要写#/$,直接写属性名就可以获取
	-->
    <if test="ename != '' and ename != null">
		and ename = #{ename}
	</if>
	<if test="age != '' and age != null">
		and age = #{age}
	</if>
	<if test="sex != '' and sex != null">
		and sex = #{sex}
	</if>
</select>
```

## 9.2、where

> where和if一般结合使用：
>
> a>若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字
>
> b>若where标签中的if条件满足，则where标签会自动添加where关键字，并将if条件最前方多余的and去掉
>
> Emp(null,"阿梓",24,"女")=>`select * from t_emp where ename=#{ename} and age=#{age} and sex=#{sex} `
>
> Emp(null,null,24,"女")=>`select * from t_emp where age=#{age} and sex=#{sex} `
>
> Emp(null,null,'',"女")=>`select * from t_emp where sex=#{sex} `
>
> Emp(null,null,'',"")=>`select * from t_emp ` ==此时没有where==
>
> 注意：where标签不能去掉if条件内容中最后多余的and

```xml
<select id="getEmpListByMoreTJ2" resultType="Emp">
	select * from t_emp
	<where>
		<if test="ename != '' and ename != null">
			ename = #{ename}
		</if>
		<if test="age != '' and age != null">
			and age = #{age}
		</if>
		<if test="sex != '' and sex != null">
			and sex = #{sex}
		</if>
	</where>
</select>
```

> where和if标签用于解决多条件查询的sql语句拼接问题

## 9.3、trim

> trim用于去掉或添加标签中的内容
>
> 常用属性：
>
> prefix：在trim标签中的内容的前面添加某些内容
>
> prefixOverrides：在trim标签中的内容的前面去掉某些内容
>
> suffix：在trim标签中的内容的后面添加某些内容
>
> suffixOverrides：在trim标签中的内容的后面去掉某些内容

```xml
<select id="getEmpListByMoreTJ" resultType="Emp">
	select * from t_emp
	<trim prefix="where" suffixOverrides="and">
		<if test="ename != '' and ename != null">
			ename = #{ename} and
		</if>
		<if test="age != '' and age != null">
			age = #{age} and
		</if>
		<if test="sex != '' and sex != null">
			sex = #{sex}
		</if>
	</trim>
</select>
```

可以理解为`where`就是通过`trim`实现的

## 9.4、choose、when、otherwise

> choose、when、 otherwise相当于if...(else if)..else
>
> when和otherwise是子标签,需要写在choose副标签里面
>
> 在where中只会加入==筛选==的一个条件,有一个判断成功其余都不判断
>
> when至少设置一个,otherwise最多设置一个

```xml
<!--List<Emp> getEmpListByChoose(Emp emp);-->
<select id="getEmpListByChoose" resultType="Emp">
	select <include refid="empColumns"></include> from t_emp
	<where>
		<choose>
			<when test="ename != '' and ename != null">
				ename = #{ename}
			</when>
			<when test="age != '' and age != null">
				age = #{age}
			</when>
			<when test="sex != '' and sex != null">
				sex = #{sex}
			</when>
			<when test="email != '' and email != null">
				email = #{email}
			</when>
		</choose>
	</where>
</select>
```

## 9.5、foreach

标签的属性

```
collection:设置当前要循环的集合
item:选项,集合元素,使用一个字符串表示集合或者数组中的一个元素
separator:每次循环的数据之间的分隔符
open:循环的数据以什么开始
close:循环的数据以什么结束
```

```
List<Emp> emps
默认mybatis将其映射在list中
```

```xml
<!--批量添加:int insertMoreEmp(@Param("emps")List<Emp> emps);-->
<insert id="insertMoreEmp">
	insert into t_emp values
	<foreach collection="emps" item="emp" separator=",">
		(null,#{emp.ename},#{emp.age},#{emp.sex},#{emp.email},null)
	</foreach>
    <!--
	insert into t_emp values (),(),()
	-->
</insert>

<!--批量删除int deleteMoreByArray(@Param("eids")int[] eids);-->
<delete id="deleteMoreByArray">
	delete from t_emp where
	<foreach collection="eids" item="eid" separator="or">
		eid = #{eid}
	</foreach>
    <!--
	delete from t_emp where eid=1 or eid=2
	-->
</delete>

<!--int deleteMoreByArray(int[] eids);-->
<delete id="deleteMoreByArray">
	delete from t_emp where eid in
	<foreach collection="eids" item="eid" separator="," open="(" close=")">
		#{eid}
	</foreach>
    <!--
	delete from t_emp where eid in(1,2,3)
	-->
</delete>
```

`delete ... where ... in()`可以写成

```xml
<!--int deleteMoreByArray(int[] eids);-->
<delete id="deleteMoreByArray">
	delete from t_emp where eid in
	(
	<foreach collection="eids" item="eid" separator=",">
		#{eid}
	</foreach>
    )
</delete>
```

## 9.6、SQL片段

> sql片段，可以记录一段公共sql片段，在使用的地方通过include标签进行引入

```xml
<sql id="empColumns">
	eid,ename,age,sex,did
</sql>
select <include refid="empColumns"></include> from t_emp
```
