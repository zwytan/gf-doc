[TOC]

# 开发环境配置

本文以`Goland`开发工具为基础，介绍在该IDE下的常用工具配置。

常用的工具包括：

1. `go fmt` : 统一的代码格式化工具（必须）。
1. `golangci-lint` : 静态代码质量检测工具，用于包的质量分析（推荐）。
1. `goimports` : 自动`import`依赖包工具（可选）。
1. `golint` : 代码规范检测，并且也检测单文件的代码质量，比较出名的Go质量评估站点[Go Report](https://goreportcard.com)在使用（可选）。

## `go fmt`, `goimports`, `golangci-lint`的配置

由于这三个工具是`Goland`自带的，因此配置比较简单，参考以下图文操作示例：

1. 在`Goland`的设置中，选择`Tools` - `File Watchers`，随后选择添加
    ![](/images/WX20190619-092825@2x.jpg)

1. 依次点击添加这3个工具，使用默认的配置即可
    ![](/images/WX20190619-093508@2x.jpg)

1. 随后在撸代码的过程中保存代码文件时将会自动触发这3个工具的自动检测。

## `golint`工具的安装及配置


### `golint`的安装
由于`Goland`没有自带`golint`工具，因此首先要自己去下载安装该工具。

使用以下命令安装：
```html
mkdir -p $GOPATH/src/golang.org/x/
cd $GOPATH/src/golang.org/x/
git clone https://github.com/golang/lint.git
git clone https://github.com/golang/tools.git
cd $GOPATH/src/golang.org/x/lint/golint
go install
```
安装成功之后将会在`$GOPATH/bin`目录下看到自动生成了`golint`二进制工具文件。

### `golint`的配置

1. 随后在`Goland`的`Tools` - `File Watchers`配置下，通过复制`go fmt`的配置
    ![](/images/WX20190619-201818@2x.jpg)

1. 修改`Name`, `Program`, `Arguments`三项配置，其中`Arguments`需要加上`-set_exit_status`参数，如图所示：
    ![](/images/WX20190619-201304@2x.jpg)

1. 保存即可，随后在代码编写中执行保存操作时将会自动触发`golint`工具检测。


## 配置备份导入

也可以通过保存以下`XML`配置文件内容，使用`import`导入功能即可完成配置（`golnt`还是得自己安装）。

![](/images/WX20190619-202618@2x.jpg)

文件内容：
```xml
<TaskOptions>
  <TaskOptions>
    <option name="arguments" value="fmt $FilePath$" />
    <option name="checkSyntaxErrors" value="true" />
    <option name="description" />
    <option name="exitCodeBehavior" value="ERROR" />
    <option name="fileExtension" value="go" />
    <option name="immediateSync" value="false" />
    <option name="name" value="go fmt" />
    <option name="output" value="$FilePath$" />
    <option name="outputFilters">
      <array />
    </option>
    <option name="outputFromStdout" value="false" />
    <option name="program" value="$GoExecPath$" />
    <option name="runOnExternalChanges" value="false" />
    <option name="scopeName" value="Project Files" />
    <option name="trackOnlyRoot" value="true" />
    <option name="workingDir" value="$ProjectFileDir$" />
    <envs>
      <env name="GOROOT" value="$GOROOT$" />
      <env name="GOPATH" value="$GOPATH$" />
      <env name="PATH" value="$GoBinDirs$" />
    </envs>
  </TaskOptions>
  <TaskOptions>
    <option name="arguments" value="-w $FilePath$" />
    <option name="checkSyntaxErrors" value="true" />
    <option name="description" />
    <option name="exitCodeBehavior" value="ERROR" />
    <option name="fileExtension" value="go" />
    <option name="immediateSync" value="false" />
    <option name="name" value="goimports" />
    <option name="output" value="$FilePath$" />
    <option name="outputFilters">
      <array />
    </option>
    <option name="outputFromStdout" value="false" />
    <option name="program" value="goimports" />
    <option name="runOnExternalChanges" value="false" />
    <option name="scopeName" value="Project Files" />
    <option name="trackOnlyRoot" value="true" />
    <option name="workingDir" value="$ProjectFileDir$" />
    <envs>
      <env name="GOROOT" value="$GOROOT$" />
      <env name="GOPATH" value="$GOPATH$" />
      <env name="PATH" value="$GoBinDirs$" />
    </envs>
  </TaskOptions>
  <TaskOptions>
    <option name="arguments" value="run --disable=typecheck $FileDir$" />
    <option name="checkSyntaxErrors" value="true" />
    <option name="description" />
    <option name="exitCodeBehavior" value="ERROR" />
    <option name="fileExtension" value="go" />
    <option name="immediateSync" value="false" />
    <option name="name" value="golangci-lint" />
    <option name="output" value="" />
    <option name="outputFilters">
      <array />
    </option>
    <option name="outputFromStdout" value="false" />
    <option name="program" value="golangci-lint" />
    <option name="runOnExternalChanges" value="false" />
    <option name="scopeName" value="Project Files" />
    <option name="trackOnlyRoot" value="true" />
    <option name="workingDir" value="$ProjectFileDir$" />
    <envs>
      <env name="GOROOT" value="$GOROOT$" />
      <env name="GOPATH" value="$GOPATH$" />
      <env name="PATH" value="$GoBinDirs$" />
    </envs>
  </TaskOptions>
  <TaskOptions>
    <option name="arguments" value="-set_exit_status $FilePath$" />
    <option name="checkSyntaxErrors" value="true" />
    <option name="description" />
    <option name="exitCodeBehavior" value="ERROR" />
    <option name="fileExtension" value="go" />
    <option name="immediateSync" value="false" />
    <option name="name" value="golint" />
    <option name="output" value="$FilePath$" />
    <option name="outputFilters">
      <array />
    </option>
    <option name="outputFromStdout" value="false" />
    <option name="program" value="golint" />
    <option name="runOnExternalChanges" value="false" />
    <option name="scopeName" value="Project Files" />
    <option name="trackOnlyRoot" value="true" />
    <option name="workingDir" value="$ProjectFileDir$" />
    <envs>
      <env name="GOROOT" value="$GOROOT$" />
      <env name="GOPATH" value="$GOPATH$" />
      <env name="PATH" value="$GoBinDirs$" />
    </envs>
  </TaskOptions>
</TaskOptions>
```
