
[TOC]

```gvalid```包实现了非常强大易用的数据校验功能，封装了```40种```常用的校验规则，支持单数据多规则校验、多数据多规则批量校验、自定义错误信息、自定义正则校验、支持struct tag规则及提示信息绑定等特性。

使用方式：
```go
import "gitee.com/johng/gf/g/util/gvalid"
```

# 校验规则

40种常用的校验规则：
```shell
required             格式：required                              说明：必需参数
required-if          格式：required-if:field,value,...           说明：必需参数(当任意所给定字段值与所给值相等时，即：当field字段的值为value时，当前验证字段为必须参数)
required-unless      格式：required-unless:field,value,...       说明：必需参数(当所给定字段值与所给值都不相等时，即：当field字段的值不为value时，当前验证字段为必须参数)
required-with        格式：required-with:field1,field2,...       说明：必需参数(当所给定任意字段值不为空时)
required-with-all    格式：required-with-all:field1,field2,...   说明：必须参数(当所给定所有字段值都不为空时)
required-without     格式：required-without:field1,field2,...    说明：必需参数(当所给定任意字段值为空时)
required-without-all 格式：required-without-all:field1,field2,...说明：必须参数(当所给定所有字段值都为空时)
date                 格式：date                                  说明：参数为常用日期类型，格式：2006-01-02, 20060102, 2006.01.02
date-format          格式：date-format:format                    说明：判断日期是否为指定的日期格式，format为Go日期格式(可以包含时间)
email                格式：email                                 说明：EMAIL邮箱地址
phone                格式：phone                                 说明：手机号
telephone            格式：telephone                             说明：国内座机电话号码，"XXXX-XXXXXXX"、"XXXX-XXXXXXXX"、"XXX-XXXXXXX"、"XXX-XXXXXXXX"、"XXXXXXX"、"XXXXXXXX"
passport             格式：passport                              说明：通用帐号规则(字母开头，只能包含字母、数字和下划线，长度在6~18之间)
password             格式：password                              说明：通用密码(任意可见字符，长度在6~18之间)
password2            格式：password2                             说明：中等强度密码(在弱密码的基础上，必须包含大小写字母和数字)
password3            格式：password3                             说明：强等强度密码(在弱密码的基础上，必须包含大小写字母、数字和特殊字符)
postcode             格式：postcode                              说明：中国邮政编码
id-number            格式：id-number                             说明：公民身份证号码
qq                   格式：qq                                    说明：腾讯QQ号码
ip                   格式：ip                                    说明：IPv4/IPv6地址
ipv4                 格式：ipv4                                  说明：IPv4地址
ipv6                 格式：ipv6                                  说明：IPv6地址
mac                  格式：mac                                   说明：MAC地址
url                  格式：url                                   说明：URL
domain               格式：domain                                说明：域名
length               格式：length:min,max                        说明：参数长度为min到max(长度参数为整形)
min-length           格式：min-length:min                        说明：参数长度最小为min(长度参数为整形)
max-length           格式：max-length:max                        说明：参数长度最大为max(长度参数为整形)
between              格式：between:min,max                       说明：参数大小为min到max(支持整形和浮点类型参数)
min                  格式：min:min                               说明：参数最小为min(支持整形和浮点类型参数)
max                  格式：max:max                               说明：参数最大为max(支持整形和浮点类型参数)
json                 格式：json                                  说明：判断数据格式为JSON
integer              格式：integer                               说明：整数
float                格式：float                                 说明：浮点数
boolean              格式：boolean                               说明：布尔值(1,true,on,yes:true | 0,false,off,no,"":false)
same                 格式：same:field                            说明：参数值必需与field参数的值相同
different            格式：different:field                       说明：参数值不能与field参数的值相同
in                   格式：in:value1,value2,...                  说明：参数值应该在value1,value2,...中(字符串匹配)
not-in               格式：not-in:value1,value2,...              说明：参数值不应该在value1,value2,...中(字符串匹配)
regex                格式：regex:pattern                         说明：参数值应当满足正则匹配规则pattern
```

# 校验方法

校验方法列表：
```go
func Check(val interface{}, rules string, msgs interface{}, params ...map[string]interface{}) map[string]string
func CheckMap(params map[string]interface{}, rules map[string]string, msgs ...map[string]interface{}) map[string]map[string]string
func CheckStruct(st interface{}, rules map[string]string, msgs ...map[string]interface{}) map[string]map[string]string
func SetDefaultErrorMsgs(msgs map[string]string)
```
```Check*```方法只有在返回nil的情况下，表示数据校验成功，否则返回校验出错的数据项(```CheckMap```)以及对应的规则和错误信息的map，具体请查看后续示例代码更容易理解。

# 使用示例

下面我们来举几个例子，看看如何使用```gvalid```来实现数据校验。

## 单数据校验 - Check

