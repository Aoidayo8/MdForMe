## Maven的工程目录结构

![image-20221231185858384](assets/image-20221231185858384.png)

```
D:.
│  pom.xml
│
└─src
    ├─main
    │  ├─java
    │  │  └─com
    │  │      └─my
    │  │              Demo.java
    │  │
    │  └─resources
    └─test
        ├─java
        │  └─com
        │      └─my
        │              DemoTest.java
        │
        └─resources
```

## mvn命令

版本命令

```
mvn -version
```

构建命令

```
mvn -compile
//下载依赖的插件并生成target文件夹
```

```
mvn -clean
//下载依赖的插件并清理target文件夹
```

测试命令

```
mvn -test
```

![image-20221231184548160](assets/image-20221231184548160.png)

测试报告

![image-20221231184803873](assets/image-20221231184803873.png)

![image-20221231184918903](assets/image-20221231184918903.png)

打包指令

```
mvn package
```

![image-20221231185429841](assets/image-20221231185429841.png)

先编译,测试再打包

![image-20221231185329598](assets/image-20221231185329598.png)

mvn安装到本地仓库

```
mvn install
```

![image-20221231185500626](assets/image-20221231185500626.png)

## Maven插件创建工程

![image-20221231190143067](assets/image-20221231190143067.png)

要求

```
创建时里面不是一个maven工程的结构,没有pom.xml
```

![image-20221231190535661](assets/image-20221231190535661.png)