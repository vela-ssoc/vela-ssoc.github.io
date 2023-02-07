# runtime 
> 运行时状态监控函数 以及一些全局函数使用说明
 
### 内置常量
* <em>runtime.OS      </em>&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;操作系统类型 windows , linux ...
* <em>runtime.ARCH    </em>&emsp;&emsp;&emsp;&emsp;&emsp;      操作系统架构 AMD64
* <em>runtime.windows </em>&emsp;&emsp;&emsp;&emsp;            是否为windows true,false
* <em>runtime.linux   </em>&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;是否为linux

### 获取任务名称
> v = runtime.code() 当前运行插件的任务名称

### Free memory
> runtime.free() 释放内存

### 设置最大内存信息
> runtime.max_memory(value)
> 只能在runtime任务中配置
```lua
    runtime.max_memory(1024 * 1024 * 500)
```

### 设置最大线程数
> runtime.max_thread(value)
> 只能在runtime任务中配置 
```lua
    runtime.max_thread(100)
```

### 设置最大CPU核心使用
> runtime.max_cpu(value)
> 只能在runtime任务中配置
```lua
    runtime.max_cpu(2)
```

### 获取agent进程占用内置方法
> alloc , total , sys = runtime.memory()
> alloc 分配内存大小
> total 运行开始到目前一共分配内存大小
> sys 系统开销
> 注意 这里 可能并不是很准 用来判断当前进程内存占用
```lua
    local  alloc , total , sys = runtime.memory()
    print(alloc)
    print(total)
    print(sys)
```

### 获取agent进程占用方法
> hm = runtime.p_memory() //hm 是lua.hashmap
> 采用获取PID的内存占用方式 类似用ps
```lua
    local hm = runtime.p_memory()
    print(hm.pid)   -- 进程PID
    print(hm.size)  -- size大小
    print(hm.rss)   -- rss
    print(hm.share) -- 共享内存mmap
```
### pprof开启 
> task = runtime.pprof(url) //url: https://127.0.0.1:9090
> 开启agent的调试 
```lua
    local task = runtime.pprof('http://127.0.0.1:9090')
```
