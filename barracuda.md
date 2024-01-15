# vela-Barracuda
解析Barracuda电子邮件安全网关系统日志

## Lua API
### vela.barracuda.format()方法
**参数**  
raw 原始日志消息 string类型  
**返回值**  
barracuda的`LogEntry`对象, 字段如下所示

### LogEntry对象
| 字段 | 类型 | 描述 |
|---|---|---|
| `Raw` | string | 原始日志消息 |
| `SyslogTimestamp` | time.Time | syslog 时间戳(已转为LUA标准时间对象) |
| `SocProcessTimestamp` | time.Time | ssoc 处理时间戳(已转为LUA标准时间对象) |
| `SyslogHost` | string | syslog 主机 |
| `BarracudaProcess` | string | Barracuda 进程 |
| `Domain` | string | 域名 |
| `RemoteIP` | string | 远程 IP 地址 |
| `MsgID` | string | 消息 ID |
| `StartTimestamp` | time.Time | 开始时间戳(已转为LUA标准时间对象) |
| `EndTimestamp` | time.Time | 结束时间戳(已转为LUA标准时间对象) |
| `Service` | string | 服务类型 |
| `Info` | interface(对象的数据结构不固定) | 服务的详细信息 </br>⚠️根据`Service`的不同, 存在三种数据结构 </br>RECV </br>SCAN </br>SEND|

#### RECVServiceInfo对象
	当Service为RECV时, Info字段为RECVServiceInfo对象, 字段如下所示  
| 字段 | 类型 | 描述 |
|---|---|---|
| `From` | string | 发件人 |
| `Mailto` | string | 收件人 |
| `ActionId` | int | 操作 ID </br>⚠️实际为枚举类型</br>整数代表的具体含义请查看`RECVServiceActionId` |
| `Action` | string | 操作 |
| `ActionCN` | string | 操作中文名 |
| `ReasonId` | int | 原因 ID </br>⚠️实际为枚举类型</br>整数代表的具体含义请查看`RECVServiceReasonId` |
| `Reason` | string | 官方英文原因 |
| `ReasonCN` | string | 中文原因 |
| `ReasonExtra` | string | 原因额外信息 |

### SCANServiceInfo对象
	当Service为SCAN时, Info字段为SCANServiceInfo对象, 字段如下所示  
| 字段 | 类型 | 描述 |
|---|---|---|
| `Encrypted` | string | 加密信息 |
| `From` | string | 发件人 |
| `Mailto` | string | 收件人 |
| `Score` | int | 评分 |
| `ActionId` | int | 操作 ID </br>⚠️实际为枚举类型</br>整数代表的具体含义请查看`SCANServiceActionId` |
| `Action` | string | 操作 |
| `ActionCN` | string | 操作中文名 |
| `ReasonId` | int | 原因 ID </br>⚠️实际为枚举类型</br>整数代表的具体含义请查看`SCANServiceReasonId` |
| `Reason` | string | 官方英文原因 |
| `ReasonCN` | string | 中文原因 |
| `Subject` | string | 主题 |
| `SubjectDecode` | string | 解码后的主题 |

### SENDServiceInfo对象
	当Service为SEND时, Info字段为SENDServiceInfo对象, 字段如下所示  
| 字段 | 类型 | 描述 |
|---|---|---|
| `Encrypted` | string | 加密信息 |
| `ActionId` | int | 操作 ID </br>⚠️实际为枚举类型</br>整数代表的具体含义请查看`SENDServiceActionId` |
| `Action` | string | 操作 |
| `ActionCN` | string | 操作中文名 |
| `QueueId` | string | 队列 ID |
| `Response` | string | 响应 |

