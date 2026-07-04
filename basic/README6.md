# 第七章 邮箱配置

Odoo 的邮件功能很重要。报价单、销售订单、发票、密码重置、客户门户邀请、系统通知，都可能通过邮件发送。

国内客户配置邮箱时，经常遇到“国外能用、国内不好用”的情况。本章先讲清楚 Odoo 原生邮件逻辑，再说明国内邮箱常见问题。

## 邮件功能用在哪里

Odoo 中常见邮件场景：

- 给客户发送报价单；
- 给客户发送订单确认；
- 给客户发送发票；
- 给用户发送重置密码邮件；
- 给客户发送门户邀请；
- 员工在单据沟通区给客户发消息；
- 系统发送活动提醒；
- 客户回复邮件后，消息回到对应单据。

如果邮件没有配置好，销售、财务、门户和协同都会受影响。

## 邮件设置入口

在 **设置** 中搜索“邮件”，可以看到邮件相关配置。

![邮件设置](images/basic-cn-email-settings-search.png)

常见设置包括：

- 使用自定义电子邮件服务器；
- 邮件别名域；
- 邮件模板；
- 摘要邮件；
- 双因素认证邮件；
- 邮件插件或集成。

不同 Odoo 版本和安装模块不同，界面上看到的选项会略有差异。

## 发送邮件的基本逻辑

Odoo 发送邮件一般需要 SMTP 服务器。

SMTP 配置通常包括：

| 字段 | 示例 |
| --- | --- |
| SMTP 服务器 | smtp.example.com |
| 端口 | 465、587 |
| 加密方式 | SSL/TLS 或 STARTTLS |
| 用户名 | service@example.com |
| 密码或授权码 | 邮箱密码、应用密码或授权码 |
| 发件人规则 | 是否允许代发 |

配置完成后，要发送测试邮件，确认邮件可以正常送达。

## 收取客户回复

Odoo 不只是发送邮件，也可以把客户回复收回到对应单据。

例如销售发送报价邮件后，客户直接回复邮件。如果收件配置正确，客户回复可以回到对应报价单或销售订单的消息区。

这依赖几个条件：

- 系统知道回复地址；
- 邮箱服务器能接收客户回复；
- Odoo 能定时读取收件箱；
- 邮件主题或 Message-ID 能匹配到原单据。

如果只是想先发报价，不一定第一天就配置完整收件逻辑。但如果企业重视邮件沟通留痕，收件配置就很重要。

## Catch-all 是什么

Catch-all 可以简单理解为“统一收件箱”。客户回复各种系统生成的地址时，邮件服务器把这些邮件统一收进一个邮箱，再由 Odoo 分配回对应单据。

原生 Odoo 在海外邮箱环境中比较常用这种方式。

但是国内很多邮箱：

- 免费版不支持 catch-all；
- 要求发件人必须等于登录邮箱；
- 不允许系统随意代发；
- 需要授权码或应用密码；
- 对海外服务器登录有限制；
- 对批量邮件有风控。

所以国内项目不要假设邮箱一定能按官方默认方式工作。

## 国内邮箱常见问题

| 问题 | 表现 | 处理方向 |
| --- | --- | --- |
| 发件人不一致 | SMTP 报错，提示发件人与认证用户不一致 | 使用固定发件邮箱或调整代发策略 |
| 不支持 catch-all | 客户回复无法自动回到单据 | 使用专用收件箱或定制方案 |
| 需要授权码 | 密码正确但无法登录 | 在邮箱后台生成授权码 |
| SMTP 未开启 | 测试连接失败 | 在邮箱后台开启 SMTP/IMAP/POP |
| 端口或加密错误 | 连接超时或认证失败 | 按邮箱官方参数配置 |
| 风控拦截 | 偶尔能发，偶尔失败 | 联系邮箱服务商或降低发送频率 |
| DNS 未配置 | 邮件进垃圾箱 | 配置 SPF、DKIM、DMARC |

国内企业邮箱建议优先使用稳定的企业邮箱服务，不建议用个人免费邮箱承载正式业务邮件。

## 推荐配置方式

小公司第一期可以先采用简单方案：

