
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

对应`fmt.Sprint`。

## printf

对应`fmt.Sprintf`。

## println

对应`fmt.Sprintln`。

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

## html/htmlencode
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
{{.value | html}}
```
将`value`变量值进行html反转义。
示例：
```go
{{"&lt;div&gt;测试&lt;/div&gt;"|html}}
// 输出: <div>测试</div>
```

## url/urlencode
```go
{{.url | url}}
{{url .url}}
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
{{urldecode .url}}
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
```
将`timestamp`时间戳变量进行时间日期格式化，类似PHP的`date`方法，参数支持PHP`date`方法格式。
可参考【[gtime](os/gtime/index.md)】模块，及【[PHP date](http://php.net/manual/en/function.date.php)】
示例：
```go
{{1540822968 | date "Y-m-d"}}
{{"1540822968" | date "Y-m-d H:i:s"}}
// 输出: 
// 2018-10-29
// 2018-10-29 22:22:48
```

## compre
```go
{{compre .str1 .str2}}
{{.str2 | compre .str1}}
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
将`str`进行字符串截取，支持中文，类似PHP的`substr`函数。
示例：
```go
{{"我是中国人" | substr 2 -1}}
{{"我是中国人" | substr 2  2}}
// 输出:
// 中国人
// 中国
```

## get
> 仅在`Web Server`下，即通过`ghttp`模块使用`ghttp.Response`/`gmvc.View`对象渲染模板引擎时有效。

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

## post
> 仅在`Web Server`下，即通过`ghttp`模块使用`ghttp.Response`/`gmvc.View`对象渲染模板引擎时有效。

```go
{{post "参数名称"}}
```
用于获取POST方式传递的参数。

## request
> 仅在`Web Server`下，即通过`ghttp`模块使用`ghttp.Response`/`gmvc.View`对象渲染模板引擎时有效。

```go
{{request "参数名称"}}
```
用于获取路由参数，以及GET/POST方式传递的参数。