### RECVServiceActionId
| ReasonId (int) |内置变量名 |Action (string) |ActionCN (string)  |
|---|---|---|---|
| 0 |RECVServiceActionAllowedMessage |"Allowed Message", |"允许的邮件",  |
| 1 |RECVServiceActionAbortedMessage |"Aborted Message", |"中止的邮件",  |
| 2 |RECVServiceActionBlockedMessage |"Blocked Message", |"阻止的邮件",  |
| 3 |RECVServiceActionQuarantinedMessage |"Quarantined Message", |"隔离的邮件",  |
| 4 |RECVServiceActionTaggedMessage |"Tagged Message", |"被标记的邮件",  |
| 5 |RECVServiceActionDeferredMessage |"Deferred Message", |"延迟的邮件",  |
| 6 |RECVServiceActionPerUserQuarantinedMessage |"Per-User Quarantined Message", |"每个用户隔离的邮件",  |
| 7 |RECVServiceActionAllowListedMessage |"Allow Listed Message", |"允许列出的邮件",  |
| 8 |RECVServiceActionEncryptedMessage |"Encrypted Message", |"加密的邮件",  |
| 9 |RECVServiceActionRedirectedMessage |"Redirected Message", |"重定向",  |
| 10 |RECVServiceActionAttachmentsStubbed |"Attachments Stubbed", |"附件已删除",  |
| 11 |RECVServiceActionUnkown |"unknown RECVActionId", |"未知的RECVActionId",  |



