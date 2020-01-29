## 结论

关于组件间通信，我们可以通过传递层级来区分记忆：

-   父传子

    -   props
    -   \$parent
    -   attrs 与 listener

    ```js
        <template>
            <div>
                <child
                :foo="foo"
                @qwer="qwer"
                ></child>
            </div>
        </template>
        <script>
        const child = () => import("./child.vue");
        export default {
            components: { child },
            data() {
                return {
                    foo: "123"
                };
            }
            qwer() {
                console.log('fn');
            }
        };
    </script>
    ```

    ```js
        //child.vue
        <template>
            <div>
                <p>foo: {{ foo }}</p>
            </div>
        </template>
        <script>
        export default {
            inheritAttrs: false,
            props: {
                foo: String
            },
            created() {
                console.log(this.$attrs);
                console.log(this.$listener);
        }
        };
        </script>
    ```

-   子传父

    -   \$emit
    -   \$children
    -   ref

    ```js
    // child vue
    export default {
        data() {
            return {
                name: 'lalala',
            };
        },
        methods: {
            sayName() {
                console.log(this.name);
            },
        },
    };
    ```

    ```js
        <template>
            <child ref="child"></child>
        </template>
        <script>
        export default {
            mounted () {
                const child = this.$refs.child;
                console.log(child.name);
                child.sayName();
            }
        }
        </script>
    ```

-   兄弟传递

    -   \$parent
    -   \$root

-   任意组件间

    -   bus
    -   vuex

    ```js
    // Bus:事件派发、监听和回调管理
    class Bus {
        constructor() {
            this.callbacks = {};
        }
        $on(name, fn) {
            this.callbacks[name] = this.callbacks[name] || [];
            this.callbacks[name].push(fn);
        }
        $emit(name, args) {
            if (this.callbacks[name]) {
                this.callbacks[name].forEach(cb => cb(args));
            }
        }
    }
    // main.js
    Vue.prototype.$bus = new Bus();
    // child1
    this.$bus.$on('foo', handle);
    // child2
    this.$bus.$emit('foo');
    ```

-   祖先传后代

    -   provide/inject

    ```js
    // A.vue
    export default {
        provide: {
            name: 'lalala',
        },
    };
    ```

    ```js
    // B.vue
    export default {
        inject: ['name'],
        mounted() {
            console.log(this.name);
        },
    };
    ```
