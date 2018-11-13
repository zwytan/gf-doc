# 多数据校验 - CheckMap

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
if e := gvalid.CheckMap(params, rules); e != nil {
    gutil.Dump(e)
}

/*
输出： 
{
	"passport": {
		"length": "字段长度为6到16个字符"
	},
	"password": {
		"same": "字段值不合法"
	}
}
*/
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
if e := gvalid.CheckMap(params, rules, msgs); e != nil {
    gutil.Dump(e)
}

/*
输出：
{
	"passport": {
		"length": "账号长度应当在6到16之间"
	},
	"password": {
		"same": "两次密码输入不相等"
	}
}
*/
```

该示例同时也展示了自定义错误传递的两种数据类型，```string```或者```map[string]string```。其中```map[string]string```类型参数需要指定对应字段、对应规则的错误提示信息，是一个二维的“关联数组”。
