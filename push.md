# 上报数据
> 上报数据 默认采用JSON编码

## 内置方法
- [vela.push.sysinfo(value)](#)
- [vela.push.cpu (value)](#)
- [vela.push.disk(value)](#)
- [vela.push.listen(value)](#)
- [vela.push.memory(value)](#)
- [vela.push.socket(value)](#)
- [vela.push.network(value)](#)
- [vela.push.process(value)](#)
- [vela.push.service(value)](#)
- [vela.push.account(value)](#)
- [vela.push.filesystem(value)](#)
- [vela.push.task(value)](#)
- [vela.push.json(opcode ,value)](#)
- [err = vela.push(opcode, value)](#)


## 提交数据

- opcode 数据标码
- value 数据值

```lua
    vela.push.sysinfo(vela.system)
    
    vela.push.cpu(val)
    vela.push.disk(val)

    local opcode = 33 --自定义
    vela.push.json(opcode , val)
    
    local opcode = 55
    vela.push(opcode , val)
```