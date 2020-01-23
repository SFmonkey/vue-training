## 结论

-   key 主要用在 vue 的虚拟 DOM 算法，在 新旧 vnode 中辨识 vnode, 可以更加高效的更新虚拟 DOM。每一层级，当 key 值存在时，会当作每个 vnode 节点的唯一标识。

    例如：

```js
function sameVnode(a, b) {
    return (
        a.key === b.key &&
        ((a.tag === b.tag &&
            a.isComment === b.isComment &&
            isDef(a.data) === isDef(b.data) &&
            sameInputType(a, b)) ||
            (isTrue(a.isAsyncPlaceholder) &&
                a.asyncFactory === b.asyncFactory &&
                isUndef(b.asyncFactory.error)))
    );
}
```

-   相同父元素的子元素必须有不同的 key，否则会报错

```js
function checkDuplicateKeys(children) {
    const seenKeys = {};
    for (let i = 0; i < children.length; i++) {
        const vnode = children[i];
        const key = vnode.key;
        if (isDef(key)) {
            if (seenKeys[key]) {
                warn(
                    `Duplicate keys detected: '${key}'. This may cause an update error.`,
                    vnode.context,
                );
            } else {
                seenKeys[key] = true;
            }
        }
    }
}
```
