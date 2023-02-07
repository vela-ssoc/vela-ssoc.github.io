# windows事件监控
> 提供windows的event事件采集方法 支持:windows

## 内置方法
- [vela.evtlog(cfg)](#监听事件) &emsp;监听windows事件

## 监听事件
> [proc](#监听对象) = win.evtlog(cfg) <br />
> cfg: 配置信息(table 结构) 返回: [procdata](#监听对象)是任务对象

配置字段:
- name      &emsp;对象名称
- begin     &emsp;开始位置: begin 从第一个事件开始
- to        &emsp;事件输出
- bucket    &emsp;数据偏移存储
- pass      &emsp;忽略事件

```lua
    local w = win.evtlog{
        name = "evtlog",     --对象名称
        begin = true,        --开始位置: begin 从第一个事件开始
        to    = output,      --事件输出
        bucket = "evt_db",   --数据偏移存储
        pass   = {4091,1321} --忽略事件
    }
    w.start()
```

## 监控对象
> windows 监控任务 标识 满足 任务系统通用接口

内置方法:

- subscribe(string...) &emsp; 订阅channel对象:Get-EventLog -List查看获取
- pipe(px)             &emsp; 处理[event](#event事件)事件
- ignore(cnd)          &emsp; 忽略[event](#event事件)事件
- filter(cnd)          &emsp; 捕获[event](#event事件)事件
- to(output)           &emsp; 日志采集 output 对象
- limit(n , t)         &emsp; 限制速度
- start()              &emsp; 启动对象

```lua
    local output = vela.kfk{}

    local w = win.evtlog{
        name = 'evtlog',
        begin = true,
        to    = output
    }
    w.subscribe("Security")
    w.ignore("event_id = 2034,3304")
    w.filter("event_id = 4656")
    w.start()
```

## event事件
> windows 事件内容封装

内置字段:
- xml
- exdata &emsp; EventData对象
- provider_name
- event_id
- task
- op_code
- create_time
- record_id
- process_id, pid
- thread_id
- channel
- computer
- version
- render_field_err
- message
- level_text
- task_text
- op_code_text
- keywords
- channel_text
- id_text
- publish_err
- bookmark
- subscribe

```lua
    local w = win.evtlog{}
    
    w.pipe(function(ev)
        print(ev.exdata.name) --获取eventdata中的name字段
        print(ev.event_id)
    end)
    
    w.start()
```
