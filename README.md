# 系统使用文档

>系统采用了vela-security的整体开发框架，借助的是Lua和Go语言的绑定技术 ，文档主要介绍系统Lua的API使用和内置方法

## 符号解释
- 中括弧[]代表参数可以省略
- 多参数...标识

## 名词解释
> 解释一些lua特有属性和特性
- [Index](###Index)
- [NewIndex](###NewIndex)
- [Meta](###Meta)
- [NewMeta](###NewMeta)

### Index
> 其实就是实现了 lua 元表当中的__index 属性
- 比如下面的接口实现Index 在lua中获取到了A这个对象 的话 就可以直接获取show 和 debug的方法
```go
    package demo

    type A struct {
        //todo	
    }
    
	func (a *A) Index(L *lua.LState , key string) lua.LValue {
		switch key {
	        case "show":
				//todo
			case "debug":
                //todo
        }
		return nil
	}
```
- lua 对应使用技巧
```lua
    local a = A() -- A是初始A的方法
    -- a.show
    -- a.debug
```

## NewIndex
> 其实就是实现了 lua 元表当中的__newindex 属性
- 类似Index的方法使用
```go
    package demo

    type A struct {
		name string
		pass string
    }

    func (a *A) NewIndex(L *lua.LState , key string , val lua.LValue) {
		switch key {
		case "name":
			a.name = val.String()
		case "pass":
			a.pass = val.String()
		}
    }

```
- lua使用技巧
```lua
    local a = A()
    a.name = '123' --key:name val:123
    a.pass = '000' --key:pass val:123
```

## Meta
> 类似 Index 只是调用的时候采用的中括弧调用
```go
package main 
type A struct {}
func (a A) Meta(L *lua.LState , key lua.LValue) lua.LValue {}
```
- 使用
```lua
    local a = A()
    --a[name]
    --a[pass]
```

## NewMeta
> 类似 NewIndex 只是调用的时候采用的中括弧调用
```go
package main 
type A struct {}
func (a A) NewMeta(L *lua.LState , key lua.LValue , val lua.LValue) {}
```
- 使用
```lua
    local a = A()
    a[name] = 'aaa'
    a[pass] = '123'
```
