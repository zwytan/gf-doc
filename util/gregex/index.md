# gregex

使用方式：
```go
import "gitee.com/johng/gf/g/util/gregex"
```

方法列表： [godoc.org/github.com/johng-cn/gf/g/util/gregex](https://godoc.org/github.com/johng-cn/gf/g/util/gregex)
```go
func IsMatch(pattern string, src []byte) bool
func IsMatchString(pattern string, src string) bool
func MatchAllString(pattern string, src string) ([][]string, error)
func MatchString(pattern string, src string) ([]string, error)
func Quote(s string) string
func Replace(pattern string, replace, src []byte) ([]byte, error)
func ReplaceFunc(pattern string, src []byte, repl func(b []byte) []byte) ([]byte, error)
func ReplaceString(pattern, replace, src string) (string, error)
func ReplaceStringFunc(pattern string, src string, repl func(s string) string) (string, error)
func Validate(pattern string) error
```