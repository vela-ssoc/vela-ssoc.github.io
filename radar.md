# vela-radar

Active scanning of network assets  
利用ssoc agent实现网络资产主动扫描

## 更新日志

2023-11-10   v0.1.0   初始测试版本  
2023-11-13   v0.1.1   资产扫描功能优化, 加入web指纹探测功能  
2023-11-15   v0.1.2   优化扫描数据的处理, 对接后端上报接口的数据结构  
2023-11-17   v0.2.0   实现远程调用的内部API, 实时进度显示  
2023-11-20   v0.2.1   实现禁止扫描的白名单ip设置  
2023-11-29   v0.3.0   实现web站点截图功能  
2023-11-30   v0.3.1   实现通过内部tunnel上报, web站点截图功能优化  
2023-12-20   v0.3.2   web指纹加载逻辑升级, 支持自定义三方指纹库动态加载和更新  
2023-12-25   v0.4.0   支持设置扫描时间段(定时暂停与开始, beta)  
2023-12-25   v0.4.1   优化任务计数逻辑, 提升扫描进度百分比的精确度  
2023-12-25   v0.4.2   修复扫描只一个大段网段的某一个端口时, 导致的任务异常退出问题  
2023-12-25   v0.4.3   修复FingerPrint子任务调度相关问题  
2024-01-02   v0.4.4   内部HTTP API支持扫描排除时间段设置  
2024-01-03   v0.4.5   修复扫描速率参数设置问题  
2024-01-05   v0.4.5   优化扫描参数传入  
2024-01-06   v0.4.6   继续完善LUA SDK 和HTTP API  
2024-01-08   v0.4.7   放宽传输层最大发包速率上限  
2024-01-08   v0.4.7   修复内置TOP5000端口号部分错误  
2024-01-09   v0.4.8   修复对任务监视器的通道关闭处理相关错误  
2024-01-09   v0.4.8   完善管道操作数据结构和相关文档  
2024-01-10   v0.4.8   修复站点截图模块一些边界条件问题
2024-01-16   v0.4.9   内部HTTP API升级改造
2024-01-16   v0.4.9   内部HTTP API支持展示上一次历史任务信息
2024-01-17   v0.4.9   内部HTTP API任务详细信息数据结构优化
2024-01-17   v0.4.9   扫描任务状态相关功能优化
2024-01-17   v0.4.9   支持展示和动态计算扫描开始时间,扫描结束时间和扫描耗时
2024-01-17   v0.4.9   实时上报扫描结果时, 加入了扫描任务ID, 方便后期查询         
2024-01-17   v0.4.9   整理完善文档       


## 功能

1. 主机存活扫描(icmp)
2. 主机端口开放扫描(支持syn和tcp全连接方式)
3. 主机端口服务识别
4. HTTP指纹识别
5. 数据上报(基于lua接口的管道)
6. 实现内部API远程调用的接口(创建扫描与获取实时扫描状态)
7. 支持CIDR和地址范围(192.168.1.1-100)格式的目标资产输入
8. 支持禁止扫描的白名单ip设置, 支持ip,CIDR,地址范围以及用","分割组合输入
9. web站点截图功能, 并上传至minio图床
10. 支持三方指纹库动态加载和更新(基于三方依赖)
11. 设置扫描排除时间段(在指定时间段内暂停)

## todo

1. 继续对可能遇到的一些边界条件时稳定性问题进行优化
2. syn扫描时实时显示进度
3. 常见UDP协议扫描
4. 优化扫描速度
5. web HTTP指纹识别优化
6. web HTTP指纹数据库自定义(中心端)
7. TCP 指纹模块优化(支持更多协议)
8. TCP 指纹模块加入安全相关指纹(FRP,CS listener,msf listener....)
9. 优化扫描结果的数据结构
10. 分布式集群扫描,智能分配扫描任务
11. 处理模块实时返回数据的问题
12. ……

## Lua API
### 主服务 radar
**初始化方法 vela.radar{}**

