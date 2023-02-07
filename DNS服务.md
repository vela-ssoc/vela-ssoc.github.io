# dns
> dns 处理的服务框架

## 内置方法
- [vela.dns.server(cfg)](#dns服务);获取一个DNS服务


## dns服务 
> userdata = vela.dns.server(cfg) <br />
> cfg: 配置信息

配置字段:
- name: 服务名称
- bind: 监听套接字
- region: 地址位置库
- resolver: 二次请求后DNS地址


内置方法:
> 满足index 和 newindex 方法

- [userdata.to(lua.writer)](#)
- [userdata.pipe(px)](#)  &emsp;处理[context](#context)信息
- [userdata.start()](#)


```lua
    local v = vela.dns.server{
        name = "dnslog",
        bind = "udp://127.0.0.1:53",
        region= region.sdk(),
    }
    
    -- 等于号代表直接匹配 不取正则
    v["=www.abc.com."] = "127.0.0.1"
    v["=www.abc.com."] = function(ctx) ctx.say("127.0.0.1")  end

    v["*.aa.cc.com."] = "127.0.0.1"
    v["*.aa.cc.com."] = function(ctx) --[[todo]] end

    v["*.bb.a*.com."] = "127.0.0.1"

    v.to(kfk)

    v.pipe(function(ctx) 
        --todo        
    end)
```

## context
- 回调中间变量的使用
- 字段
- [ctx.say(ip) error](#)
- [ctx.A(ipv4) error](#)
- [ctx.AAAA(ipv6) error](#)
- [ctx.CNAME(host) error](#)
- [ctx.suffix(string) bool](#)
- [ctx.prefix(string) bool](#)
- [ctx.suffix_trim(string) value](#)
- [ctx.prefix_trim(string) value](#)
- [ctx.class string](#)
- [ctx.type string](#)
- [ctx.dns  string](#)
- [ctx.op_code int](#)
- [ctx.extra string](#)
- [ctx.ns string](#)
- [ctx.answer string](#)
- [cx.hit  bool](#)
- [cx.region string](#)
- [cx.addr string](#)
- [cx.port int](#)
- [cx.hdr_raw string](#)
- [cx.question_raw string](#)
```lua
    -- dns = xxx.abc.aa.com.
    ctx.pipe(function(ctx)
        print(ctx.hit)
        print(ctx.suffix("aa.com.")) -- true
        print(ctx.suffix_trim("abc.aa.com.")) -- abc
    end)
```