
[TOC]

## 从输入 URL 到页面展现中间发生了什么
```
DNS 查询 DNS 缓存
建立 TCP 连接（三次握手）连接复用
发送 HTTP 请求（请求的四部分）
后台处理请求
监听 80 端口
路由
渲染 HTML 模板
生成响应
发送 HTTP 响应
关闭 TCP 连接（四次挥手）
解析 HTML
下载 CSS（缓存
解析 CSS
下载 JS（缓存
解析 JS
下载图片
解析图片
渲染 DOM 树
渲染样式树
执行 JS
```

## ES6新特性
- 块级作用域、块级变量let、块级常量const
- 箭头函数、展开运算符、默认参数
- 模版字面量
- class
- Promise
- 模块的导入导出 import export
- 解构赋值

## 防抖
> 触发事件后在 n 秒内函数只能执行一次（window.resize）
```
function debounce(func, delay) {
    let timer
    return function () {
        clearTimeout(timer)
        timer = setTimeout(() => {
            func.apply(this, arguments)
        }, delay)
    }
}
```

## 截流
> 连续触发事件但是在 n 秒中只执行一次函数（有点像游戏中的技能cd）
```
function throttle(func, delay) {
    let flag = true
    return function () {
        if (!flag) {
            return
        }
        flag = false
        setTimeout(() => {
            func.apply(this, arguments)
            flag = true
        }, delay)
    }
}
```

## Ajax
```
var request = new XMLHttpRequest()
request.open('GET', 'xxx')
request.onreadystatechange = function () {
    if (request.readySState === 4) {
        console.log('请求完成')
        if (request.response.status >= 200 && request.response.status < 300) {
            console.log('请求成功')
        } else {
        }
    }
}
request.send()
```

## this指向
```
1、fn()                    -> window
2、obj.fn()                -> obj
3、fn.call/apply/bind(xxx) -> xxx
4、new Fn()                -> 新的对象
5、fn = () => {}           -> 外面的this
```

## 闭包
> 官方定义：有权访问另一函数作用域中变量的函数。

> 很好理解，你调用 fnOut，fnOut 入栈，产生一个运行时。
运行完毕，返回 fnIn， fnOut 出栈，此时函数运行完毕。
但是你把 fnIn 的引用挂在了 window 对象的 iOne 属性上，
因为 window 对象不会被释放，为了保证 iOne 能被正常访问到，
fnIn 所在的作用域就不能释放，产生一个闭包。

在哪里用到
1、不想被直接修改的数据
2、构造函数里

## CORS 和 跨域
在 `header` 里的 `Access-Control-Allow-Origin` 设置为 `*` 或者 对应域名

## async await
> 如何捕获异常：try catch

## 深拷贝
- 递归
- 判断类型
- 检查循环引用
- 不可能拷贝 __proto__

JSON.parse(JSON.stringify())
缺点：JSON 不支持函数、引用、undefined、RegExp、Date……

```
function clone(object){
    var object2
    if(! (object instanceof Object) ){
        return object
    }else if(object instanceof Array){
        object2 = []
    }else if(object instanceof Function){
        object2 = eval(object.toString())
    }else if(object instanceof Object){
        object2 = {}
    }
    你也可以把 Array Function Object 都当做 Object 来看待，参考 https://juejin.im/post/587dab348d6d810058d87a0a
    for(let key in object){
        object2[key] = clone(object[key])
    }
    return object2
}
```

## 正则实现trim()
```
function trim() {
    return string.replace(/^\s+|\s+$/g, '')
}
```

## 继承
> 继承 `属性` 和 `方法`
```
function SuperType(name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}
SuperType.prototype.sayName = function() {
    alert(this.name);
}

function SubType(name, age) {
    // 继承属性
    SuperType.call(this, name);
    this.age = age;
}
// 继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function() {
    alert(this.age);
}
```

## 数组去重

- hash
- [...new Set(array)]
- WeakMap

## Promise

```
function Promise(executor) {
    let self = this;
    self.status = 'pending'; //等待态
    self.value = undefined;  //成功的返回值
    self.reason = undefined; //失败的原因

    function resolve(value){
        if(self.status === 'pending'){
            self.status = 'resolved';
            self.value = value;
        }
    }
    function reject(reason) {
        if(self.status === 'pending') {
            self.status = 'rejected';
            self.reason = reason;
        }
    }
    try{
        executor(resolve, reject);
    }catch(e){
        reject(e);// 捕获时发生异常，就直接失败
    }
}
//onFufiled 成功的回调
//onRejected 失败的回调
Promise.prototype.then = function (onFufiled, onRejected) {
    let self = this;
    if(self.status === 'resolved'){
        onFufiled(self.value);
    }
    if(self.status === 'rejected'){
        onRejected(self.reason);
    }
}
```

## http
### 状态码
- 100 continue
- 200 ok
- 201 created
- 301 永久重定向
- 302 临时重定向
- 304 not modify
- 401 没权限
- 403 forbidden 服务器禁止
- 404 not found 没资源
- 405 method not allowed 方法不被允许
- 414 请求过长
- 500 服务器错误

