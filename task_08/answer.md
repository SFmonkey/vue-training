## 结论

-   MVC 的意思是，软件可以分为三个部分

    -   model：数据保存
    -   view： 用户界面
    -   controller： 业务逻辑

    各部分的通信都是单向的，view 传送指令到 controller，controller 完成业务逻辑后，要求 model 改变状态，model 将新的数据发送到 view，用户得到反馈

-   MVP 模式将 controller 改名为 presenter ，同时改变了通信方向

    -   各部分的通信都是双向的
    -   view 和 model 不再发生通信
    -   view 非常的薄，不部署任何业务逻辑，称为被动视图

-   MVVM 将 presenter 改名为 ViewModel

    MVVM 与 MVP 唯一的区别是，它采用双向绑定，view 层发生的变动，自动地反映在 viewModel 上，反之亦然。
