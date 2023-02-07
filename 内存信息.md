# memory
> 提供上层获取内存的方法和接口


## vela.memory
> summary = vela.memory <br />
> 返回内存汇总信息 支持windows linux

### 内置字段
- total
- free
- used
- available
- used_pct
- swap_total
- swap_free
- swap_in_pages
- swap_out_pages
- swap_used_pct

```lua
    local v = vela.memory
    print(total)
    print(free)
    print(used)
    print(available)
    print(used_pct)
    print(swap_total)
    print(swap_free)
    print(swap_in_pages)
    print(swap_out_pages)
    print(swap_used_pct)
```