```text
统一发件邮箱 -> 发送报价、发票和系统通知
统一收件邮箱 -> 收客户回复或人工查看
```

例如：

- service@company.com 用于系统发件；
- reply@company.com 用于客户回复；
- 财务单独使用 finance@company.com；
- 销售个人邮箱先不直接接入 Odoo。

等流程稳定后，再考虑每个销售使用自己的邮箱、多公司邮箱、catch-all 或更复杂的邮件路由。

## 发送邮件测试

配置完邮箱后，至少测试：

1. 给内部邮箱发送测试邮件；
2. 给外部邮箱发送测试邮件；
3. 给 QQ、网易、企业微信、Outlook 等不同邮箱测试；
4. 检查是否进入垃圾箱；
5. 检查发件人显示是否正确；
6. 客户回复后是否能收到；
7. 报价单或发票邮件是否带附件；
8. 重置密码邮件是否能收到。

只测试“连接成功”不够。真正重要的是客户能不能收到，收到后能不能回复。

## 邮件模板

Odoo 中很多邮件都是通过模板生成的。例如报价邮件、发票邮件、密码重置邮件。

邮件模板会影响：

- 邮件标题；
- 正文内容；
- 按钮文字；
- 附件；
- 使用语言；
- 公司签名。

修改模板前要先复制或记录原始内容。不要随意删除模板变量，例如客户名称、单据链接、门户链接，否则邮件可能无法正常生成。

## 邮箱配置验收标准

| 检查项 | 通过标准 |
| --- | --- |
| SMTP | 可以正常发送邮件 |
| 收件 | 客户回复可以被收到 |
| 发件人 | 客户看到的发件人正确 |
| 垃圾箱 | 主流邮箱不进入垃圾箱或概率可接受 |
| 模板 | 报价、发票、重置密码模板内容正确 |
| 附件 | PDF 附件可以正常打开 |
| 门户链接 | 客户点击链接可以访问对应单据 |
| 测试库 | 测试库不会误发给真实客户 |

如果企业邮件非常关键，建议上线前用真实域名、真实客户邮箱做完整测试。

## 什么时候需要定制

以下情况原生配置可能不够：

- 每个销售必须使用自己的邮箱发送；
- 多公司使用不同邮箱域名；
- 客户回复必须自动进入对应单据；
- 要兼容不支持 catch-all 的国内邮箱；
- 要和企业微信、飞书、CRM 邮件归档打通；
- 要做复杂的发件人、回复人、抄送规则；
- 要批量营销邮件并做退订和追踪。

这时应先明确业务目标，再决定是原生配置、第三方模块还是定制开发。不要为了“看起来高级”一开始就做复杂邮件方案。

## 国内邮箱落地方案

理解了邮件发送、收件、catch-all 和模板之后，就可以进入真正的落地配置。国内项目的难点往往不在 Odoo 按钮，而在邮箱服务商规则、OAuth 认证、发件人匹配、多公司多邮箱和客户回复留痕。

下面这些方案对应国内项目中最常见的问题：Outlook/Office365 新式认证、腾讯企业邮严格发件人匹配、Gmail OAuth、多公司多邮箱、catch-all 不可用等。

## 原生系统的发件和收件

原生 Odoo 的邮件通常分为两部分：

- 发件服务器：负责把 Odoo 中的报价、发票、通知发出去；
- 收件服务器：负责把客户回复收回来，并尽量匹配到对应单据。

在标准海外邮箱环境中，Odoo 常使用 bounce、catch-all、邮件别名域等机制来处理发件和回信。但国内邮箱环境经常不完全支持这些机制，所以需要先理解原生逻辑，再决定是否使用本地化方案。

### 发件服务器

在设置中启用自定义邮件服务器后，可以配置 SMTP 发件服务。

![发件箱配置](images/email1.png)

一个典型的发件箱配置如下：

![发件服务器](images/email3.png)

典型字段包括：

- 描述：邮箱服务器名称；
- SMTP Server：发送邮件的 SMTP 地址；
- SMTP 端口：常见为 465 或 587；
- 连接安全性：SSL/TLS 或 STARTTLS；
- 用户名：邮箱账号；
- 密码：邮箱密码、授权码或应用密码。

