## 结论

### 定义

-   computed

    > 加算属性会根据依赖的数据而动态的变化，当依赖数据不变时，它的结果是缓存的。

-   watch

    > watch 像是一个 data 数据的监听回调，在监听方法中会传入 newVal 与 oldVal。

### 使用

-   当一个数据依赖多个数据时，使用 computed

    ```js
    var vm = new Vue({
        data: {
            a: 1,
            b: 2,
        },
        computed: {
            sum: function() {
                return a + b;
            },
        },
    });
    ```

-   当一个数据变化需要做一些事情的时候，使用 watch

    ```js
    var vm = new Vue({
        data: {
            a: 1,
            b: 2,
        },
        watch: {
            a: function(val, oldVal) {
                // 一些与后台交互操作等
            },
        },
    });
    ```

### 功能

-   computed 的底层是 watch，但是 computed 实现了缓存等更多的功能。

-   watch 方法允许我们执行一个异步操作，watch 更加的通用。

-   在可以使用 computed 的时候，优先使用 computed。
