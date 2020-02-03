## 结论

1. 创建一个 vue 实例，new Vue

在 vue 方法中，我们执行了 \_init 方法

```js
function Vue(options) {
    this._init(options);
}
```

2. 在 init 方法中，我们初始化了事件与生命周期，这里的生命周期指的是 $parent, $children 等。

```js
initLifecycle(vm);
initEvents(vm);
initRender(vm);
```

3. beforeCreate 在此时调用

4. 初始化注入与校验

我们开始获取注入数据，初始化 props，data，computed，watch，并且提供数据注入。

```js
initInjections(vm); // resolve injections before data/props
initState(vm);
initProvide(vm); // resolve provide after data/props
```

5. created 在此时调用, \_init 方法结束。

6. 接着判断实例参数中是否包含 el 选项，如果有，则进行下一步，若没有，等待用户执行 \$mount 方法。

```js
if (vm.$options.el) {
    vm.$mount(vm.$options.el);
}
```

7. 判断参数中是否包含 template 方法，如果存在，将 template 转换成 render 函数，如果不存在，会将 \$mount 参数中的 el 所在的 html 当作模版编译

8. 在执行 \$mount 方法的核心逻辑前，会首先执行 beforeMount 钩子

```js
callHook(vm, 'beforeMount');
```

9. 创建 vm.\$el 并替代 el

10. mounted 在此时调用

11. 当 data 发生变化时，首先触发 beforeUpdate 钩子

```js
callHook(vm, 'beforeUpdate');
```

12. 虚拟 DOM 重新渲染并应用更新

```js
update () {
    if (this.lazy) {
        this.dirty = true
    } else if (this.sync) {
        this.run()
    } else {
        queueWatcher(this)
    }
}
```

13. updated 在此时调用

14. 当调用 \$distroy 方法时，在核心逻辑前，触发 beforeDistroy 方法

```js
callHook(vm, 'beforeDestroy');
```

15. 解除绑定销毁子组件以及事件监听器

```js
remove(parent.$children, vm);
```

16. 销毁完毕，触发 destroyed

```js
callHook(vm, 'destroyed');
```
