# 7、特殊SQL的执行

## 7.1、模糊查询

```sql
SELECT * FROM t_user WHERE `username` LIKE '%a%';
```

有模糊查询如上,`%`表示任意个字符

```
1	admin	123456	23	男	12345@qq.com
3	admin1	123456	23	男	12345@qq.com
4	Aoidayo	201741	21	男	2908496836@qq.com
```

```java
/**
* 测试模糊查询
* @param mohu
* @return
*/
List<User> testMohu(@Param("mohu") String mohu);
```

```xml
<!--List<User> testMohu(@Param("mohu") String mohu);-->
<select id="testMohu" resultType="User">
    <!--select * from t_user where username like '%${mohu}%'-->
    <!--select * from t_user where username like concat('%',#{mohu},'%')-->
	select * from t_user where username like "%"#{mohu}"%"
</select>
```

为什么不能使用`#{}`

> 因为`#`是占位符替换
>
> ```sql
> select * from t_user where username like '%#{mohu}%'
> ```
>
> 相当于
>
> ```sql
> select * from t_user where username like '%?%'
> ```
>
> set参数的时候找不到这个`?`
>
> 所以不能使用`#{}`
>
> 当然可以使用字符串拼接的方式来使用`#`,但是`$`更方便一点
>
> ```sql
> select * from t_user where username like "%"#{mohu}"%"
> ```

结果

```reStructuredText
User{id=1, username='admin', password='123456', age=23, gender='男', email='12345@qq.com'}
User{id=3, username='admin1', password='123456', age=23, gender='男', email='12345@qq.com'}
User{id=4, username='Aoidayo', password='201741', age=21, gender='男', email='2908496836@qq.com'}
```

## 7.2、批量删除

```java
/**
* 批量删除
* @param ids
* @return
*/
int deleteMore(@Param("ids") String ids);
```

```xml
<!--int deleteMore(@Param("ids") String ids);-->
<delete id="deleteMore">
	delete from t_user where id in (${ids})
</delete>
```

后面将动态sql的时候可以使用`forEach`标签循环执行单个删除

## 7.3、动态设置表名

```java
/**
* 动态设置表名，查询所有的用户信息
* @param tableName
* @return
*/
List<User> getAllUser(@Param("tableName") String tableName);
```

```xml
<!--List<User> getAllUser(@Param("tableName") String tableName);-->
<select id="getAllUser" resultType="User">
	select * from ${tableName}
</select>
```

根据不同的`tableName`从不同的表中获取所有的User

## 7.4、添加功能获取自增的主键(常用)

> 场景模拟：
>
> t_clazz(clazz_id,clazz_name)
>
> t_student(student_id,student_name,clazz_id)
>
> 1、添加班级信息
>
> 2、获取新添加的班级的id
>
> 3、为班级分配学生，即将某学的班级id修改为新添加的班级的id

```java
/**
* 添加用户信息
* @param user
* @return
* useGeneratedKeys：设置使用自增的主键
* keyProperty：因为增删改有统一的返回值固定:是受影响的行数，因此只能将获取的自增的主键放在传输的参数user对象的某个属性中(所以是`id`)
*/
int insertUser(User user);
```

```xml
<!--int insertUser(User user);-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
	insert into t_user values(null,#{username},#{password},#{age},#{gender})
</insert>
```

