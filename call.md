# 调用远程数据
>提供调用管理后台的威胁情报和可信数据的API

## 内置方法
- [vela.call(uri , value...)](#远程数据) &emsp;远程数据查询
- [vela.call{uri}](#客户端) &emsp;调用远程的客户端,参数为配置文件

## 特殊参数
- have &emsp;uri中存在have参数的时候返回bool
- count &emsp;uri中存在count参数的时候返回int
- cache &emsp;是否缓存 防止访问过快
- ttl &emsp;缓存时间 不配置cache不生效 , 默认30s
- kind &emsp;数据标签内容

## 数据类别
path类别:
- /ip/pass &emsp;IP白名单
- /ip/risk &emsp;IP威胁数据
- /dns/pass &emsp;DNS白名单
- /dns/risk &emsp;DNS威胁数据
- /file/risk &emsp;恶意文件

 
```lua
    local v = vela.call("/ip/risk?have&kind=virus" , "192.168.1.1" )
    print(v) --true or false

    local v = vela.call("/ip/risk?count&kind=virus" , "192.168.1.1" , "192.168.2.3" ) 
    print(v) --命中恶意的IP个数 这里是两个：可能是0 , 1 ,2
    

    local reply = vela.call("/ip/risk" , "192.168.1.1")
    print(reply) -- 返回所有的标签内容 看reply对象结构
 
```


## 远程数据
> v   = vela.call(uri , v...) 直接调用
```lua
    local v = vela.call("/ip/risk?have&kind=virus" , "192.168.1.1" )
    print(v) --true or false

    local v = vela.call("/ip/risk?count&kind=virus" , "192.168.1.1" , "192.168.2.3" )
    print(v) --命中恶意的IP个数 这里是两个：可能是0 , 1 ,2


    local reply = vela.call("/ip/risk" , "192.168.1.1")
    print(reply) -- 返回所有的标签内容 看reply对象结构

```



内部特殊逻辑:

uri: 


```lua
    local r = vela.call("ip/pass?have&kind=登录" , "192.168.1.1")

    local call = vela.call{ uri = "ip/pass?have&kind=登录" }
    call.cache(name , ttl)

    vela.call("192.168.1.1").match("ok = true").ok(function() end)
    
    local r = vela.call("/ip/pass?" , "192.168.1.1")
    
    r.have("登录")
```

## 客户端
> [reply](#reply)  = vela.call{cfg} 配置文件 <br />
> cfg: 配置字段{uri:"/ip/risk"}

内置方法:
- cache(name , size , ttl) &emsp;name:缓存名称 size:缓存键值个数  ttl:超时时间
- r.do(string...)

```lua
    local cli = vela.call{uri = "/ip/risk"}
    cli.cache("ip_hit_cache" , 300 , 30)
    
    local reply = cli.r"192.168.1.1"
    print(reply.kind)
```

## reply
> 封装服务器端的返回结果

内置方法: 
- ok
- status
- err
- path
- count
- query
- kind
- 其他满足index中的方法,可以直接用key去查看单个IP或者DNS信息是否有结果

```lua
    local r = vela.call("/ip/risk" , "192.168.10.9")
    print(r.kind) 
    print(r["192.168.10.9"])
```