### RECVServiceReasonId
| ReasonId (int)  | 内置变量名 | Reason (string) | ReasonCN (string) |
|---|---|---|---|
| 0 | RECVServiceMissing0 | unknown RECVServiceReasonID 0 | 未知 RECVServiceReasonID 0 |
| 1 | RECVServiceVirus | Virus | 病毒 |
| 2 | RECVServiceBannedAttachment | Banned Attachment | 禁止的附件 |
| 3 | RECVServiceRBLMatch | RBL Match | RBL 匹配 |
| 4 | RECVServiceRateControl | Rate Control | 速率控制 |
| 5 | RECVServiceTooManyMessageInSession | Too Many Message In Session | 会话中的消息过多 |
| 6 | RECVServiceTimeoutExceeded | Timeout Exceeded | 超时 |
| 7 | RECVServiceNoSuchDomain | No Such Domain | 没有这样的域 |
| 8 | RECVServiceNoSuchUser | No Such User | 没有这样的用户 |
| 9 | RECVServiceSubjectFilterMatch | Subject Filter Match | 主题过滤器匹配 |
| 10 | RECVServiceMissing10 | unknown RECVServiceReasonID 10 | 未知 RECVServiceReasonID 10 |
| 11 | RECVServiceClientIP | Client IP | 客户端 IP |
| 12 | RECVServiceRecipientAddress | Recipient Address | 收件人地址 |
| 13 | RECVServiceNoValidRecipients | No Valid Recipients | 没有有效的收件人 |
| 14 | RECVServiceDomainNotFound | Domain Not Found | 找不到域 |
| 15 | RECVServiceSenderAddress | Sender Address | 发件人地址 |
| 16 | RECVServiceMissing16 | unknown RECVServiceReasonID 16 | 未知 RECVServiceReasonID 16 |
| 17 | RECVServiceNeedFullyQualifiedRecipient | Need Fully Qualified Recipient | 需要完全限定的收件人 |
| 18 | RECVServiceNeedFullyQualifiedSender | Need Fully Qualified Sender | 需要完全限定的发件人 |
| 19 | RECVServiceUnsupportedCommand | Unsupported Command | 不受支持的命令 |
| 20 | RECVServiceMAILFROMSyntaxError | MAIL FROM Syntax Error | MAIL FROM 语法错误 |
| 21 | RECVServiceBadAddressSyntax | Bad Address Syntax | 错误的地址语法 |
| 22 | RECVServiceRCPTTOSyntaxError | RCPT TO Syntax Error | RCPT TO 语法错误 |
| 23 | RECVServiceSendEHLOHELOFirst | Send EHLO/HELO First | 先发送 EHLO/HELO |
| 24 | RECVServiceNeedMAILCommand | Need MAIL Command | 需要 MAIL 命令 |
| 25 | RECVServiceNestedMAILCommand | Nested MAIL Command | 嵌套的 MAIL 命令 |
| 26 | RECVServiceMissing26 | unknown RECVServiceReasonID 26 | 未知 RECVServiceReasonID 26 |
| 27 | RECVServiceEHLOHELOSyntaxError | EHLO/HELO Syntax Error | EHLO/HELO 语法错误 |
| 28 | RECVServiceMissing28 | unknown RECVServiceReasonID 28 | 未知 RECVServiceReasonID 28 |
| 29 | RECVServiceMailProtocolViolation | Mail Protocol Violation | 邮件协议违规 |
| 30 | RECVServiceScore | Score | 分数 |
| 31 | RECVServiceHeaderFilterMatch | Header Filter Match | 标题过滤器匹配 |
| 32 | RECVServiceMissing32 | unknown RECVServiceReasonID 32 | 未知 RECVServiceReasonID 32 |
| 33 | RECVServiceMissing33 | unknown RECVServiceReasonID 33 | 未知 RECVServiceReasonID 33 |
| 34 | RECVServiceSenderBlockAccept | Sender Block/Accept | 发件人阻止/接受 |
| 35 | RECVServiceRecipientBlockAccept | Recipient Block/Accept | 收件人阻止/接受 |
| 36 | RECVServiceBodyFilterMatch | Body Filter Match | 正文过滤器匹配 |
| 37 | RECVServiceMessageSizeBypass | Message Size Bypass | 消息大小绕过 |
| 38 | RECVServiceIntentionAnalysisMatch | Intention Analysis Match | 意图分析匹配 |
| 39 | RECVServiceSPFCallerID | SPF/Caller-ID | SPF/呼叫者 ID |
| 40 | RECVServiceClientHostRejected | Client Host Rejected | 客户端主机被拒绝 |
| 41 | RECVServiceAuthenticationNotEnabled | Authentication Not Enabled | 未启用身份验证 |
| 42 | RECVServiceMissing42 | unknown RECVServiceReasonID 42 | 未知 RECVServiceReasonID 42 |
| 43 | RECVServiceMissing43 | unknown RECVServiceReasonID 43 | 未知 RECVServiceReasonID 43 |
| 44 | RECVServiceAllowedMessageSizeExceeded | Allowed Message Size Exceeded | 允许的消息大小超限 |
| 45 | RECVServiceTooManyRecipients | Too Many Recipients | 收件人过多 |
| 46 | RECVServiceNeedRCPTCommand | Need RCPT Command | 需要 RCPT 命令 |
| 47 | RECVServiceDATASyntaxError | DATA Syntax Error | DATA 语法错误 |
| 48 | RECVServiceInternalError | Internal Error | 内部错误 |
| 49 | RECVServiceMissing49 | unknown RECVServiceReasonID 49 | 未知 RECVServiceReasonID 49 |
| 50 | RECVServiceTooManyHops | Too Many Hops | 跳数过多 |
| 51 | RECVServiceMailProtocolError | Mail Protocol Error | 邮件协议错误 |
| 52 | RECVServiceMissing52 | unknown RECVServiceReasonID 52 | 未知 RECVServiceReasonID 52 |
| 53 | RECVServiceMissing53 | unknown RECVServiceReasonID 53 | 未知 RECVServiceReasonID 53 |
| 54 | RECVServiceMissing54 | unknown RECVServiceReasonID 54 | 未知 RECVServiceReasonID 54 |
| 55 | RECVServiceInvalidParameterSyntax | Invalid Parameter Syntax | 无效的参数语法 |
| 56 | RECVServiceSTARTTLSSyntaxError | STARTTLS Syntax Error | STARTTLS 语法错误 |
| 57 | RECVServiceTLSAlreadyActive | TLS Already Active | TLS 已激活 |
| 58 | RECVServiceTooManyErrors | Too Many Errors | 错误过多 |
| 59 | RECVServiceNeedSTARTTLSFirst | Need STARTTLS First | 需要先进行 STARTTLS |
| 60 | RECVServiceSpamFingerprintFound | Spam Fingerprint Found | 发现垃圾邮件指纹 |
| 61 | RECVServiceBarracudaReputationAllowList | Barracuda Reputation Allow List | Barracuda 声誉允许列表 |
| 62 | RECVServiceBarracudaReputationBlockList | Barracuda Reputation Block List | Barracuda 声誉阻止列表 |
| 63 | RECVServiceDomainKeys | DomainKeys | 域密钥 |
| 64 | RECVServiceRecipientVerificationUnavailable | Recipient Verification Unavailable | 收件人验证不可用 |
| 65 | RECVServiceRealtimeIntent | Realtime Intent | 实时意图 |
| 66 | RECVServiceClientReverseDNS | Client Reverse DNS | 客户端反向 DNS |
| 67 | RECVServiceEmailRegistry | Email Registry | 电子邮件注册表 |
| 68 | RECVServiceInvalidBounce | Invalid Bounce | 无效的退信 |
| 69 | RECVServiceIntentAdult | Intent - Adult | 意图 - 成人 |
| 70 | RECVServiceIntentPolitical | Intent - Political | 意图 - 政治 |
| 71 | RECVServiceMultiLevelIntent | Multi-Level Intent | 多级意图 |
| 72 | RECVServiceAttachmentLimitExceeded | Attachment Limit Exceeded | 附件限制超限 |
| 73 | RECVServiceSystemBusy | System Busy | 系统繁忙 |
| 74 | RECVServiceBRTSIntent | BRTS Intent | BRTS 意图 |
| 75 | RECVServicePerDomainRecipient | Per Domain Recipient | 按域收件人 |
| 76 | RECVServicePerDomainSender | Per Domain Sender | 按域发件人 |
| 77 | RECVServicePerDomainClientIP | Per Domain Client IP | 按域客户端 IP |
| 78 | RECVServiceSenderSpoofed | Sender Spoofed | 发件人欺骗 |
| 79 | RECVServiceAttachmentContent | Attachment Content | 附件内容 |
| 80 | RECVServiceOutlookAddIn | Outlook Add-in | Outlook 加载项 |
| 81 | RECVServiceMissing81 | unknown RECVServiceReasonID 81 | 未知 RECVServiceReasonID 81 |
| 82 | RECVServiceBarracudaIPDomainReputation | Barracuda IP/Domain Reputation | Barracuda IP/域声誉 |
| 83 | RECVServiceAuthenticationFailure | Authentication Failure | 身份验证失败 |
| 84 | RECVServiceMissing84 | unknown RECVServiceReasonID 84 | 未知 RECVServiceReasonID 84 |
| 85 | RECVServiceAttachmentSize | Attachment Size | 附件大小 |
| 86 | RECVServiceVirusDetectedByExtendedMalwareProtection | Virus detected by Extended Malware Protection ** | 扩展恶意软件防护检测到病毒 ** |
| 87 | RECVServiceExtendedMalwareProtectionEngineIsBusy | Extended Malware Protection engine is busy ** | 扩展恶意软件防护引擎繁忙 ** |
| 88 | RECVServiceMessageCategorizedForEmailCategory | A message was categorized for Email Category** | 一条消息被归类为电子邮件类别** |
| 89 | RECVServiceMacroBlocked | Macro Blocked* | 宏被阻止* |
| 90 | RECVServiceReasonUnkown | unknown RECVServiceReasonID | 未知 RECVServiceReasonID |



