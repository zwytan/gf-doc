# gtest

`gtest`提供了常用的单元测试方法。

**使用方式**：
```go
import "github.com/gogf/gf/g/test/gtest"
```

**接口文档**： 

https://godoc.org/github.com/gogf/gf/g/test/gtest



**使用示例**： 

例如`gstr`模块其中一个单元测试用例：

```go
package gstr_test

import (
    "github.com/gogf/gf/g/test/gtest"
    "github.com/gogf/gf/g/text/gstr"
    "testing"
)

func Test_Trim(t *testing.T) {
    gtest.Case(t, func() {
        gtest.Assert(gstr.Trim(" 123456\n "),      "123456")
        gtest.Assert(gstr.Trim("#123456#;", "#;"), "123456")
    })
}
```
也可以这样使用：

```go
package gstr_test

import (
    . "github.com/gogf/gf/g/test/gtest"
    "github.com/gogf/gf/g/text/gstr"
    "testing"
)

func Test_Trim(t *testing.T) {
    Case(t, func() {
        Assert(gstr.Trim(" 123456\n "),      "123456")
        Assert(gstr.Trim("#123456#;", "#;"), "123456")
    })
}
```
一个单元测试用例可以包含多个`Case`，一个`Case`也可以执行多个断言。
断言成功时直接PASS，但是如果断言失败，会输出如下类似的错误信息，并终止当前单元测试用例的继续执行（不会终止后续的其他单元测试用例）。
```html
=== RUN   Test_Trim
[ASSERT] EXPECT 123456#; == 123456
1. /Users/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/g/text/gstr/gstr_z_unit_trim_test.go:20
2. /Users/john/Workspace/Go/GOPATH/src/github.com/gogf/gf/g/text/gstr/gstr_z_unit_trim_test.go:18
--- FAIL: Test_Trim (0.00s)
FAIL
```
