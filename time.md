# 时间函数
> 基础时间处理库 

内置方法:
- [time.sec()](#)
- [time.min()](#)
- [time.hour()](#)
- [time.day()](#)
- [time.week()](#)
- [time.month()](#)
- [time.year()](#)
- [time.sleep(v)](#)
- [time.now(v)](#时间)
- [time.at(delay , pipe)](#延时执行)
- [time.every(time , pipe)](#周期执行)
- [time.today()](#)
- [time.parse(layout , string)](#parse)
## example
```lua
    local sec = time.sec()
    print(sec) -- 59

    local min = time.min()
    print(min) -- 58

    local hour = time.hour()
    print(hour) -- 01 或 13

    local day = time.day()
    print(day)  -- 30

    local week = time.week()
    print(week) -- Sunday

    local month = time.month()
    print(month) -- 12

    local year = time.year()
    print(year)  -- 2022

    time.sleep(1 * 1000) -- sleep 1s

    local td = time.today() -- 今天
    print(td) -- 2022-02-22

    local td = time.today(1) -- 一天
    print(td) -- 2022-02-23

    local td = time.today(-1) -- 前一天时间
    print(td) -- 2022-02-21
```

## 时间
> [time](#time)  = time.now() <br />
> string = time.now(format)
format:
- mil  &emsp;格式: 2006-01-02 15 : 04:05.00
- sec  &emsp;格式: 2006-01-02 15 : 04:05
- min  &emsp;格式: 2006-01-02 15 : 04
- hour &emsp;格式: 2006-01-02.15
- day  &emsp;格式: 2006-01-02
- mon  &emsp;格式: 2006-01
- year &emsp;格式: 2006

```lua
    print(time.now("mil")) -- 2022-10-27 09:30:00.01
    print(time.now("sec")) -- 2022-10-27 10:10:10
    

    local tv = time.now()
    print(tv.tt_sec) -- 打印sec的时间戳 

```

## time
> time = time.now
> 封装time的时间格式

内置字段:

- sec
- min
- hour
- day
- week
- month
- year
- format
- tt_sec
- tt_milli
- tt_nano
- today

```lua
    local tv = time.now()
    print(tv.sec) -- 59
    print(tv.min) -- 58
    print(tv.hour) -- 01 或 13
    print(tv.day)  -- 30
    print(tv.week) -- Sunday
    print(tv.month) -- 12
    print(tv.year)  -- 2022
    print(tv.today)  -- 2022-10-30
    
    print(tv.tt_sec)
    print(tv.tt_milli)
    print(tv.tt_nano)
```

## parse
> [time](#time) = time.parse(layout , string) 
```
    local tv = time.parse("2006-01-02 15:04:05" , "2022-10-30 01:02:03")
    print(tv.sec)
```

## 延时执行
> time.at(delay , pipe) 
```lua
    time.at(1000 , function()  print(1) end , function() print(2) end)
```

## 周期执行
> v = time.every(time , pipe)
```lua
   time.every(1000 , function(now) -- 每秒中执行一次
        print(now)
    end)
```