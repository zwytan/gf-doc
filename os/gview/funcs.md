
[TOC]

# 基本函数

变量可以使用符号 `|` 在函数间传递

```go
{{.value | Func1 | Func2}}
```

使用括号

```go
{{printf "nums is %s %d" (printf "%d %d" 1 2) 3}}
```

## and

```go
{{and .X .Y .Z}}
```

`and`会逐一判断每个参数，将返回第一个为空的参数，否则就返回最后一个非空参数

## call

```go
{{call .Field.Func .Arg1 .Arg2}}
```

`call`可以调用函数，并传入参数

调用的函数需要返回 1 个值 或者 2 个值，返回两个值时，第二个值用于返回`error`类型的错误。返回的错误不等于`nil`时，执行将终止。

## index

`index`支持`map`, `slice`, `array`, `string`，读取指定类型对应下标的值。

```go
{{index .Maps "name"}}
```

## len

```go
{{printf "The content length is %d" (.Content|len)}}
```

返回对应类型的长度，支持类型：`map`, `slice`, `array`, `string`, `chan`。

## not

`not`返回输入参数的否定值。

## or

```go
{{or .X .Y .Z}}
```

`or`会逐一判断每个参数，将返回第一个非空的参数，否则就返回最后一个参数。

## print

同`fmt.Sprint`。

## printf

同`fmt.Sprintf`。

## println

同`fmt.Sprintln`。

## urlquery

```go
{{urlquery "http://johng.cn"}}
```

将返回

```go
http%3A%2F%2Fjohng.cn
```

## eq / ne / lt / le / gt / ge

这类函数一般配合在`if`中使用

```go
`eq`: arg1 == arg2
`ne`: arg1 != arg2
`lt`: arg1 < arg2
`le`: arg1 <= arg2
`gt`: arg1 > arg2
`ge`: arg1 >= arg2
```

`eq`和其他函数不一样的地方是，支持多个参数，和下面的逻辑判断相同

```go
arg1==arg2 || arg1==arg3 || arg1==arg4 ...
```

与`if`一起使用

```go
{{if eq true .Var1 .Var2 .Var3}}...{{end}}
```

```go
{{if lt 100 200}}...{{end}}
```

# 内置函数

## text
```go
{{.value | text}}
```
将`value`变量值去掉HTML标签，仅显示文字内容（并且去掉`script`标签）。
示例：
```go
{{"<div>测试</div>"|text}}
// 输出: 测试
```

## html
别名`htmlencode`。
```go
{{.value | html}}
```
将`value`变量值进行html转义。
示例：
```go
{{"<div>测试</div>"|html}}
// 输出: &lt;div&gt;测试&lt;/div&gt;
```

## htmldecode
```go
{{.value | html}}
```
将`value`变量值进行html反转义。
示例：
```go
{{"&lt;div&gt;测试&lt;/div&gt;"|html}}
// 输出: <div>测试</div>
```

## url
同`urlquery`，别名`urlencode`。
```go
{{.url | url}}
```
将`url`变量值进行url转义。
示例：
```go
{{"https://gfer.me"|url}}
// 输出: https%3A%2F%2Fgfer.me
```

## urldecode
```go
{{.url | urldecode}}
```
将`url`变量值进行url反转义。
示例：
```go
{{"https%3A%2F%2Fgfer.me"|urldecode}}
// 输出: https://gfer.me
```


## date
```go
{{.timestamp | date .format}}
{{date .format .timestamp}}
{{date .format}}
```
将`timestamp`时间戳变量进行时间日期格式化，类似PHP的`date`方法，参数支持PHP`date`方法格式。
可参考【[gtime](os/gtime/index.md)】模块，及【[PHP date](http://php.net/manual/en/function.date.php)】
当`timestamp`变量为`空`(或者`0`)时，表示以当前时间作为时间戳参数执行打印。
示例：
```go
{{1540822968 | date "Y-m-d"}}
{{"1540822968" | date "Y-m-d H:i:s"}}
{{date "Y-m-d H:i:s"}}
// 输出: 
// 2018-10-29
// 2018-10-29 22:22:48
// 2018-12-05 10:22:00
```

## compare
```go
{{compare .str1 .str2}}
{{.str2 | compare .str1}}
```
将`str1`和`str2`进行字符串比较，返回值：
- 0 : `str1` == `str2`
- 1 : `str1` > `str2`
- -1 : `str1` < `str2`

示例：
```go
{{compare "A" "B"}}
{{compare "1" "2"}}
{{compare 2 1}}
{{compare 1 1}}
// 输出: 
// -1
// -1
// 1
// 0
```

## substr
```go
{{.str | substr .start .length}}
{{substr .start .length .str}}
```
将`str`从`start`索引位置(索引从0开始)进行字符串截取`length`，支持中文，类似PHP的`substr`函数。
示例：
```go
{{"我是中国人" | substr 2 -1}}
{{"我是中国人" | substr 2  2}}
// 输出:
// 中国人
// 中国
```

## strlimit
```go
{{.str | strlimit .length .suffix}}
```
将`str`字符串截取`length`长度，支持中文，超过长度则追加`suffix`字符串到末尾。
示例：
```go
{{"我是中国人" | strlimit 2  "..."}}
// 输出:
// 我是...
```

## hidestr
```go
{{.str | hidestr .percent .hide}}
```
将`str`字符串按照`percent`百分比从字符串中间向两边隐藏字符(主要用于姓名、手机号、邮箱地址、身份证号等的隐藏)，隐藏字符由`hide`变量定义。
支持中文，支持email格式。
示例：
```go
{{"热爱GF热爱生活" | hidestr 20  "*"}}
{{"热爱GF热爱生活" | hidestr 50  "*"}}
// 输出:
// 热爱GF*爱生活
// 热爱****生活
```

## highlight
```go
{{.str | highlight .key .color}}
```
将`str`字符串中的关键字`key`按照定义的颜色`color`进行前置颜色高亮。
示例：
```go
{{"热爱GF热爱生活" | highlight "GF" "red"}}
// 输出:
// 热爱<span style="color:red;">GF</span>热爱生活
```

## toupper/tolower
```go
{{.str | toupper}}
{{.str | tolower}}
```
将`str`字符串进行大小写转换。
示例：
```go
{{"gf" | toupper}}
{{"GF" | tolower}}
// 输出:
// GF
// gf
```

## nl2br
```go
{{.str | nl2br}}
```
将`str`字符串中的`\n/\r`替换为html中的`<br />`标签。
示例：
```go
{{"Go\nFrame" | nl2br}}
// 输出:
// Go<br />Frame
```

## `ghttp`模板函数

以下内置模板函数仅在`WebServer`下，即通过`ghttp`模块使用`ghttp.Response`/`gmvc.View`对象渲染模板引擎时有效。

### get

```go
{{get "参数名称"}}
```
用于获取GET方式传递的参数。

使用示例：
```go
{{if (get "name")}}
    {{get "name"}}
{{else}}
    NoName
{{end}}
```

### post

```go
{{post "参数名称"}}
```
用于获取POST方式传递的参数。

### request

```go
{{request "参数名称"}}
```
用于获取路由参数，以及GET/POST方式传递的参数。






