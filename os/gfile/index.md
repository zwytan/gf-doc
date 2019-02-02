
# gfile

通用的文件管理模块。

使用方式：
```go
import "github.com/gogf/gf/g/os/gfile"
```

接口文档：[godoc.org/github.com/gogf/gf/g/os/gfile](https://godoc.org/github.com/gogf/gf/g/os/gfile)
```go
func Basename(path string) string
func Chmod(path string, mode os.FileMode) error
func Copy(src string, dst string) error
func Create(path string) error
func Dir(path string) string
func DirNames(path string) ([]string, error)
func Exists(path string) bool
func Ext(path string) string
func FormatSize(raw float64) string
func Glob(pattern string, onlyNames ...bool) ([]string, error)
func GoRootOfBuild() string
func Home() (string, error)
func Info(path string) *os.FileInfo
func IsDir(path string) bool
func IsFile(path string) bool
func IsReadable(path string) bool
func IsWritable(path string) bool
func MTime(path string) int64
func MTimeMillisecond(path string) int64
func MainPkgPath() string
func Mkdir(path string) error
func Move(src string, dst string) error
func Open(path string) (*os.File, error)
func OpenWithFlag(path string, flag int) (*os.File, error)
func OpenWithFlagPerm(path string, flag int, perm int) (*os.File, error)
func Pwd() string
func ReadableSize(path string) string
func RealPath(path string) string
func Remove(path string) error
func Rename(src string, dst string) error
func ScanDir(path string, pattern string, recursive ...bool) ([]string, error)
func SelfDir() string
func SelfPath() string
func Size(path string) int64
func TempDir() string

func Truncate(path string, size int) error
func PutBinContents(path string, content []byte) error
func PutBinContentsAppend(path string, content []byte) error
func PutContents(path string, content string) error
func PutContentsAppend(path string, content string) error
func GetBinContents(path string) []byte
func GetBinContentsByTwoOffsets(file *os.File, start int64, end int64) []byte
func GetBinContentsByTwoOffsetsByPath(path string, start int64, end int64) []byte
func GetBinContentsTilChar(file *os.File, char byte, start int64) ([]byte, int64)
func GetBinContentsTilCharByPath(path string, char byte, start int64) ([]byte, int64)
func GetContents(path string) string
func GetNextCharOffset(file *os.File, char byte, start int64) int64
func GetNextCharOffsetByPath(path string, char byte, start int64) int64
```

`gfile`模块是对文件操作的进一步封装，提供了常用的，简易的API来操作底层文件，隐藏了复杂的底层实现细节。
