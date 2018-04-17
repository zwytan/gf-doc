
[TOC]

>[danger] # 基本函数

变量可以使用符号 `|` 在函数间传递

```
{{.value | Func1 | Func2}}
```

使用括号

```
{{printf "nums is %s %d" (printf "%d %d" 1 2) 3}}
```

>[success] ## and

```
{{and .X .Y .Z}}
```

and 会逐一判断每个参数，将返回第一个为空的参数，否则就返回最后一个非空参数

>[success] ## call

```
{{call .Field.Func .Arg1 .Arg2}}
```

call 可以调用函数，并传入参数

调用的函数需要返回 1 个值 或者 2 个值，返回两个值时，第二个值用于返回 error 类型的错误。返回的错误不等于 nil 时，执行将终止。

>[success] ## index

index 支持 map, slice, array, string，读取指定类型对应下标的值

```
{{index .Maps "name"}}
```

>[success] ## len

```
{{printf "The content length is %d" (.Content|len)}}
```

返回对应类型的长度，支持类型：map, slice, array, string, chan

>[success] ## not

not 返回输入参数的否定值，if true then false else true

>[success] ## or

```
{{or .X .Y .Z}}
```

or 会逐一判断每个参数，将返回第一个非空的参数，否则就返回最后一个参数

>[success] ## print

对应 fmt.Sprint

>[success] ## printf

对应 fmt.Sprintf

>[success] ## println

对应 fmt.Sprintln

>[success] ## urlquery

```
{{urlquery "http://johng.cn"}}
```

将返回

```
http%3A%2F%2Fjohng.cn
```

>[success] ## eq / ne / lt / le / gt / ge

这类函数一般配合在 if 中使用

```
`eq`: arg1 == arg2
`ne`: arg1 != arg2
`lt`: arg1 < arg2
`le`: arg1 <= arg2
`gt`: arg1 > arg2
`ge`: arg1 >= arg2
```

eq 和其他函数不一样的地方是，支持多个参数，和下面的逻辑判断相同

```
arg1==arg2 || arg1==arg3 || arg1==arg4 ...
```

与 if 一起使用

```
{{if eq true .Var1 .Var2 .Var3}}{{end}}
```

```
{{if lt 100 200}}{{end}}
```
