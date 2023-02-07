# fsnotify
>文件状态监控

## 内置方法 
- [vela.fsnotify(cfg)](#文件监控) &emsp;获取一个文件监控
- [事件信息](#事件信息)&emsp;事件信息监控

## 文件监控
> userdata = vela.fsnotify{name , path}
> name: 进程名  path: 监控路径

内置函数:

- [userdata.start()](#)
- [userdata.filter(cnd)](#)
- [userdata.pipe(lua.writer)](#)
- [userdata.on_err(lua.writer)](#)

```lua
  local ud = vela.fsnotify{
    name = "ff",
    path = "/var/log",
  }

  ud.filter("op eq WRITE" , "name re *passwd*") --cond 过滤

  ud.pipe(function(ev)
      print(ev.op)
      print(ev.name) -- filename
      print(ev.write) -- true
      print(ev.time) -- true

  end)

  ud.on_err(function(err)
  end)

  ud.start()

```

## 事件信息

- [event.op string](#)
- [event.time](#)
- [event.name](#)
- [event.create](#)
- [event.write](#)
- [event.rename](#)
- [event.chmod](#)

```lua
    local ud = vela.fsnotify{
        name = "ff",
        path = "/var/log",
    }

    ud.pipe(function(ev)
        print(ev.name)
        print(ev.create)
    end)
```