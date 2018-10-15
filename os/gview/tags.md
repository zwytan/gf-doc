
[TOC]

# 基本语法

模板引擎默认使用了 `{{` 和 `}}` 作为左右闭合标签，开发者可通过`gview.SetDelimiters`方法设置自定义的模板闭合标签。

使用 `.` 来访问当前对象的值(模板全局变量)。

使用 `$` 来引用当前模板根级的上下文。

使用 `$var` 来访问特定的模板变量。


**模板中支持的 go 语言符号**

```go
{{"string"}}     // 一般 string
{{`raw string`}} // 原始 string
{{'c'}}          // byte
{{print nil}}    // nil 也被支持
```

**模板中的 pipeline**

可以是上下文的变量输出，也可以是函数通过管道传递的返回值

```go
{{. | FuncA | FuncB | FuncC}}
```

当`pipeline`的值等于:

* `false`或`0`
* `nil的指针`或`interface`
* 长度为`0`的`array`, `slice`, `map`, `string`

那么这个`pipeline`被认为是空。

## if ... else ... end

```go
{{if pipeline}}...{{end}}
```

`if`判断时，`pipeline`为空时，相当于判断为`false`。


支持嵌套的循环

```go
{{if .condition}}
    ...
{{else}}
	{{if .condition2}}
        ...
    {{end}}
{{end}}
```

也可以使用`else if`进行

```go
{{if .condition}}
    ...
{{else if .condition2}}
    ...
{{else}}
    ...
{{end}}
```

## range ... end

```go
{{range pipeline}} {{.}} {{end}}
```

`pipeline`支持的类型为`array`, `slice`, `map`, `channel`。

注意：在`range`循环内部，`.` 被覆盖为以上类型的子元素。

此外，对应的值长度为`0`时，`range`不会执行，`.` 不会改变。

## with ... end

```go
{{with pipeline}}...{{end}}
```

`with`用于重定向`pipeline`

```go
{{with .Field.NestField.SubField}}
	{{.Var}}
{{end}}
```


## define

`define`可以用来**定义自模板**(给一段模板内容定义一个模板名称)，可用于模块定义和模板嵌套(使用在`template`标签中)。

```go
{{define "loop"}}
	<li>{{.Name}}</li>
{{end}}
```

使用 template 调用模板

```go
<ul>
	{{range .Items}}
		{{template "loop" .}}
	{{end}}
</ul>
```

## template

```go
{{template "模板名称" pipeline}}
```

将对应的上下文`pipeline`传给模板，才可以在模板中调用。

注意：`template`标签参数为`模板名称`，而不是模板文件路径，`template`标签不支持模板文件路径。


## include

**该标签为`gf`模板引擎新增标签**

```go
{{include "模板文件名(需要带完整文件名后缀)" pipeline}}
```

在模板中可以使用`include`标签载入其他模板，并且使用当前模板引擎已有的数据对于模板的分模块处理很有用处。模板文件名支持**相对路径**以及文件的系统**绝对路径**。如果想要把当前模板的模板变量传递给子模板(嵌套模板)，可以这样：
```go
{{include "模板文件名(需要带完整文件名后缀)" .}}
```

与`template`标签的区别是：`include`仅支持**文件路径**，不支持**模板名称**；而`tempalte`标签仅支持**模板名称**，不支持**文件路径**。

## 注释

允许多行文本注释，不允许嵌套。

```go
{{/*
comment content
support new line
*/}}
```