1、校验数据长度，使用默认的错误提示
```go
rule := "length:6,16"
if m := gvalid.Check("123456", rule);  m != nil {
    fmt.Println(m)
}
if m := gvalid.Check("12345", rule);  m != nil {
    fmt.Println(m)
}

// 输出： map[length:字段长度为6到16个字符]
```

2、校验数据类型及大小，并且使用自定义的错误提示
```go
rule := "integer|between:6,16"
msgs := "请输入一个整数|参数大小不对啊老铁"
fmt.Println(gvalid.Check(5.66, rule, msgs))

// 输出： map[integer:请输入一个整数 between:参数大小不对啊老铁]
```

可以看到，多个规则以及多个自定义错误提示之间使用英文“|”号进行分割，注意自定义错误提示的顺序和多规则的顺序一一对应。msgs参数除了支持string类型以外，还支持**map[string]string类型**，请看以下例子：
```go
rule := "url|min-length:11"
msgs := map[string]string{
    "url"       : "请输入正确的URL地址",
    "minlength" : "地址长度至少为:min位"
}
fmt.Println(gvalid.Check("http://johngcn", rule, msgs))

// 输出： map[url:请输入正确的URL地址]
```

3、使用自定义正则校验数据格式，使用默认错误提示
```go
// 参数长度至少为6个数字或者6个字母，但是总长度不能超过16个字符
rule := `regex:\d{6,}|\D{6,}|max-length:16`
if m := gvalid.Check("123456", rule);  m != nil {
    fmt.Println(m)
}
if m := gvalid.Check("abcde6", rule);  m != nil {
    fmt.Println(m)
}

// 输出： map[regex:字段值不合法]
```

## 多数据校验 - CheckMap

gvalid支持对多数据进行校验，支持map和struct类型，分别使用```CheckMap```和```CheckStruct```方法实现。

1、多数据多规则校验，使用默认错误提示
```go
params := map[string]interface{} {
    "passport"  : "john",
    "password"  : "123456",
    "password2" : "1234567",
}
rules := map[string]string {
    "passport"  : "required|length:6,16",
    "password"  : "required|length:6,16|same:password2",
    "password2" : "required|length:6,16",
}
fmt.Println(gvalid.CheckMap(params, rules))

// 输出： map[passport:map[length:字段长度为6到16个字符] password:map[same:字段值不合法]]
```

2、多数据多规则校验，使用自定义错误提示
```go
params := map[string]interface{} {
    "passport"  : "john",
    "password"  : "123456",
    "password2" : "1234567",
}
rules := map[string]string {
    "passport"  : "required|length:6,16",
    "password"  : "required|length:6,16|same:password2",
    "password2" : "required|length:6,16",
}
msgs  := map[string]interface{} {
    "passport" : "账号不能为空|账号长度应当在:min到:max之间",
    "password" : map[string]string {
        "required" : "密码不能为空",
        "same"     : "两次密码输入不相等",
    },
}
fmt.Println(gvalid.CheckMap(params, rules, msgs))

// 输出： map[passport:map[length:账号长度应当在6到16之间] password:map[same:两次密码输入不相等]]
```

该示例同时也展示了自定义错误传递的两种数据类型，```string```或者```map[string]string```。其中```map[string]string```类型参数需要指定对应字段、对应规则的错误提示信息，是一个二维的“关联数组”。


## 多数据校验 - CheckStruct

```CheckStruct```的使用方式同CheckMap，除了第一个参数为```struct类型```的对象（也可以是对象指针）。**但是需要注意的一个细节是，struct的属性会有默认值，因此某些情况下会引起required规则的失效，因此根据实际情况配合多种规则一起校验会是一个比较严谨的做法。**

### 示例1，使用map指定规则及提示信息
```go
type Object struct {
    Name string
    Age  int
}
rules := map[string]string {
    "Name" : "required|length:6,16",
    "Age"  : "between:18,30",
}
msgs  := map[string]interface{} {
    "Name" : map[string]string {
        "required" : "名称不能为空",
        "length"   : "名称长度为:min到:max个字符",
    },
    "Age"  : "年龄为18到30周岁",
}
obj := Object{Name : "john"}
// 也可以是
// obj := &Object{Name : "john"}
fmt.Println(gvalid.CheckStruct(obj, rules, msgs))

// 输出： map[Age:map[between:年龄为18到30周岁] Name:map[length:名称长度为6到16个字符]]
```

在以上示例中，```Age```属性由于默认值0的存在，因此会引起```required```规则的失效，因此这里没有使用```required```规则而是使用```between```规则来进行校验。

### 示例2，使用struct tag绑定规则及提示信息

