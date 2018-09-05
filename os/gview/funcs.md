
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

在Web Server的模板引擎内置了多个模板函数，需要注意的是，这些内置函数只在Web Server下使用`ghttp.Response`或者`gmvc.View`对象渲染模板下有效。

## get

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

```go
{{post "参数名称"}}
```
用于获取POST方式传递的参数。

## request

```go
{{request "参数名称"}}
```
用于获取路由参数，以及GET/POST方式传递的参数。