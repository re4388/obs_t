## [SSO 介绍](https://javaguide.cn/system-design/security/sso-intro.html#sso-%E4%BB%8B%E7%BB%8D)

### [什么是 SSO？](https://javaguide.cn/system-design/security/sso-intro.html#%E4%BB%80%E4%B9%88%E6%98%AF-sso)

SSO 英文全称 Single Sign On，单点登录。SSO 是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。

例如你登录网易账号中心（[https://reg.163.com/](https://reg.163.com/) ）之后访问以下站点都是登录状态。

- 网易直播 [https://v.163.com](https://v.163.com/)
- 网易博客 [https://blog.163.com](https://blog.163.com/)
- 网易花田 [https://love.163.com](https://love.163.com/)
- 网易考拉 [https://www.kaola.com](https://www.kaola.com/)
- 网易 Lofter [http://www.lofter.com](http://www.lofter.com/)

### [SSO 有什么好处？](https://javaguide.cn/system-design/security/sso-intro.html#sso-%E6%9C%89%E4%BB%80%E4%B9%88%E5%A5%BD%E5%A4%84)

1. **用户角度** : 用户能够做到一次登录多次使用，无需记录多套用户名和密码，省心。
2. **系统管理员角度** : 管理员只需维护好一个统一的账号中心就可以了，方便。
3. **新系统开发角度:** 新系统开发时只需直接对接统一的账号中心即可，简化开发流程，省时。

## [SSO 设计与实现](https://javaguide.cn/system-design/security/sso-intro.html#sso-%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E7%8E%B0)

本篇文章也主要是为了探讨如何设计 & 实现一个 SSO 系统

以下为需要实现的核心功能：

- 单点登录
- 单点登出
- 支持跨域单点登录
- 支持跨域单点登出

### [核心应用与依赖](https://javaguide.cn/system-design/security/sso-intro.html#%E6%A0%B8%E5%BF%83%E5%BA%94%E7%94%A8%E4%B8%8E%E4%BE%9D%E8%B5%96)

![[100_attachements/95dd7d96c2b35baae9a97b4742449da8_MD5.png]]

|应用 / 模块 / 对象|说明|
|---|---|
|前台站点|需要登录的站点|
|SSO 站点 - 登录|提供登录的页面|
|SSO 站点 - 登出|提供注销登录的入口|
|SSO 服务 - 登录|提供登录服务|
|SSO 服务 - 登录状态|提供登录状态校验 / 登录信息查询的服务|
|SSO 服务 - 登出|提供用户注销登录的服务|
|数据库|存储用户账户信息|
|缓存|存储用户的登录信息，通常使用 Redis|

### [用户登录状态的存储与校验](https://javaguide.cn/system-design/security/sso-intro.html#%E7%94%A8%E6%88%B7%E7%99%BB%E5%BD%95%E7%8A%B6%E6%80%81%E7%9A%84%E5%AD%98%E5%82%A8%E4%B8%8E%E6%A0%A1%E9%AA%8C)

常见的 Web 框架对于 Session 的实现都是生成一个 SessionId 存储在浏览器 Cookie 中。然后将 Session 内容存储在服务器端内存中，这个 [ken.io](https://ken.io/) 在之前 [Session 工作原理](https://ken.io/note/session-principle-skill)中也提到过。整体也是借鉴这个思路。

用户登录成功之后，生成 AuthToken 交给客户端保存。如果是浏览器，就保存在 Cookie 中。如果是手机 App 就保存在 App 本地缓存中。本篇主要探讨基于 Web 站点的 SSO。

用户在浏览需要登录的页面时，客户端将 AuthToken 提交给 SSO 服务校验登录状态 / 获取用户登录信息

对于登录信息的存储，建议采用 Redis，使用 Redis 集群来存储登录信息，既可以保证高可用，又可以线性扩充。同时也可以让 SSO 服务满足负载均衡 / 可伸缩的需求。

|对象|说明|
|---|---|
|AuthToken|直接使用 UUID/GUID 即可，如果有验证 AuthToken 合法性需求，可以将 UserName + 时间戳加密生成，服务端解密之后验证合法性|
|登录信息|通常是将 UserId，UserName 缓存起来|

### [用户登录 / 登录校验](https://javaguide.cn/system-design/security/sso-intro.html#%E7%94%A8%E6%88%B7%E7%99%BB%E5%BD%95-%E7%99%BB%E5%BD%95%E6%A0%A1%E9%AA%8C)

**登录时序图**

![[100_attachements/c1b5ea47fe75a47f75af14e5ef3418a9_MD5.png]]

按照上图，用户登录后 AuthToken 保存在 Cookie 中。 [domain=test.com](http://domain=test.com/)  
浏览器会将 domain 设置成 .test.com，

这样访问所有 *.test.com 的 web 站点，都会将 AuthToken 携带到服务器端。  
然后通过 SSO 服务，完成对用户状态的校验 / 用户登录信息的获取

**登录信息获取 / 登录状态校验**

![[100_attachements/b9e916e3631ce69ea6e415220f91f345_MD5.png]]

### [用户登出](https://javaguide.cn/system-design/security/sso-intro.html#%E7%94%A8%E6%88%B7%E7%99%BB%E5%87%BA)

用户登出时要做的事情很简单：

1. 服务端清除缓存（Redis）中的登录状态
2. 客户端清除存储的 AuthToken

**登出时序图**

![[100_attachements/e18851e82eeb52b1997ce74ca140329f_MD5.png]]

### [跨域登录、登出](https://javaguide.cn/system-design/security/sso-intro.html#%E8%B7%A8%E5%9F%9F%E7%99%BB%E5%BD%95%E3%80%81%E7%99%BB%E5%87%BA)

前面提到过，核心思路是客户端存储 AuthToken，服务器端通过 Redis 存储登录信息。由于客户端是将 AuthToken 存储在 Cookie 中的。所以跨域要解决的问题，就是如何解决 Cookie 的跨域读写问题。

解决跨域的核心思路就是：

- 登录完成之后通过回调的方式，将 AuthToken 传递给主域名之外的站点，该站点自行将 AuthToken 保存在当前域下的 Cookie 中。
- 登出完成之后通过回调的方式，调用非主域名站点的登出页面，完成设置 Cookie 中的 AuthToken 过期的操作。

**跨域登录（主域名已登录）**

![[100_attachements/d65316bc6a373cd424d3a92a9494cccc_MD5.png]]

**跨域登录（主域名未登录）**

![[100_attachements/7ce90350583a207f348e24ed7904fdb3_MD5.png]]

**跨域登出**

![[100_attachements/fc81e8e5a444e29bd144f1d04c036a93_MD5.png]]

## [说明](https://javaguide.cn/system-design/security/sso-intro.html#%E8%AF%B4%E6%98%8E)

- 关于方案：这次设计方案更多是提供实现思路。如果涉及到 APP 用户登录等情况，在访问 SSO 服务时，增加对 APP 的签名验证就好了。当然，如果有无线网关，验证签名不是问题。
- 关于时序图：时序图中并没有包含所有场景，只列举了核心 / 主要场景，另外对于一些不影响理解思路的消息能省就省了。