### SCANServiceActionId
和上面的RECVServiceActionId结构相同

### SCANServiceReasonId
和上面的RECVServiceReasonId结构相同


### SENDServiceActionId
| ActionId (int)  | 内置变量名 | Action (string) | ActionCN (string) |
|---|---|---|---|
| 0 | SENDServiceMissing0  | "unknown SENDServiceActionID 0",  | "未知 SENDServiceActionID 0",  |
| 1 | SENDServiceActionAllowedMessage  | "Delivered Message",  | "投递",  |
| 2 | SENDServiceActionRejectedMessage  | "Rejected Message",  | "拒收",  |
| 3 | SENDServiceActionDeferredMessage  | "Deferred Message",  | "延期",  |
| 4 | SENDServiceActionExpiredMessage  | "Expired Message",  | "过期",  |
| 5 | SENDServiceActionUnkown | "unknown SENDServiceActionId", | "未知 SENDServiceActionID", |


## 示例
```lua
d=vela.barracuda.fromat("Jan 11 06:58:03 10.10.10.111  inbound/pass1: unknown[114.114.114.114] 1704927410-1920756-20844-227386-1 1704927410 1704927410 RECV - - 2 83 535 Error: authentication failed [zhangshan@163.com]")
vela.Debug("%v",d)
```
```lua
vela.barracuda.fromat("Jan 11 06:58:03 10.10.10.111  inbound/pass1: unknown[114.114.114.114] 1704927410-1920756-20844-227386-1 1704927410 1704927410 RECV - - 2 83 535 Error: authentication failed [zhangshan@163.com]")
```
## 参考
https://campus.barracuda.com/product/emailsecuritygateway/doc/12193950/syslog-and-the-barracuda-email-security-gateway/?lang=2AJYS