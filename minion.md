# minion
> 链接broker 控制端的lib 提供我们跟中心端交互的接口

## 内置方法
- [vela.push(opcode, value...)](#上传数据) &emsp;上传数据
- [vela.stream.kfk{cfg}](#代理producer) &emsp;代理kafka生产者
- [vela.stream.es{cfg}](#代理Elastic) &emsp;代理elastic服务
- [vela.stream.tcp{cfg}](#代理tcp) &emsp;代理TCP

## 上传数据
> err = vela.push(mime , value)
- 向服务器端提交数据
- mime 数据编码
- value 数据值


扩展方法：
- vela.push.sysinfo(value)
- vela.push.cpu (value)
- vela.push.disk(value)
- vela.push.listen(value)
- vela.push.memory(value)
- vela.push.socket(value)
- vela.push.network(value)
- vela.push.process(value)
- vela.push.service(value)
- vela.push.account(value)
- vela.push.filesystem(value)
- vela.push.task(value)
- vela.push.json(opcode ,value)

```lua
    local opcode = vela.require("opcode")
    local err = vela.push(mime.xxx , aaa)
```

## 代理producer
> kfk = vela.stream.kfk{name , addr , topic}
- 利用tunnel代理链接远程地址, 满足lua.writer
- name 名称
- addr 远程地址
- topic 默认topic
- [kfk.push(string)](#)
- [kfk.clone(topic)](#)  &emsp;满足lua.writer 只是重定向topic
- [kfk.start()](#)


```lua
    local kfk = vela.stream.kfk{
        name  = "abcd",
        addr  = {"192.168.1.1:9092" , "192.168.1.10:9092"},
        topic = "aa"
    }
    kfk.start()
    kfk.push("x")
    kfk.push("2")

    local c = kfk.clone("bb")
    c.push("b")
    c.push("c")
```

## 代理Elastic
> es = vela.stream.es{name , addr , index , username , password}
- 利用tunnel代理链接远程ES服务
- [es.push(v)](#)
- [es.start()]

```lua
    local es = vela.stream.es{
        name = "es",
        addr = "http://192.168.1.1:9200",
        index = "hello",
        --username = "aaaa",
        --password = ""
    }
    es.start()
```

## 代理TCP
> tcp = vela.stream.tcp{name , addr}
- 利用tunnel代理tcp链接 , 满足lua.writer

```lua
    local tcp = vela.stream.tcp{
        name = "tcp",
        addr = {"192.168.1.1:9092"}
    }
```