国内使用网易、腾讯、阿里等企业邮箱时，经常不是填写登录密码，而是填写邮箱后台生成的授权码。

配置完成后，可以点击测试连接。如果邮箱配置正确，会看到类似下面的成功提示：

![成功提示](images/email2.png)

也可以在开发者模式下，通过技术菜单中的邮件记录发送测试邮件。

![测试邮件](images/mail4.png)

用户收到的测试邮件示例：

![收件效果](images/email5.png)

### Bounce 账号

Bounce 账号可以理解为系统代发和退信处理相关的邮箱别名。

例如系统用户 Kevin 的邮箱是 `kevin@example.com`，系统通过 `bounce@example.com` 代发邮件时，收件人可能会看到“由 bounce 代发”的效果。

这种方式的好处是多个用户可以共用一个系统发件服务，缺点是在国内邮箱中经常遇到“声明发件人和认证邮箱不一致”的限制。

Bounce 相关参数可以在系统参数中查看或调整。

![Bounce参数](images/mail6.png)

### Catch-all 账号

Catch-all 又叫全收邮箱。它的作用是把发给某个域名下不存在账号的邮件统一收进一个邮箱，再交给 Odoo 根据邮件头信息分配到对应单据。

收件服务器配置示例：

![收件服务器配置](images/email7.png)

例如客户回复报价邮件时，Odoo 可以尝试把回复自动放回对应销售订单的消息区。

但国内常见问题是：

- 免费企业邮箱不支持 catch-all；
- 支持 catch-all 但需要额外付费；
- 邮件服务商要求发件人必须等于认证用户；
- 客户回复无法稳定回到 Odoo 单据。

所以国内项目里，catch-all 要提前测试，不要默认认为一定可用。

### 邮件发送与客户回复测试

可以在销售订单中创建一张订单，然后向客户发送报价邮件。

![销售订单发送邮件](images/sale1.png)

发送后，消息区会记录已经发出的邮件。

![消息区记录](images/sale2.png)

客户收到邮件后的效果示例：

![客户收到邮件](images/sale3.png)

如果客户直接回复邮件，回复地址会进入系统声明的收件邮箱或 catch-all 邮箱。

![客户回复地址](images/sale4.png)

在技术菜单中的邮件记录里，可以进一步检查邮件和业务单据之间的关联。

![邮件记录详情](images/sale5.png)

## 国内免费邮箱的常见报错

国内邮箱最常见的问题是发件人不匹配。

典型报错类似：

```text
SMTPSenderRefused: 553 Mail from must equal authorized user
```

或：

```text
SMTPSenderRefused: 501 mail from address must be same as authorization user
```

意思是：Odoo 声明的发件人和 SMTP 登录邮箱不是同一个邮箱，邮件服务器拒绝发送。

下面是一个多公司和国内邮箱环境下的示例配置。

![多公司邮箱示例](images/email8.png)

用户邮箱和声明发件人不一致时，国内邮箱服务商可能会拒绝发送。

![用户邮箱示例](images/email9.png)

可选方案：

- 使用固定公司邮箱作为统一发件人；
- 删除或调整 catch-all 相关系统参数；
- 让每个用户邮箱都能通过对应 SMTP 认证；
- 使用支持 OAuth 或代发策略的企业邮箱；
- 使用专门处理国内邮箱限制的 Odoo 邮件模块。

第一种方式最简单，但无法满足“每个销售用自己邮箱对外沟通”的场景。后几种方式更灵活，但配置和维护成本更高。

## 欧姆网络邮箱解决方案

在外贸和多公司项目中，一个员工可能在不同公司下使用不同邮箱跟客户沟通。例如：

- A 公司下使用 `sales@companya.com`；
- B 公司下使用 `sales@companyb.com`；
- 客户回复要进入对应公司或对应单据；
- 发件箱需要按公司、域名、用户进行匹配。

原生 Odoo 对这种场景支持有限。为了解决国内邮箱、严格发件人匹配、多公司邮箱、catch-all 不可用等问题，可以使用欧姆网络的邮箱配置解决方案。

模块入口：

