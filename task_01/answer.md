## 结论

当 v-for 与 v-if 处于同一元素上时，v-for 的优先级更高，v-if 将重复运行于每个 v-for 循环中。

当两者同时出现：

-   如果是为了跳过 v-for 循环，可以将 v-if 放置在父节点。

```html
<template v-if="todos.length">
    <p v-for="item in todos">{{item}}</p>
</template>
```

-   如果是为了过滤部分元素，可以使用计算属性。

```js
export default {
    computed: {
        listRes() {
            return todos.filter(() => v.isComplete);
        },
    },
};
```
