## 结论

-   组件返回一个 data 函数是因为组件可能被用来创建多个实例，如果 data 是对象，那么多个实例共用一个对象，会造成数据混乱。

-   而 vue 根实例只会创建一次，所以不会存在问题

## 过程

-   源码角度

从初始化 data 函数的代码中我们可以看出，data 可能是 function，如果是 function，就执行该 function。

```js
function initData(vm: Component) {
    let data = vm.$options.data;
    data = vm._data =
        typeof data === 'function' ? getData(data, vm) : data || {};
    ...
}
```

如果 data 是函数，在之后的监听函数中，就是单独的一份内存，不会影响其他 vue 实例。

```js
// observe data
observe(data, true /* asRootData */);
```

-   js 角度

    如果 data 是一个对象，那么在实例化的时候，会引用同一个对象，这样在一个实例发生变化的时候，另外的实例也会发生变化，这是 JS 的特性，所以为了不相互影响，也必须用函数返回不同对象。
