# 注册表
> 提供windows下注册表操作方式 支持: windows

## 内置方法
- 操作windows的注册表
- [vela.registry.USERS](#注册表对象)            &emsp;USERS 
- [vela.registry.LOCAL_MACHINE](#注册表对象)    &emsp;LOCAL_MACHINE
- [vela.registry.CLASSES_ROOT](#注册表对象)     &emsp;CLASSES_ROOT
- [vela.registry.CURRENT_CONFIG](#注册表对象)   &emsp;CURRENT_CONFIG
- [vela.registry.CURRENT_USER](#注册表对象)     &emsp;CURRENT_USER
- [vela.registry.PERFORMANCE_DATA](#注册表对象) &emsp;PERFORMANCE_DATA

## 注册表对象
> 获取的是注册表的对象数据 <br />
> USERS , LOCAL_MACHINE , CLASSES_ROOT , CURRENT_CONFIG , CURRENT_USER , PERFORMANCE_DATA

内置方法

- [set(key , perm)](#设置键值) &emsp; 设置键值
- [open(key)](#打开键值) &emsp;打开键值
- [pipe(key , function)](#遍历键值) &emsp;遍历键值


### 设置键值
> item , exist , err = win.registry.*.set(key , perm) <br />
> key: 路径 perm: 权限设置 <br />
> 返回 item: [key](#键对象) exist:是否已经存在 err:报错信息
 
权限列表:

- [ALL_ACCESS](#)
- [CREATE_LINK](#)
- [CREATE_SUB_KEY](#)
- [EXECUTE](#)
- [NOTIFY](#)
- [QUERY_VALUE](#)
- [READ](#)
- [SET_VALUE](#)
- [WOW65_32KEY](#)
- [wow65_64KEY](#)
- [WRITE](#)
- [ENUMERATE_SUB_KEYS](#)

```lua
    local chat , ex , err = cu.set("\\SOFTWARE\\Tencent\\WeChat" , "ALL_ACCESS")
    print(ex)  --是否已经存在了

    local item = chat.get(FileSavePath)
    print(item.set_qword(10))
    print(item.set_dword(10))
    print(item.strings{"aa" , "bb"})
    print(item.expand("aaaa"))

    for key , val in cu.map() do
        print(key)
        print(val)
    end 
```

## 打开键值
> [key](#键对象) = win.registry.*.open(key) <br />
> key: 路径 perm: 权限设置 <br />
> 返回 item: [key](#键对象) exist:是否已经存在 err:报错信息

```lua
    local cu = win.registry.CURRENT_USER
    
    local chat = cu.open("\\SOFTWARE\\Tencent\\WeChat")

    print(chat.s'FileSavePath') --获取FileSavePath string 类型
    print(chat.n'LANG_ID')      --获取LANG_ID      Int    类型
    print(chat.b'xxxx')         --获取xxxx         Bool   类型

    print(chat.get'xxxx')       --获取xxxx 
    
    local keys = chat.sub_keys() --子键
    local count = chat.sub_count() --数量

    local keys = chat.keys()       -- keys
    local count = chat.count()     -- 数量

```

## 遍历键值
> err = pipe(key , function)](#遍历键值) &emsp;遍历键值
内置方法:
- [item.prefix](#) &emsp;当前key的前缀
- [item.name](#)   
- [item.path](#)
- [item.value](#) 
```lua
    local err = win.registry.CURRENT_USER.pipe("\\SOFTWARE\\Tencent\\WeChat" , function(item)
        print(item.prefix)
        print(item.name)
        print(item.path)
        print(item.value)
    end)
```

## 键对象
> 封装每个key的状态和对象信息

内置方法:
- [item.s(string)](#) &emsp;设置为string值
- [item.n(string)](#) &emsp;设置为整形
- [item.b(string)](#) &emsp;设置binary
- [item.get(string)](#) &emsp;获取字段
- [item.keys()](#) &emsp; 获取所有key
- [item.count()](#) &emsp; 统计所有key
- [item.sub_keys()](#) &emsp;所有sub key
- [item.sub_count()](#)
- [item.map()](#)  &emsp;转成map对象
- [item.close()](#)
- [item.set_qword()](#) &emsp;设置为world
- [item.set_dwrod()](#) &emsp;设置为dobule word
- [item.set_strings()](#) &emsp;设置string数组
- [item.set_expand()](#)  

```lua
local item = chat.get("name")
print(item.set_qword(10))
print(item.set_dword(10))
print(item.strings{"aa" , "bb"})
print(item.expand("aaaa"))

for key , val in cu.map() do
    print(key)
    print(val)
end 
```