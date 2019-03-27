# gitee迁移到github

从`v1.5.x`版本开始，代码主库从`gitee`迁移到了`github`，因此如果是`v1.5.x`以下版本升级，需要做一下全局替换。

1. `import`地址修改：

    ```
    gitee.com/johng/gf
    ```
    替换为
    ```
    github.com/gogf/gf
    ```

2. 模块`gregex`和`gstr`从`util`分类迁移到了`text`分类：
    ```
    /util/gstr
    /util/gregex
    ```
    替换为
    ```
    /text/gstr
    /text/gregex
    ```








