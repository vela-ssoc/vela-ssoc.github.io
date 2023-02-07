# pipe
> 框架中一种统一的管道数据处理模式 说明中的pipe 的通用逻辑;大大增加了lua的开发中的灵活度和难度

## 说明
pipe 是支持多个函数,对象处理的管道服务,原理类似于linux中的|管道符号,用于处理复杂的数据流转的功能 <br />
很多服务都有内部流转对象,对应的文档格式 我们可以查看对应服务器中的数据接口,如tail 中流转的是获取的字符内容<br />
参数支持lua fucntion 和 go object 对象
```lua
    pipe(print) --默认将内容打印
    
    pipe(print , function(a , b)
        print(a , b)
    end)

    local kfk = vela.kfk.consumer{}

    pipe(print , kfk , function() end) --会启动将第一个参数的content 内容写入 kfk中
```