### 缓存

> 顺序: Cache-Control -> Expires -> ETag

- ETag: md5 有请求 304
- Expire: 日期。bug: 用户调整本地时间的话会失效
- cache-control: max-age=600 大部分用这个 无请求

### get post 区别
- post 相对安全
- get 有长度限制
- get 参数放url，post 放body体
- get 只需要一个报文，post 需要两个
- get 幂等，post 不幂等

### Cookie、LocalStorage、SessionStorage、Session

> cookie
1. 服务器发给浏览器的一段字符串
2. 通信的时候会带上
3. 放浏览器
4. 4k 放用户信息

> session
1. 一段时间内的会话
2. 放服务器
3. session基于cookie，sessionid放到cookie里

> LocalStorage
1. 5M 放不重要的数据
2. 不发送到服务器
3. 一般不过期

> SessionStorage
1. 会话结束时过期（如关闭浏览器）

## Vue

### watch 和 computed

watch: 监听属性，没有缓存
computed: 计算属性，有缓存

### 生命周期
- `beforeCreate` - 实例初始化后，不能访问元素、data
- `created` - 实例创建完成，可访问data，不能访问元素
- `beforeMount` - 挂载开始前被调用，同 created 一样
- `mounted` - 挂载到 DOM 上，data 和 元素 都能访问，这里发送请求
- `beforeUpdate`
- `updated`
- `beforeDestory` - 实例销毁前
- `destoryed` - 实例销毁后

### 组件通信
1. 父子组件 `$emit()` `$on()`
2. 爷孙 `eventBus` 
```
var eventBus = new Vue();
eventBus.$emit()
eventBus.$on()
```
3. Vuex
状态管理工具 - 其实就是把数据存放在闭包里，再在闭包里实现几个可供外面调用的函数，来更改数据

- `state`: 你要存放数据的地方
- `Getter`: 获取一些数据，可以经过处理的数据，有缓存，只有变化后才会重新计算
- `Mutation`: 更改数据的唯一方法，{type, func}
- `Action`: 做异步操作，内部提交mutation，通过 `dispatch` 触发

### 响应式原理
`Object.defineProperty` 把属性转为 getter/setter

Vue.set 用来添加或者删除属性，原因是因为对于对象、数组无法检测

### VueRouter

重定向：redirect
别名：

#### 导航守卫
- router.beforeEach 前置钩子
- router.afterEach 后置钩子
- 组件内部：
1. beforeRouteEnter
2. beforeRouteUpdate
3. beforeRouteLeave

#### 滚动行为
history.pushState

#### 懒加载：
```
import('./xxx')
```

## React

### 受控组件 和 非受控组件
受控组件：值和方法都是传入的，外面可控制
非受控组件：方法内部的，不受控制

### 生命周期
- componentDidMount
- componentWillUnmount
- shouldComponentUpdate

### 组件通信
- props
- redux

### 如何优化的

### 虚拟DOM
1. 用js来表示dom的结构
2. 比较两棵dom数的差异
3. 对真实dom进行最小的修改

### diff原理
深度优先
广度优先

### redux
单项数据流
action -> reducers -> store 最后更新到 dom 上
- createStore
- State 通过 createStore 获取
- getState
- action 要修改的方法 {type, func}
- dispatch 发出action
- Reducer 更新state
- subscribe监听更新

connect 和 provider

## Webpack

### loader、pulgin
loader 加载器
- css-loader
- scss-loader
- babel-loader
- file-loader
- vue-loader

pulgin 插件
- html-webpack-pulgin
- happypack
- webpack-merge

### 构建速度
dll dll-pulgin manifest.json告诉那些打包哪些不需要打包
code split
happypack

### 文件过大
- 提取通用模块

## 宏任务 和 微任务
> 先 微任务 后 宏任务

node:
setTimeout
setImmediate
nextTick

chrome:
setTimeout 宏任务
promise.then() 微任务

宏任务：一会
- setTimeout
- setInterval
- UI渲染
- I/O

微任务：马上
- Promise
- process.nextTick

## 算法
> 快速
```
function quickSort(arr) {
    if (arr.length <= 1) {
        return arr;
    }
    var pivotIndex = Math.floor(arr.length / 2);
    var pivot = arr.splice(pivotIndex, 1)[0];
    var left = [];
    var right = [];

    for (var i = 0; i < arr.length; i++) {
        if (arr[i] <= pivot) {
            left.push(arr[i]);
        } else {
            right.push(arr[i]);
        }
    }
    return quickSort(left).concat([pivot], quickSort(right));
}
```
> 冒泡
```
function bubbleSort(arr) {
    for (let i = 0; i < arr.length; i++) {
        for (let j = 0; j < arr.length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
            }
        }
    }
    return arr
}
```
> 选择
```
function sectionSort(arr) {
    let minIndex
    for (let i = 0; i < arr.length; i++) {
        minIndex = i
        for (let j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j
            }
        }
        if (i !== minIndex) {
            [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]]
        }
    }

    return arr
}
```
