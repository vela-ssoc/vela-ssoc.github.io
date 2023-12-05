# 插件模式

目前支持linux 默认插件功能  可以直接加载（目前不支持卸载功能）

## 开发插件

为了保证项目路径新的库文件一直 我们需要 新增一个项目 ，利用vela-cli命令

```bash
    # 初始化环境 
    vela-cli.exe init [path]
    
    # 创建功能模块
    vela-cli.exe create ai [path]
    # 同步模块 
    go mod tidy
  
    # 运行 或者 调试 
    go run main.go
```

生成如下代码：

```go
package ai

import (
    "github.com/vela-ssoc/vela-kit/lua"
    "github.com/vela-ssoc/vela-kit/vela"
)

var xEnv vela.Environment

func say(L *lua.LState) int {
    body := lua.Format(L, 0)
    L.Push(lua.S2L(body))
    return 1
}

func indexL(L *lua.LState, key string) lua.LValue {
    switch key {
    case "banner":
        return lua.S2L("crawler succeed")
    }
    return lua.LNil
}

func WithEnv(env vela.Environment) {
    xEnv = env
    xEnv.Set("say", lua.NewExport("lua.ai.export", 
                    lua.WithIndex(indexL), 
                    lua.WithFunc(sayL)))
}


//注意插件需要main函数调用
func main(){}
```

生成插件 编译命令

```bash
# 调试模式编译命令
go build -gcflags "all=-N -l" -o ai.so -buildmode=plugin vela-ai/lua.go

# 生成环境编译命令
go build -ldflags="-s -w" -o ai.so -buildmode=plugin vela-ai/lua.go
```



## 加载插件

可以直接调用对应的so文件

```lua
-- 第一个参数: 文件名
-- 第二个参数: 是否远程
local ai = vela.plugin("ai.so" , false)

vela.ai.say("hello")    -- 注入的say方法
print(vela.ai.banner)   -- 注入的banner信息
```



## 注意事项

1.编译的GO版本必须完全一致

2.双方依赖的公共第三方库版本必须完全一致

3.GOPATH也得保持一致，这一点可以在编译时候使用trimpath参数解决

4.插件加载之后无法卸载


