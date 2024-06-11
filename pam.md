# vela-pam
SSOC linux pam(linux可插拔认证模块)插件

### 关于PAM
**PAM**(Linux Pluggable Authentication Modules) 代表可插入式身份验证模块，用于执行涉及身份验证、授权和某些修改（例如密码更改）的各种类型的任务。  
- 它允许系统管理员将身份验证任务的细节与应用程序本身分开。这使得策略不仅具有通用性，还意味着无需修改程序即可更新策略！
- PAM 用法的一个示例是控制对 shell/GUI 界面的登录尝试，以便只允许成功的身份验证和授权事件。您还可以使用 PAM 来控制谁可以使用su二进制文件切换身份或控制谁可以使用passwd实用程序更改密码。  
![alt text](images\pam01.png)
### 1. 基于pwquality模块的密码强度配置
##### 支持系统:
- CentOS 7.x
- CenOS 6.x  
##### 说明    
基于Linux的 pam_pwquality模块(或pam_cracklib模块)做系统密码复杂度策略的控制管理.

**具体配置如下**:  
`password requisite pam_pwquality.so enforce_for_root retry=3 minlen=8 ucredit=-1 dcredit=-1 lcredit=-1 ocredit=-1 difok=3 reject_username dictpath=/usr/share/cracklib/pw_dict`
🔑必须有大小写字母、数字、特殊字符, 长度至少8位，且不能包含用户名, 密码不能包含字典文件中的单词.  
-暂不支持自定义的密码策略.  
-支持自定义的密码字典(基于三方依赖功能管理和下发字典)    

##### 示例
```lua
-- 配置密码策略
vela.pam.set_pwquality("mydict")

-- 恢复原密码策略
vela.pam.recove_pwquality()

-- 获取当前密码策略
res=vela.pam.check_pwquality()
vela.Debug(res)

```
### 2. 自定义的PAM模块 
自编译.so进系统, 联动ssoc,在lua脚本层进行更加灵活的系统认证/改密等介入动作  
todo..