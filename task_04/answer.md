## 结论

diff 算法的过程就是调用叫做 patch 的函数，比较新旧节点，一边比较一边给真实的 DOM 打补丁。

-   在 patch 方法中·，首先进行树级别比较，有三种情况：

    -   新的虚 vnode 如果不存在就删除老的 vnode
    -   如果老的 vnode 不存在就新增新的 vnode
    -   都存在就执行 diff

*   当我们确定两个 vnode 间需要比较时，就执行 patchVnode 方法

    -   如果新老节点有 children 节点，则调用 updateChildren 方法
    -   如果老节点没有而新节点存在，则删除老节点添加新节点
    -   如过老节点有而新节点没有，则移除老节点的自节点
    -   如果都没有，则执行文本替换。

*   对于 updateChildren 函数，新老 vnode 节点会分别有两个变量标记，相互比较，共有四种比较方法。

    -   当 oldStartVnode 和 newStartVnode 或者 oldEndVnode 和 newEndVnode 满足 sameVnode ，直接进行 patchVnode
    -   当 oldStartVnode 和 newEndVnode 满足 sameVnode，则进行 patchVnode 并将真实 DOM 移动到 oldEndVnode 后面
    -   当 oldEndVnode 和 newStartVnode 满足 sameVnode， 则进行 patchVnode 并将真实 DOM 移动到 oldStartVnode 前面
    -   在老节点中找与 newStartVnode 满足 sameVnode 的 vnodeToMove，存在就执行 patchVnode， 将 vnodeToMove 对应的 DOM 移动到老节点对应的 DOM 的前面。

    若没有以上四种情况，如果设置了 key ，就进行 key 的比较，最后一方有剩余，则删除或新增剩下的节点。

## 源码

-   sameVnode

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

-   patchVnode

```js
if (isUndef(vnode.text)) {
    if (isDef(oldCh) && isDef(ch)) {
        if (oldCh !== ch)
            updateChildren(elm, oldCh, ch, insertedVnodeQueue, removeOnly);
    } else if (isDef(ch)) {
        if (process.env.NODE_ENV !== 'production') {
            checkDuplicateKeys(ch);
        }
        if (isDef(oldVnode.text)) nodeOps.setTextContent(elm, '');
        addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue);
    } else if (isDef(oldCh)) {
        removeVnodes(oldCh, 0, oldCh.length - 1);
    } else if (isDef(oldVnode.text)) {
        nodeOps.setTextContent(elm, '');
    }
} else if (oldVnode.text !== vnode.text) {
    nodeOps.setTextContent(elm, vnode.text);
}
```

```js
while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
    if (isUndef(oldStartVnode)) {
        oldStartVnode = oldCh[++oldStartIdx]; // Vnode has been moved left
    } else if (isUndef(oldEndVnode)) {
        oldEndVnode = oldCh[--oldEndIdx];
    } else if (sameVnode(oldStartVnode, newStartVnode)) {
        patchVnode(
            oldStartVnode,
            newStartVnode,
            insertedVnodeQueue,
            newCh,
            newStartIdx,
        );
        oldStartVnode = oldCh[++oldStartIdx];
        newStartVnode = newCh[++newStartIdx];
    } else if (sameVnode(oldEndVnode, newEndVnode)) {
        patchVnode(
            oldEndVnode,
            newEndVnode,
            insertedVnodeQueue,
            newCh,
            newEndIdx,
        );
        oldEndVnode = oldCh[--oldEndIdx];
        newEndVnode = newCh[--newEndIdx];
    } else if (sameVnode(oldStartVnode, newEndVnode)) {
        // Vnode moved right
        patchVnode(
            oldStartVnode,
            newEndVnode,
            insertedVnodeQueue,
            newCh,
            newEndIdx,
        );
        canMove &&
            nodeOps.insertBefore(
                parentElm,
                oldStartVnode.elm,
                nodeOps.nextSibling(oldEndVnode.elm),
            );
        oldStartVnode = oldCh[++oldStartIdx];
        newEndVnode = newCh[--newEndIdx];
    } else if (sameVnode(oldEndVnode, newStartVnode)) {
        // Vnode moved left
        patchVnode(
            oldEndVnode,
            newStartVnode,
            insertedVnodeQueue,
            newCh,
            newStartIdx,
        );
        canMove &&
            nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm);
        oldEndVnode = oldCh[--oldEndIdx];
        newStartVnode = newCh[++newStartIdx];
    } else {
        if (isUndef(oldKeyToIdx))
            oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx);
        idxInOld = isDef(newStartVnode.key)
            ? oldKeyToIdx[newStartVnode.key]
            : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx);
        if (isUndef(idxInOld)) {
            // New element
            createElm(
                newStartVnode,
                insertedVnodeQueue,
                parentElm,
                oldStartVnode.elm,
                false,
                newCh,
                newStartIdx,
            );
        } else {
            vnodeToMove = oldCh[idxInOld];
            if (sameVnode(vnodeToMove, newStartVnode)) {
                patchVnode(
                    vnodeToMove,
                    newStartVnode,
                    insertedVnodeQueue,
                    newCh,
                    newStartIdx,
                );
                oldCh[idxInOld] = undefined;
                canMove &&
                    nodeOps.insertBefore(
                        parentElm,
                        vnodeToMove.elm,
                        oldStartVnode.elm,
                    );
            } else {
                // same key but different element. treat as new element
                createElm(
                    newStartVnode,
                    insertedVnodeQueue,
                    parentElm,
                    oldStartVnode.elm,
                    false,
                    newCh,
                    newStartIdx,
                );
            }
        }
        newStartVnode = newCh[++newStartIdx];
    }
}
if (oldStartIdx > oldEndIdx) {
    refElm = isUndef(newCh[newEndIdx + 1]) ? null : newCh[newEndIdx + 1].elm;
    addVnodes(
        parentElm,
        refElm,
        newCh,
        newStartIdx,
        newEndIdx,
        insertedVnodeQueue,
    );
} else if (newStartIdx > newEndIdx) {
    removeVnodes(oldCh, oldStartIdx, oldEndIdx);
}
```