| 参数名 | 数据类型 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| name | string | ✅ | 扫描主服务名称 |
| finger | finger配置对象 |  | 指纹识别子服务配置 |
| minio | minio配置对象 |  | minio图床配置</br>不填则站点截图无法上报 |
| reportDoer | string |  | 内置report tunnel配置 |
| reportUri | string |  | 内置report http uri配置 |
| debug   | bool |  | 是否开启debug模式(console输出debug日志) |

**返回值** radar对象
### finger配置对象
| 参数名 | 数据类型 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| timeout | string | | 超时时间 单位: ms |
| ~~udp~~ | bool |  | 是否开启udp指纹识别 (暂未开放此功能) |
| ~~fast~~ | bool |  | 快速探测模式(暂未开放此功能) |

### minio配置对象
| 参数名 | 数据类型 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| accessKey | string |  | accessKey |
| secretKey | string |  | secretKey |
| endpoint | string |  | endpoint URI |
| useSSL | bool |  | 是否启用SSL |


### (rander).start()方法
启动服务  
**传入参数** 无  
**返回值** 无  
### (rander).define()方法
启动内部API调用功能  
**传入参数** 无  
**返回值** 无  

### (rander).pipe()方法
结果输出管道, 可以通过该管道实时获取扫描结果   
**传入参数**  
传入一个或者多个function来链式调用
例如
```lua
function(host)
  es.send(host)
end
```  
传入参数可以任意取名, 传入的数据结构固定为host对象, 参数有且仅有一个  
####  host对象
| 参数名 | 数据类型 | 说明 |
| --- | --- | --- |
| ip         | string       | IP                      |
| port       | int          | 端口                      |
| tls        | bool         |                         |
| location   | string       | 地理位置                    |
| host       | string       | 主机域名 (扫内网的时候一般为空或者填 IP) |
| protocol   | string       | 应用层协议                   |
| transport  | string       | 传输层协议 tcp/udp           |
| version    | string       | 应用(或者协议)版本              |
| comment    | string       | 备注信息                    |
| component  | []string     | 组件标签                    |
| http_info  | *HttpInfo    | web服务的指纹以及相关信息          |
| banner     | []byte(json) | tcp服务的banner信息          |
| task_id    | string       | 任务Id                    |  

⚠️ report实时上报扫描结果时,数据结构也是这个  


上表中的http_info字段是一个对象, HttpInfo的数据结构如下表
#### HttpInfo对象
| 字段            | 类型       | JSON 字段           | BSON 字段           | 描述                                |
|-------------|--------|-----------------|-----------------|---------------------------------|
| StatusCode    | int      | "status_code"     | "status_code"     | 响应状态码                             |
| ContentLength | int      | "content_length"  | "content_length"  | 响应包大小                             |
| URL           | string   | "url"             | "url"             | HTTP URL                          |
| Location      | string   | "location"        | "location"        | 301/302 重定向地址                     |
| Title         | string   | "title"           | "title"           | 网站 title                          |
| Server        | string   | "server"          | "server"          | HTTP Header 中的 server 字段          |
| Body          | string   | "body"            | "body"            | HTTP body                         |
| Header        | string   | "header"          | "header"          | HTTP Header 的数据                   |
| FaviconMH3    | string   | "favicon_mh3"     | "favicon_mh3"     | favicon 的 mh3, mh3 一种比 md5 更快的算法  |
| FaviconMD5    | string   | "favicon_md5"     | "favicon_md5"     | favicon 的 md5                     |
| ScreenshotURL | string   | "screenshot_url"  | "screenshot_url"  | 网站截图的 URL                         |
| Fingerprints  | []string | "fingerprints"    | "fingerprints"    | 识别到的 web 指纹                       |
| TLSCommonName | string   | "tls_common_name" | "tls_common_name" | TLS 证书的 CommonName                |
| TLSDNSNames   | []string | "tls_dns_names"   | "tls_dns_names"   | TLS 证书的 DNSName                   |

**返回值** 无  