[欧姆邮箱配置解决方案](https://odoohub.com.cn/shop/11)

模块示例：

![欧姆邮箱模块](images/mommy_mail.png)

安装后，可以在通用设置中看到和 catch-all、严格发件人匹配相关的配置项。

![Catch All设置](images/13.jpg)

配置后再次发送邮件，可以解决部分国内邮箱严格发件人匹配导致的失败问题。

![修正后发送邮件](images/sale6.png)

客户收到邮件示例：

![修正后客户收件](images/sale7.png)

典型能力：

- 发件箱支持公司维度配置；
- 发件箱可以匹配对应收件箱；
- 支持限制发件人和认证邮箱一致；
- 支持不使用 catch-all 的国内邮箱环境；
- 支持用户在多公司下配置优先邮箱；
- 适合外贸、多公司、多邮箱销售团队。

### 发件箱公司匹配

在多公司环境中，可以为发件箱指定适用公司。留空表示所有公司可用。

![发件箱公司配置](./images/email16.png)

这样可以避免 A 公司业务误用 B 公司邮箱发信。

### Catch-all 匹配

发件箱可以关联对应收件箱。配置后，由该发件箱发出的邮件，可以声明对应的回复地址。

![发件箱Catch All配置](./images/email17.png)

例如：

```text
发件箱：service@odoomommy.com
回复邮箱：catchall@odoohub.com.cn
```

客户回复时，会回复到指定的 catch-all 或收件邮箱，再由系统处理。

### 用户多公司优先邮箱

如果员工在不同公司下使用不同邮箱，可以在用户上配置多公司优先邮箱。

![用户多公司邮箱配置](./images/email18.png)

例如：

| 公司 | 用户邮箱 |
| --- | --- |
| A 公司 | a@companya.com |
| B 公司 | a@companyb.com |

系统发送邮件时，可以根据当前公司优先使用对应邮箱。

> 注意：邮箱后缀需要能匹配到对应发件箱域名，否则仍可能发送失败。

## Office365 / Outlook OAuth 配置

Office365 和 Outlook 逐步收紧了基本身份验证。很多情况下，不能再简单用邮箱密码配置 SMTP/IMAP，而需要使用 OAuth 新式认证。

配置前提：

- Odoo 需要能通过 HTTPS 访问；
- 回调地址需要和 Odoo 的 base URL 一致；
- Microsoft Entra ID 中需要创建应用；
- 用户或组织需要允许对应权限；
- SMTP Auth 可能需要单独开启。

### 注册应用

进入 Microsoft Azure / Entra 管理后台，创建应用注册。

![Office365注册应用](images/office1.png)

应用类型通常选择 Web，回调地址填写：

```text
https://你的Odoo域名/microsoft_outlook/confirm
```

如果使用本地测试环境，部分 OAuth 场景只允许 HTTPS 或 `http://localhost`，普通 HTTP 域名可能无法通过。

应用注册页面示例：

![Office365应用注册详情](images/office2.png)

### 设置 API 权限

在应用的 API 权限中，按 Odoo Outlook 集成要求添加邮件相关权限。

![Office365 API权限入口](images/office3.png)

![Office365 API权限选择](images/office4.png)

最终权限界面示例：

![Office365权限结果](images/office5.png)

常见权限包括：

- 读取用户基本资料；
- 发送邮件；
- 读取邮件；
- 离线访问令牌。

具体权限会随 Odoo 版本和 Microsoft 策略变化，配置时以当前 Odoo 界面提示和 Microsoft 后台为准。

还需要确认用户或用户组可以使用该应用。

![Office365企业应用](images/office6.png)

![Office365用户和组](images/office7.png)

![Office365分配用户](images/office8.png)

### 创建凭据

Odoo 需要两个关键参数：

- Client ID；
- Client Secret。

Client ID 可以在应用概览页面获取。Client Secret 需要在证书和密码中生成。生成后要立即保存，后续可能无法再次查看明文。

Client ID 位置示例：

![Office365 Client ID](images/office9.png)

Client Secret 生成入口：

![Office365 Client Secret](images/office10.png)

### 在 Odoo 中连接 Outlook 账号

回到 Odoo，填写 Client ID 和 Client Secret，然后创建或编辑邮箱服务器。

![Odoo Outlook凭据](images/office11.png)

服务器类型选择 Outlook OAuth 认证，点击连接 Outlook 账号，跳转到 Microsoft 登录页面完成授权。

![Odoo Outlook邮箱服务器](images/office13.png)

完成授权后，邮箱服务器会显示令牌有效状态。

![Outlook授权成功](images/office14.png)

授权成功后，回到 Odoo 测试连接。

### Office365 常见问题

**SmtpClientAuthentication is disabled for the tenant**

这通常表示租户或用户没有启用 Authenticated SMTP。

![Office365 SMTP Auth提示](./images/office12.png)

处理方向：

- 进入 Microsoft 365 管理中心；
- 找到目标用户；
- 编辑邮件应用设置；
- 勾选 Authenticated SMTP；
- 等待策略生效后重新测试。

Microsoft 365 管理中心示例：

![Office365用户管理](./images/office15.png)

Email App 中开启 Authenticated SMTP：

![Office365开启SMTP](./images/office16.png)

**Redirect URI 不匹配**

检查：

- Odoo 的 Web Base URL；
- Microsoft 应用中的重定向 URI；
- 是否使用 HTTPS；
- 域名后是否多了斜杠或路径不一致。

## 腾讯企业邮配置

腾讯企业邮在国内项目中很常见，但经常遇到“发件人必须等于认证用户”的限制。

常用服务器参数：

```text
POP3/SMTP 协议
接收邮件服务器：pop.exmail.qq.com，SSL，端口 995
发送邮件服务器：smtp.exmail.qq.com，SSL，端口 465

IMAP 协议
接收邮件服务器：imap.exmail.qq.com，SSL，端口 993
发送邮件服务器：smtp.exmail.qq.com，SSL，端口 465
```

配置前要确认：

- 邮箱账号已启用 POP/IMAP/SMTP；
- 使用的是授权码或正确密码；
- 企业微信/腾讯企业邮后台没有限制第三方客户端；
- Odoo 发件人和 SMTP 认证用户是否一致。

腾讯企业邮后台配置示例：

![腾讯企业邮配置](./images/tx1.png)

常见报错：

```text
SMTPSenderRefused: 501 mail from address must be same as authorization user
```

如果企业要求多个用户通过同一个腾讯企业邮发信，就需要调整发件策略，或使用支持严格发件人匹配处理的模块。

## Gmail OAuth 配置

Gmail 也常使用 OAuth 认证，不建议直接使用普通密码。

在 Odoo 设置中启用 Gmail 后，系统会要求填写 ID 和密钥。

![Gmail设置](./images/gmail.png)

配置大致步骤：

1. 打开 Google Cloud Console；
2. 创建项目；
3. 配置 OAuth 同意屏幕；
4. 创建 Web 应用 OAuth 凭据；
5. 设置授权重定向 URI；
6. 将 Client ID 和 Client Secret 填入 Odoo；
7. 创建 Gmail 发件服务器；
8. 点击连接 Gmail 账号完成授权。

重定向 URI 通常类似：

```text
https://你的Odoo域名/google_gmail/confirm
```

Google Cloud Console 中新建项目示例：

![Google项目](./images/gmail2.png)

OAuth 同意屏幕示例：

![Google OAuth同意屏幕](./images/gmail3.png)

创建 OAuth 凭据：

![Google OAuth凭据](./images/gmai4.png)

填写授权重定向 URI：

![Google重定向URI](./images/gmail5.png)

在 Odoo 中创建 Gmail 发件箱：

![Gmail发件箱](./images/gmail6.png)

点击连接 Gmail 账号完成认证：

![连接Gmail账号](./images/gmail7.png)

认证成功后的状态示例：

![Gmail认证成功](./images/gmail8.png)

常见问题：

| 问题 | 处理方向 |
| --- | --- |
| Redirect URI mismatch | 检查 Google 后台回调地址和 Odoo base URL |
| 认证后 500 错误 | 检查网络、域名、证书和 OAuth 配置 |
| 测试用户无法授权 | 检查 OAuth 同意屏幕测试用户列表 |
| 发信失败 | 检查 Gmail API、授权范围和邮箱服务器配置 |

Gmail 在国内网络环境中可能不稳定，国内客户使用前要充分测试。