```go
package main

import (
    "gitee.com/johng/gf/g/util/gutil"
    "gitee.com/johng/gf/g/util/gvalid"
)

type User struct {
    Uid   int    `gvalid:"uid      @integer|min:1"`
    Name  string `gvalid:"name     @required|length:6,30#请输入用户名称|用户名称长度非法"`
    Pass1 string `gvalid:"password1@required|password3"`
    Pass2 string `gvalid:"password2@required|password3|same:password1#||两次密码不一致，请重新输入"`
}

func main() {
    user := &User{
        Name : "john",
        Pass1: "Abc123!@#",
        Pass2: "123",
    }

    // 使用结构体定义的校验规则和错误提示进行校验
    gutil.Dump(gvalid.CheckStruct(user, nil))

    // 自定义校验规则和错误提示，对定义的特定校验规则和错误提示进行覆盖
    rules := map[string]string {
        "Uid" : "required",
    }
    msgs  := map[string]interface{} {
        "Pass2" : map[string]string {
            "password3" : "名称不能为空",
        },
    }
    gutil.Dump(gvalid.CheckStruct(user, rules, msgs))
}
```
可以看到，我们可以对在struct定义时使用了```gvalid```的标签属性来绑定校验的规则及错误提示信息，该标签规则如下：
```
[属性别名@]校验规则[#错误提示]
```
其中，属性别名和错误提示为非必需字段。**属性别名**指定在校验中使用的对应struct属性的别名，同时校验成功后的map中的也将使用该别名返回，例如在处理请求表单时比较有用，因为表单的字段名称往往和struct的属性名称不一致；**校验规则**是必需参数，具体请查看本章节上面的相关说明；**错误提示**表示自定义的错误提示信息，当规则校验时对默认的错误提示信息进行覆盖。

在此示例代码中，```same:password1```规则同使用```same:Pass2```规则是一样的效果。也就是说，在数据校验中，可以同时使用原有的struct属性名称，也可以使用别名。但是，返回的结果中只会使用别名返回，这也是别名最大的用途。

此外，在使用```CheckStruct```方法对struct对象进行校验时，也可以传递校验或者和错误提示参数，这个时候会覆盖struct在定义时绑定的对应参数。

以上示例执行后，输出结果为：
```json
{
	"name": {
		"length": "字段长度为6到30个字符"
	},
	"password2": {
		"password3": "密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符",
		"same": "字段值不合法"
	}
}

{
	"name": {
		"length": "字段长度为6到30个字符"
	},
	"password2": {
		"password3": "密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符",
		"same": "字段值不合法"
	}
}
```


# 错误提示

任何时候，我们都可以通过```gvalid.SetDefaultErrorMsgs```方法来批量设置默认的错误提示信息（特别是针对多语言环境中），但是需要注意的是，修改是全局变化的，请注意可能会对其他模块校验信息的影响。通常建议为针对特定的校验单独配置不同的校验错误提示信息。默认的错误提示如下：

```go
var defaultMessages = map[string]string {
    "required"             : "字段不能为空",
    "required-if"          : "字段不能为空",
    "required-unless"      : "字段不能为空",
    "required-with"        : "字段不能为空",
    "required-with-all"    : "字段不能为空",
    "required-without"     : "字段不能为空",
    "required-without-all" : "字段不能为空",
    "date"                 : "日期格式不正确",
    "date-format"          : "日期格式不正确",
    "email"                : "邮箱地址格式不正确",
    "phone"                : "手机号码格式不正确",
    "telephone"            : "电话号码格式不正确",
    "passport"             : "账号格式不合法，必需以字母开头，只能包含字母、数字和下划线，长度在6~18之间",
    "password"             : "密码格式不合法，密码格式为任意6-18位的可见字符",
    "password2"            : "密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母和数字",
    "password3"            : "密码格式不合法，密码格式为任意6-18位的可见字符，必须包含大小写字母、数字和特殊字符",
    "postcode"             : "邮政编码不正确",
    "id-number"            : "身份证号码不正确",
    "qq"                   : "QQ号码格式不正确",
    "ip"                   : "IP地址格式不正确",
    "ipv4"                 : "IPv4地址格式不正确",
    "ipv6"                 : "IPv6地址格式不正确",
    "mac"                  : "MAC地址格式不正确",
    "url"                  : "URL地址格式不正确",
    "domain"               : "域名格式不正确",
    "length"               : "字段长度为:min到:max个字符",
    "min-length"           : "字段最小长度为:min",
    "max-length"           : "字段最大长度为:max",
    "between"              : "字段大小为:min到:max",
    "min"                  : "字段最小值为:min",
    "max"                  : "字段最大值为:max",
    "json"                 : "字段应当为JSON格式",
    "xml"                  : "字段应当为XML格式",
    "array"                : "字段应当为数组",
    "integer"              : "字段应当为整数",
    "float"                : "字段应当为浮点数",
    "boolean"              : "字段应当为布尔值",
    "same"                 : "字段值不合法",
    "different"            : "字段值不合法",
    "in"                   : "字段值不合法",
    "not-in"               : "字段值不合法",
    "regex"                : "字段值不合法",
}
```

