### 扫描任务相关方法
| 方法名 | 传入参数 | 返回 | 是否必填 | 说明 |
| --- | --- | --- | --- | --- |
| `(radar).target(..)` | string | 自身task对象(链式调用) | ✅ | 扫描目标资产 支持IP/CIDR/IP范围</br>⚠️target方法需在链式调用的首步调用 |
| `(task).location(..)` | string | 自身task对象(链式调用) | ✅ | 网络位置 必填 以区分不同网络位置出现相同IP的情况 |
| `(task).name(..)` | string | 自身task对象(链式调用) | ✅ | 任务名称 |
| `(task).mode(..)`   | string | 自身task对象(链式调用) | | 模式 "tcp"(默认)/"syn" (syn扫描依赖libcap) |
| `(task).port(..)` | string | 自身task对象(链式调用) |  | 端口  默认top1000 |
| `(task).exclude(..)` | string | 自身task对象(链式调用) |  | 排除扫描ip 支持IP/CIDR/IP范围 并可以以","组合 |
| `(task).rate(..)`   | int | 自身task对象(链式调用) |  | 传输层协议基础发包速率 默认500 |
| `(task).timeout(..)` | int | 自身task对象(链式调用) |  | 超时时间(ms) 默认800 |
| `(task).httpx(..)`   | bool | 自身task对象(链式调用) |  | 是否开启http指纹探测 |
| `(task).fingerDB(..)`   | bool | 自身task对象(链式调用) |  | 指定http指纹库三方依赖(不指定则使用内置默认指纹库) |
| `(task).screenshot(..)`   | bool | 自身task对象(链式调用) |  | 是否开启站点截图功能 |
| `(task).ping(..)`   | bool | 自身task对象(链式调用) |  | 是否开启ping存活探测 |
| `(task).debug(..)`   | bool | 自身task对象(链式调用) |  | 是否开启debug模式(console输出debug日志) |
| `(task).pool(..)`   | int scan,int finger,int ping | 自身task对象(链式调用) |  | ping探测并发数率 默认10</br>scan并发数率 默认10</br>指纹识别并发数率 默认50 |
| `(task).excludeTimeRange(..)`   | string | 自身task对象(链式调用) |  | 扫描排除时间段 示例</br>"daily,9:00,17:00" 排除每天的早9至晚5</br>"everyWorKDay,9:00,17:00"排除每个工作日的早9至晚5</br>"OpeningtimeBroad,,"排除开盘时间(宽泛),所有工作日以及周六的0点至5点 |
| `(task).run(..)` | null | null | ✅ | 开启扫描 </br>⚠️run方法需在链式调用的最后一步调用 |


### (rander).chrome()方法
web站点截图服务相关配置  
可以全部不填 
```lua
(radar).chrome({})
```
**参数** lua table    

| 参数名 | 数据类型 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| save | string |  | 是否本地保存的站点截图图片 |
| thread | string |  | 同时启动的tab个数 |
| timeout | string |  | 站点打开超时时间(单位:秒) |
| resultDir | string |  | 本地保存的站点截图图片文件夹</br>⚠️save选项开启时才起作用 |
| debug | bool |  | 是否开启debug日志打印 |  
**返回值** 无   



## 内部HTTP API

### **GET** `/api/v1/arr/agent/radar/status`
获取当前扫描服务状态  
#### 请求
GET 无需参数  
#### 响应
radar扫描主服务信息以及当前运行任务信息(若存在)  
**响应包数据结构(JSON):**  

|·字段|数据类型|说明|
|----|----|----|
|`name`|string|radar扫描主服务名(lua脚本里初始化服务时定义的)|
|`status`|string|radar扫描服务状态  idle 空闲  working正在扫描|
|`thread`|string|radar扫描主服务配置默认线程数|
|`udp`|bool|radar扫描主服务配置默认UDP是否开启(功能暂未开放)|
|`fastmode`|string|radar扫描主服务配置默认快速扫描模式是否开启(功能暂未开放)|
|`defaultTimeout`|int|radar扫描主服务配置指纹识别默认超时时间(单位:毫秒)|
|`task_all_num`|int|所有的扫描子任务项数|
|`task_success_num`|int|已完成的扫描子任务项数|
|`task_process`|float|扫描进度%0.2f (0-100.00)|
|`pause_signal`|int|暂停信号 1是机器自动暂停 2是用户人工手动暂停 0是正常运行|
|`task`|**task对象**|没有任务在运行时, 返回空字符串`""`</br>有任务在运行时, 返回当前运行任务的详细信息|
|`last_task`|**task对象**|没有上次扫描任务时, 时返回空字符串`""`</br>有上次扫描任务时, 返回上次扫描任务的详细信息|


