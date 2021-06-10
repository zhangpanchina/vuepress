# hash history 区别

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

## History API

在`history`中向后跳转：

```js
window.history.back();
```

向前跳转:

```js
window.history.forward();
```

跳转到 history 中指定的一个点:

```js
window.history.go(-1); // 相对于当前页面0，跳转-1个页面，也就是后跳一个页面
```

查看历史堆栈中页面的数量:

```js
window.history.length;
```

## HTML5 引入了`history.pushState()` 和 `history.replaceState()`方法,这些方法通常与 `window.onpopstate` 配合使用

> [window.onpopstate](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowEventHandlers/onpopstate)  
> window.onpopstate 是 popstate 事件在 window 对象上的事件处理程序
