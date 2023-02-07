# awk
> 类似linux中awk的命令可以分割字符串获取

## 内置方法
> vela.awk(string , [sep] , [n]) 
- sep默认是空格

```lua
    local a = vela.awk("hello world!")
    print(a.size) -- 2 
    print(a[1])   -- hello 


    local v = vela.awk("hello\nworld" , "\n")
    print(a.size) -- 2 
    print(a[1])   -- hello 
```