**task对象**

|字段|数据类型|说明|
|----|----|----|
|(task).`name`|sting|任务名称|
|(task).`id`|sting|创建任务唯一的uuid|
|(task).`status`|int|**Task_Status枚举类型**|
|(task).`debug`|bool|tsak是否处于debug状态|
|(task).`start_time`|string|任务开始时间</br>`RFC3339Nano`标准格式 </br>示例"2006-01-02T15:04:05.999999999Z07:00"|
|(task).`end_time`|string|任务结束时间</br>`RFC3339Nano`标准格式 </br>示例"2006-01-02T15:04:05.999999999Z07:00"|
|(task).`task_all_num`|int|所有的扫描子任务项数|
|(task).`task_success_num`|int|已完成的扫描子任务项数|
|(task).`task_process`|string|扫描进度%0.2f (0-100.00)|
|(task).`timeuse_second`|float|任务耗时 单位:秒|
|(task).`timeuse_msg`|string|任务耗时 时:分:秒|
|(task).`option`|**option对象**|⚠️返回的原生Marshal()方法的bytes, 结构体定义如下所示|

**Task_Status枚举类型**

|值(int)|名称|说明|
|----|----|----|
|0|Task_Status_Init|初始化状态|
|1|Task_Status_Running|正在扫描|
|2|Task_Status_Success|扫描完成|
|3|Task_Status_Paused_By_Program|程序暂停(如当前出在禁扫时间)|
|4|Task_Status_Paused_Artificial|人工暂停|q
|5|Task_Status_Error|扫描出错|
|6|Task_Status_Unknown|未知状态|


**option对象**
```go
// 字段信息备注查看下面"下发扫描任务"表格
type Option struct {
	Location         string         `json:"location"`
	Mode             string         `json:"mode"`
	Target           string         `json:"target"`
	ExcludedTarget   string         `json:"exclude_target"`
	Port             string         `json:"port"`
	Rate             int            `json:"rate"`
	Timeout          int            `json:"timeout"`
	Httpx            bool           `json:"httpx"`
	Ping             bool           `json:"ping"`
	FingerDB         string         `json:"finger_db"`
	Screenshot       bool           `json:"screenshot"`
	Pool             Pool           `json:"pool"`
	ExcludeTimeRange util.TimeRange `json:"exclude_time_range"`
	MinioCfg         util.MinioCfg  `json:"-"`
}

type Pool struct {
	Ping   int `json:"ping"`
	Scan   int `json:"scan"`
	Finger int `json:"finger"`
}

type TimeRange struct {
	Daily string `json:"daily"`
	Begin string `json:"begin"`
	End   string `json:"end"`
}

type MinioCfg struct {
	AccessKey string
	SecretKey string
	Name      string
	Endpoint  string
	UseSSL    bool
}
```
#### 数据示例
```json
{
  "name": "radar",
  "status": "idle",
  "thread": 25,
  "udp": false,
  "fastmode": false,
  "default_timeout": 500,
  "task": "",
  "last_task": {
    "name": "测试扫描任务",
    "id": "dd57a313-ba8c-46c2-903f-77c6d067179d",
    "debug": false,
    "status": 2,
    "start_time": "2024-01-17 13:32:58",
    "end_time": "2024-01-17 13:35:13",
    "timeuse_second": 135.28085949,
    "timeuse_msg": "0小时02分钟15秒",
    "task_all_num": 257280,
    "task_success_num": 257280,
    "task_process": "100.00",
    "option": {
      "location": "测试本地网",
      "mode": "pn",
      "target": "192.168.1.1/24",
      "exclude_target": "",
      "port": "top1000",
      "rate": 2000,
      "timeout": 800,
      "httpx": true,
      "ping": false,
      "fingerDB": "",
      "screenshot": false,
      "pool": {
        "ping": 10,
        "scan": 10,
        "finger": 50
      },
      "exclude_time_range": {
        "daily": "",
        "begin": "",
        "end": ""
      }
    }
  }
}
```

