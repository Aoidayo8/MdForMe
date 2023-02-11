# async 和 await

async 和 await 两种语法结合可以让异步代码像同步代码一样

## async 函数

### 语法

```js
async function fn(){
    retrun ...
}
```

async会将返回的值封装为一个promise对象

- async 函数的返回值不是为`promise `对象

  - promise 对象的结果`PromiseValue`由 async 函数执行的返回值决定
  - 返回promise对象状态`PromiseState`为resolved

- async函数内部抛出错误

  - `promise{vlaue:抛出对象,state:rejected}`失败promsie

- async返回一个`promise`对象

  - ```js
    return new Promise((resolve,reject)=>{
    	resolve("success")
    })
    //async返回的promise{value:success,state:resolved}
    return new Promise((resolve,reject)=>{
    	reject("failure")
    })
    //async返回的promise{value:failure,state:rejected}
    ```

对async函数返回值处理//使用then处理

```js
async function fn(){
	return new Promise((resolve,reject)=>{
		resolve("success");
	})
}
fn().then(value=>{
	console.log(value);
},reason=>{
	console.log(reason);
})]
```

## await 表达式

1. await 必须写在 async 函数中(await单向依赖于async)
2. await 右侧的表达式一般为 promise 对象
3. await 返回的是 promise 成功的值(即返回`PromiseValue`)
4. await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理

### 语法

```js
await new Promise((resolve,reject)=>{...})
```

### Context

> 成功

```js
const p=new Promise((resolve,reject)=>{
	resolve("success")
})
async function fn(){
	let result=await p;
	console.log(result);//success
}
```

> 失败
>
> - await处理promise,失败的promise抛出异常
> - 需要捕获异常

```js
const p=new Promise((resolve,reject)=>{
	reject("failure")
})
async function fn(){
    try{
    	let result=await p;
		console.log(result);//faliure
    }catch(e){
        console.log(e)
    }
}
```

## 读取文件

依赖于`nodejs`的`fs`模块

调用`fs.readFile(url,callback)`函数,返回一个`promise`对象

```js
//1. 引入 fs 模块
const fs = require("fs");

//读取『为学』
function readWeiXue() {
    return new Promise((resolve, reject) => {
        fs.readFile("./resources/为学.md", (err, data) => {
            //如果失败
            if (err) reject(err);
            //如果成功
            resolve(data);
        })
    })
}

readWeiXue();
```

## 应用:async&await的优势

读取文件

```js
//1. 引入 fs 模块
const fs = require("fs");
//声明3个读取函数
//读取『为学』
function readWeiXue() {
    //防止读取失败,使用promise对象
    return new Promise((resolve, reject) => {
        fs.readFile("./resources/为学.md", (err, data) => {
            //如果失败
            if (err) reject(err);
            //如果成功
            resolve(data);
        })
    })
}
// 读取插秧诗
function readChaYangShi() {
    return new Promise((resolve, reject) => {
        fs.readFile("./resources/插秧诗.md", (err, data) => {
            //如果失败
            if (err) reject(err);
            //如果成功
            resolve(data);
        })
    })
}
//读取观书
function readGuanShu() {
    return new Promise((resolve, reject) => {
        fs.readFile("./resources/观书有感.md", (err, data) => {
            //如果失败
            if (err) reject(err);
            //如果成功
            resolve(data);
        })
    })
}
```

> 如果使用async&await
>
> - 主要是比较方便,使用then处理promsie还是有点小麻烦]

```js

//声明一个 async 函数
async function main() {
    //获取为学内容
    let weixue = await readWeiXue();
    //获取插秧诗内容
    let chayang = await readChaYangShi();
    // 获取观书有感
    let guanshu = await readGuanShu();

    console.log(weixue.toString());
    console.log(chayang.toString());
    console.log(guanshu.toString());
    // 使用await关键字,可以不用处理promise错误时的情况
    // 非常类似于同步函数的处理,但是本质上还是promsie
}

main();
```

> 如果使用then处理的话

```js
    /*
    否则使用then函数,会导致比较繁杂的程序,不如使用await&async便捷:
        let p1=readWeiXue();
        p1.then((value)=>{
            console.log(value.toString())
        },reason=>{
            console.log(reason)
        })
        
        let p2=readChaYangShi();
        p2.then((value)=>{
            console.log(value.toString())
        },reason=>{
            console.log(reason)
        })
        
        let p3=readGuanShu();
        p3.then((value)=>{
            console.log(value.toString())
        },reason=>{
            console.log(reason)
        })
    */
```

## async&await发送ajax请求

```js
        // 发送 AJAX 请求, 返回的结果是 Promise 对象
        function sendAJAX(url) {
            return new Promise((resolve, reject) => {
                //1. 创建对象
                const x = new XMLHttpRequest();

                //2. 初始化
                x.open('GET', url);

                //3. 发送
                x.send();

                //4. 事件绑定
                x.onreadystatechange = function () {
                    if (x.readyState === 4) {
                        if (x.status >= 200 && x.status < 300) {
                            //成功啦
                            resolve(x.response);
                        } else {
                            //如果失败
                            reject(x.status);
                        }
                    }
                }
            })
        }

        //promise then 方法测试
        // sendAJAX("https://api.apiopen.top/getJoke").then(value=>{
        //     console.log(value);
        // }, reason=>{})

        // async 与 await 测试  axios
        async function main() {
            //发送 AJAX 请求
            let result = await sendAJAX("https://api.apiopen.top/getJoke");

            console.log(reault);
            //再次测试
            let tianqi = 
                await sendAJAX
            ('https://www.tianqiapi.com/api/version=v1&city=%E5%8C%97%E4%BA%AC&appid=23941491&appsecret=TXoD5e8P')

            console.log(tianqi);
        }

        main();
```

