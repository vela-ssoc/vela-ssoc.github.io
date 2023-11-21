# third
> 主要用来加载三方依赖

```lua
    local info , err = vela.third("ip2region.xdb")
    if err ~= nil then
        error(err) 
        return
    end

    print(info.file)
    print(info.size)
    print(info.hash)
    print(info.mtime)
    print(info.ctime)
    print(info.expire)
    print(info.ext)
    
    local r = vela.region.new{
        name = 'region',
        xdb  = info.file,
        method = "index",
    }

    r.start()
    r.default()
```