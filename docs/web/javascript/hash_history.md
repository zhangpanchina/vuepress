# hash history 区别

## 一、history 对象
> `history` 对象表示用户首次使用当前窗口以来导航的历史记录，处于安全考虑，不能通过它来访问历史`URL`,但它提供了前进和后退的`API`。
### 1.导航
`history.go()` `api`提供了可以导航到用户任意一次历史记录的功能，他接受一个整型参数，整数表示前进几页，负数表示后退几页
```js
history.go(1) // 前进一页
```
```js
hsitory.go(-2) // 后退两页
```
::: tip
当使用`history.go(0)`时，会刷新当前页面
:::
`history.go()`有两个简写的方法：`history.back()`和`history.forward()`，分别相当于`history.go(-1)`和`history.go(1)`。  
使用`history.length`属性可以查看有多少条历史记录。 
### 2.历史状态管理
`HTML5` 提供了添加和修改历史记录的方法，分别是`history.pushState()`和`history.replaceState()`
#### history.pushState() 方法
`pushState()`需要三个参数：
* 状态对象：是一个`javascript`对象，状态对象可以是能被序列化的任何东西。
* 标题：暂未用到。
* URL：调用 pushState() 后浏览器并不会立即加载这个URL，但可能会在稍后某些情况下加载这个URL，比如在用户重新打开浏览器时。新URL不必须为绝对路径。如果新URL是相对路径，那么它将被作为相对于当前URL处理。新URL必须与当前URL同源，否则 pushState() 会抛出一个异常。该参数是可选的，缺省为当前URL。  
```js
let stateObj = {
    foo: "bar",
};

history.pushState(stateObj, "page 2", "bar.html");
```
`pushState()`方法执行后，状态信息就会被推到历史记录中，浏览器地址栏也会改变成新的相对`URL`，但是**浏览器页不会向服务器发送请求**。  
#### history.replaceState()
`replaceState()`修改了当前的历史记录项。  
```js
history.replaceState(stateObj, "page 3", "bar2.html");
```
## 二、location.hash
## 三种监听`hashchange`事件的方法

```js
window.onhashchange = funcRef;

<body onhashchange="funcRef()"></body>;

window.addEventListener("hashchange", funcRef, false);
```

_Example:_

```js
function locationHashChanged() {
  if (location.hash === "#somecoolfeature") {
    somecoolfeature();
  }
}

window.addEventListener("hashchange", locationHashChanged);
```
### 三、两者区别
`history`可以将任意数据和新的历史记录项相关联。而基于哈希的方式，要把所有相关数据编码为短字符串。 
::: tip
pushState() 绝对不会触发 hashchange 事件，即使新的URL与旧的URL仅哈希不同也是如此
:::
