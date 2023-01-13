# less 简介

`less`是一门`css`的**预处理**语言(**预编译**语言)

- `less`是一个 css 的增强版，通过`less`可以编写更少的代码实现更强大的样式
- 在`less`中添加了许多的新特性：像对变量的支持、对`mixin`的支持...
- `less`的语法大体上和`css`语法一致，但是`less`中增添了许多对`css`的扩展，所以浏览器无法直接执行`less`代码，要执行必须向将`less`转换为`css`，然后再由浏览器执行

> css缺点:
>
> 1. 当一个网站统一使用某个颜色的时候,使用单纯的css无法统一修改
>    - Css的新特性-变量兼容性差,IE11不支持
>    - Css的计算函数`calc()`,能用,但是兼容性差,IE11不支持
>    - Css的许多特性导致许多都不能使用

# 安装与使用

在`vscode`中搜索`less`，点击`安装`

![image-20210626203546217](https://img-blog.csdnimg.cn/img_convert/7649e0d4d1ad3f9179fecb8642287ad0.png)

### 编写 less

**html 代码**

使用快捷方式创建`html`代码

![image-20210626204018016](https://img-blog.csdnimg.cn/img_convert/f992f8225262f1355df4628586602a87.png)

`回车`生成`html`代码

```html
<div class="box1"></div>
<div class="box2"></div>
<div class="box3"></div>
```

**less 代码**

创建`style.less`文件，编写`less`代码

```less
body {
    /*设置变量*/
  --height: calc(200px / 2);
  --width: 100px;
    --color:#bfa;
  div { 
    height: var(--height);/*使用变量*/
    width: var(--width);
  }
  .box1 {
    background-color: var(--color);
  }
  .box2 {
    background-color: var(--color);
  }
  .box3 {
    background-color: var(--color);
  }
}
```

`Easy LESS`插件会帮助我们在`style.less`所在目录下面生成一个相同名称的`css`文件

![image-20210626204312658](https://img-blog.csdnimg.cn/img_convert/201068ea2c14019210172db470a04934.png)

查看生成的`style.css`代码

```css
body {
  --height: calc(200px / 2);
  --width: 100px;
}
body div {
  height: var(--height);
  width: var(--width);
}
body .box1 {
  background-color: var(--color);
}
body .box2 {
  background-color:var(--color);
}
body .box3 {
  background-color: var(--color);
}
```

我们直接在 HTML 中引入生成的`style.css`

```html
<link rel="stylesheet" href="/css/style.css" />
```

运行代码，查看效果

# less 语法

## less 注释

`less`中的单行注释，注释中的内容不会被解析到`css`中

`css`中的多行注释，内容会被解析到`css`文件中

```less
// 单行注释，注释中的内容不会被解析到`css`中

/*
多行注释，内容会被解析到`css`文件中
*/
```

## 父子关系嵌套

在`less`中，父子关系可以直接嵌套

```less
// `less`中的单行注释，注释中的内容不会被解析到`css`中

/*
`css`中的注释，内容会被解析到`css`文件中
*/
body {
  --height: calc(200px / 2);
  --width: 100px;
  div {
    height: var(--height);
    width: var(--width);
  }
  .box1 {
    background-color: #bfa;
      //box2是box1的后代
      .box2 {
          background-color: red;
          //box3是box2的后代
          .box3 {
              background-color: yellow;
          }
          //box4是box2的儿子
          >.box4 {
              background-color: green;
          }
    }
  }
}
```

对应的`css`

```css
/*
`css`中的注释，内容会被解析到`css`文件中
*/
body {
  --height: calc(200px / 2);
  --width: 100px;
}
body div {
  height: var(--height);
  width: var(--width);
}
body .box1 {
  background-color: #bfa;
}
body .box1 .box2 {
  background-color: red;
}
body .box1 .box2 .box3 {
  background-color: yellow;
}
body .box1 .box2 > .box4 {
  background-color: green;
}
```

## 变量

变量，在变量中可以存储一个任意的值

并且我们可以在需要时，任意的修改变量中的值

变量的语法：`@变量名`

- **直接使用变量**(颜色,值,带单位的值)时，则以`@变量名`的形式使用即可

- **变量可以替换的东西**:类名,属性名,值,值和单位

- 作为**类名、属性名或者一部分值使用**时，必须以`@{变量名}`的形式使用

  - ```less
    .@{b1}{
      width: @size;
      height: $width;
        //属性名
      @{bc}: @color;
        //一部分值
        //注意：在`url`中使用`less`语法需要用引号包裹
      @{bi}: url("@{path}/1.png");
    }
    ```

- 可以在变量声明前就使用变量（可以但不建议）

```less
@b1:box1;
@b2:box2;
@b3:box3;
@size:200px;
@bc:background-color;
@bi:background-image;
@color:red;
@path:image/a/b/c;

.@{b1}{
  width: @size;
  height: $width;
  @{bc}: @color;
  @{bi}: url("@{path}/1.png");
}

.@{b2}{
  width: @size;
  height: $width;
  @{bc}: @color;
  @{bi}: url("@{path}/2.png");
}

.@{b3}{
  width: @size;
  height: $width;
  @{bc}: @color;
  @{bi}: url("@{path}/3.png");
}
```

生成的`css`代码

```css
.box1 {
  width: 200px;
  height: 200px;
  background-color: red;
  background-image: url("image / a / b / c/1.png");
}
.box2 {
  width: 200px;
  height: 200px;
  background-color: red;
  background-image: url("image / a / b / c/2.png");
}
.box3 {
  width: 200px;
  height: 200px;
  background-color: red;
  background-image: url("image / a / b / c/3.png");
}
```

- 可以看出来编译以后的css文件是直接替换
- 注意：在`url`中使用`less`语法需要用引号包裹

### 变量的重复定义

```less
@b:200px;
@b:250px;
div{
    @b:150px;
    width:@b;
    //变量发生重复的时候,就近原则,哪个近使用哪个
}
```

## 属性值的引用$

`$属性值`引用

```less
div{
    @b:150px;
    width:@b;
    height:$width;
}
```

## 父元素选择器&

```less
.box1 {

    // 后代选择器
    .box2 {
        height: 100px;
    }

    // 子元素选择器
    >.box3 {
        color: aliceblue;

        // &=.box1>.box3: hover
        &:hover {
            height: 200px;
        }
    }

    // 伪类选择器
    :hover {
        color: aqua;
    }

    //and符号表示一个外层的父元素=>.box1
    //外层的嵌套元素
    &:hover {
        color: red;
    }
}
```

用于简写属性名

```less
//加入box内部有box-1,box-2,box-3可以写成
.box1{
    &-1{}//变异后.box-1{}
    &-2{}//变异后.box-2{}
    &-3{}
}
```

## :extend()

`:extend()` 对当前选择器扩展指定选择器的样式（选择器分组）,相当于继承样式

```less
.p1 {
    width: 200px;
    height: 200px;
}

.p2:extend(p1) {
    color: red;
}
```

**compileTo**

```css
.p1 {
  width: 200px;
  height: 200px;
}
.p2 {
  color: red;
}

```

另外一种写法

使用`选择器+()`的方式引用,此时编译后的css文件不会分组

> 直接对指定的样式进行引用，这里就相当于将`p1`的样式在这里进行了复制（`mixin` 混合）
>
> - p1的样式直接混合复制到p2处
>
>   - 使用类选择器时可以在选择器后边添加一个括号，此时不是选择器, 这时我们实际上就创建了一个`mixins`混合函数
>
>     - 在css中不会有p1的样式,仅仅只是创建一个样式给别的元素使用
>
>     - 但是使用分组的时候,代码占用小,执行效率更高
>
>     - ```less
>       .p1() {
>           width: 200px;
>           height: 200px;
>       }
>       .p2 {
>           .p1;
>           //引用的时候不写括号
>           color: red;
>       }
>       ```

```less
.p1 {
    width: 200px;
    height: 200px;
}

.p2 {
    .p1();
    color: red;
}
```

```css
.p1 {
  width: 200px;
  height: 200px;
}
.p2 {
  width: 200px;
  height: 200px;
  color: red;
}
```

## 混合函数mixin

在混合函数中可以直接设置变量，并且可以指定默认值

```less
.test(@w:200px, @h:100px, @bc:red) {
  /*指定默认值*/
  width: @w;
  height: @h;
  background-color: @bc;
}

.p6 {
  // .test(200px, 100px, red); // 对应参数位传值
  // .test(@h:200px,@w:100px,@bc:red); // 写明对应属性，可变换顺序
  // .test();
    //在其他元素中对test进行引用,即使用混合函数
  .test(300px);
}
```

生成的`css`代码

```css
.p6 {
  width: 300px;
  height: 100px;
  background-color: red;
}
```

### 一些给定的mixin函数

- `average`混合函数

  - 单对颜色之间取平均值


  ```less
  .h1 {
    color: average(red, yellow);
  }
  ```

  生成的`css`代码

  ```css
  .h1 {
    color: #ff8000;
  }
  ```

- `darken`混合函数

  - 加深颜色
  - 参数1:颜色
  - 参数2:参数1的颜色加深的百分比
    - 100%:黑色
    - 介于中间的值:进行一个普通的加深
    - 0%:无变化


  ```less
  body {
    background-color: darken(#bfa, 50%);
  }
  ```

  生成的`css`代码

  ```css
  body {
    background-color: #22aa00;
  }
  ```


## 对于calc的改进

less中可以直接对数值进行计算

`+-*/`

```less
width:100px+200px;
width:100px/2;
...
```

## 引入

### 引入的方式

创建`all.less`文件，将我们之前编写的`less`文件通过`@import`引入进来

可以通过`import`来将其他的`less`引入到当前的`less`中

```less
@import "style.less";
@import "syntax.less";
```

查看生成的`all.css`代码，会发现其他的内容囊括了两个`less`文件和本地文件的内容

- 所以，我们可以利用`@import`来对`less`文件进行整合，然后引入生成的`css`文件使用即可
  - 这样，每次修改的时候直接对某个模块的`less`文件进行修改，就会非常简单(**模块化**)

### less代码定位

如果我们观察过之前`fontawesome`源码文件，会发现其中也有`less`代码文件

![image-20210626222450991](https://img-blog.csdnimg.cn/img_convert/71b255aafaccb595fc0341c50f6a3fa0.png)

不同的`less`文件里都有其自己的`职责`，如

- `_animated.less`中专门存放动画的混合函数
- `_variables.less`中专门存放定义的变量
- ...

但是也有个问题，通过`F12`调试时显示的也是`css`中对应的行号

![image-20210626223208492](https://img-blog.csdnimg.cn/img_convert/dcc8dac7e284d8f1f296455dd302d7a6.png)

如果我们要改，需要找一下，太麻烦了，能不能直接显示`less`中行号呢？这样我们直接定位到对应`less`中直接进行修改，维护起来也会比较方便

我们需要在`Easy LESS`插件中修改`settings.json`文件，在其中添加如下配置

```json
"less.compile": {
    "compress": true, // true => remove surplus whitespace
    "sourceMap": true, // true => generate source maps (.css.map files)
    "out": true // false => DON'T output .css files (overridable per-file, see below)
}
我们来逐一解释下配置的`less.compile`项中每一个属性的含义
- `compress` 生成的`css`文件代码会被压缩（作用相当于我们之前安装的`JS & CSS Minifier (Minify)`插件的效果）
- `sourceMap` 生成`.css.map`文件，通过`F12`可以查看了`less`文件对应行号(源码地图,用于定位css文件中的less代码在第几行)
- `out` 生成对应`css`文件
```

修改完毕后，会发现多生成出来一个`all.css.map`文件，说明配置生效

![image-20210626224441979](https://img-blog.csdnimg.cn/img_convert/288677124cd646d938430db12c32efbe.png)

再刷新下页面，通过`F12`会发现变成了`less`文件对应的行号

![image-20210626223858712](https://img-blog.csdnimg.cn/img_convert/781253b1659517f6dcf08697930b8e6d.png)

