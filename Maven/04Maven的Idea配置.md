新建`maven工程`

`settings\Build Tools\Maven`进行配置

![image-20221231191050149](assets/image-20221231191050149.png)

创建一个普通的Maven项目
==================================================================================

![在这里插入图片描述](https://img-blog.csdnimg.cn/f580ae38abe44b53b96a3d6c78837cd0.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F1Z2Vuc3Rlcm5fUVhM,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/d5cc4d3d2ac14093a0d9e45fd6527b49.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F1Z2Vuc3Rlcm5fUVhM,size_16,color_FFFFFF,t_70#pic_center)

## 1. 添加<dependencies>依赖

![image-20221231192804601](assets/image-20221231192804601.png)

## 2. 编写程序测试

![image-20221231192840125](assets/image-20221231192840125.png)

```
D:.
├─src
│  ├─main
│  │  ├─java-Demo.java
│  │  └─resources
│  │      ├─archetype-resources
│  │      │  └─src
│  │      │      ├─main
│  │      │      │  └─java
│  │      │      └─test
│  │      │          └─java
│  │      └─META-INF
│  │          └─maven
│  └─test
│      └─java--DemoTest.java
└─target
```

## 3. 可以在右侧进行mvn的命令选择

![image-20221231193110905](assets/image-20221231193110905.png)

## 4. 也可以在运行配置中

配置需要执行的mvn命令和mvn工作文件夹

![image-20221231193226917](assets/image-20221231193226917.png)