### **POST** `/api/v1/arr/agent/radar/runscan`
下发和运行扫描任务 ( 如果已有扫描任务正在进行则无法运行 )  
POST数据包体类型(content-type) : **JSON**  
#### 参数  :
| 参数名 | 数据类型 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| `target` | string | ✅ | 扫描目标资产 支持IP/CIDR/IP范围 |
| `location` | string | ✅ | 网络位置 必填 以区分不同网络位置出现相同IP的情况 |
| `name` | string | ✅ | 任务名称 |
| `mode`   | string |  | 模式 "tcp"(默认)/"syn" (syn扫描依赖libcap) |
| `port` | string |  | 端口  默认top1000 |
| `rate`   | int |  | 传输层协议基础发包速率 默认500 |
| `timeout` | int |  | 超时时间(ms) 默认800 |
| `httpx`   | bool |  | 是否开启http指纹探测 |
| `fingerDB`   | bool |  | 指定http指纹库三方依赖(不指定则使用内置默认指纹库) |
| `screenshot`   | bool |  | 是否开启站点截图功能|
| `ping`   | bool |  | 是否开启ping存活探测 |
| `pool_ping`   | int |  | ping探测并发数率 默认10 |
| `pool_scan`   | int |  | scan并发数率 默认10 |
| `pool_finger`  | int |  | 指纹识别并发数率 默认50 |
| `excludeTimeRange`   | string |  | 扫描排除时间段 示例</br>"daily,9:00,17:00" 排除每天的早9至晚5</br>"everyWorKDay,9:00,17:00"排除每个工作日的早9至晚5</br>"OpeningtimeBroad,,"排除开盘时间(宽泛),所有工作日以及周六的0点至5点 ||  |  |  |  |

**响应**:   
成功时, 返回本次提交扫描的任务信息(task对象,上同,JSON格式) status_code为 200  
已经有任务在运行时或者参数设置错误时, 返回status_code为 500, 内容为错误信息  

#### 数据示例
请求  
```json
{
    "target":"192.168.1.1/24",
    "location":"测试本地网",
    "name":"测试扫描任务",
    "port":"top1000",
    "httpx":true,
    "rate":2000,
    "screenshot":true
}
```
响应  
```json
{
  "name": "测试扫描任务",
  "id": "dd46f2ca-461e-4112-9e64-e931fcde13a1",
  "debug": false,
  "status": 0,
  "start_time": "2024-01-17 14:01:24",
  "end_time": "",
  "timeuse_second": 0.000057763,
  "timeuse_msg": "0小时00分钟00秒",
  "task_all_num": 0,
  "task_success_num": 0,
  "task_process": "NaN",
  "option": {
    "location": "测试本地网",
    "mode": "pn",
    "target": "192.168.1.1/24",
    "exclude_target": "",
    "port": "top1000",
    "rate": 2000,
    "timeout": 800,
    "httpx": true,
    "ping": false,
    "fingerDB": "",
    "screenshot": false,
    "pool": {
      "ping": 10,
      "scan": 10,
      "finger": 50
    },
    "exclude_time_range": {
      "daily": "",
      "begin": "",
      "end": ""
    }
  }
}
```

## 示例(lua)

```lua
local rr = vela.radar{
  name = "radar",
  finger = {timeout = 500 , udp = false , fast = false},
  minio = {accessKey="xxx" , secretKey="xxx" , endpoint="xxx" , useSSL=false}
}

local es = vela.elastic.default("vela-radar-%s" , "$day")
rr.pipe(function(host)
  es.send(host)
end)

-- 启动服务
rr.start()

-- 启动内部API调用功能
rr.define()

-- 开启扫描任务
rr.task("192.168.1.1/24").port("top1000").httpx(true).exclude("192.168.1.100,192.168.1.10-20").run()
-- 关闭主机ping存活探测 .ping(false)
-- web快照截图 .screenshot(true)
-- 指定指纹库  .fingerDB("radar-http-finger.json")
-- 指定扫描时间段  .excludeTimeRange("daily","15:00","15:02")
```

## ⚠️注意

1. 在做外网探测时, 可能会因为syn的包过多, 导致网络无法链接
2. 使用syn做扫描时, 暂时无法实时计算准确进度
