# XERO 对接

Xero是一款基于云的会计软件，专为中小型企业设计，用于管理财务和简化账务流程，在新西兰和澳大利亚有着广泛的应用。本章将演示如何使用Odoo跟Xero进行无缝链接。

## XERO端设置

首先，我们来一下XERO端的设置，我们打开[XERO开发者中心](https://developer.xero.com/app/manage/)，创建一个应用：

![xero](./images/xero.png)

这里需要填写应用名称和应用的网站。

然后我们点击左边的配置，配置我们应用的回调地址：

![xero2](./images/xero2.png)

这里我们要获取两个重要的参数：

* Client Id：应用的APPID
* Client Secret： 应用的密钥

### 获取Tenants ID

接下来就是获取商家ID这个参数，点击API Expoloer

![xero3](./images/xero3.png)

在底部的Headers信息中获取 Tenant ID。

做完以上两步就可以在Odoo中进行对接了。

## Odoo端设置

首先，我们在应用中心中安装mommy_xero模块：

![xero4](./images/xero4.png)

### 创建Xero App

我们在设置中新建一个Xero App，然后将我们在第一步获取到的应用APPID和密钥填到相应的位置中：

然后我们在主应用点击Xero应用进入设置页面：

![xero5](./images/xero5.png)

![xero6](./images/xero6.png)

### 绑定公司

创建完应用后，我们需要将应用绑定到当前公司才可以使用，在设置-公司中点击公司，然后选择当前公司，在Xero选项卡中选择我们刚才创建的应用：

![xero7](./images/xero7.png)

### OAuth授权

由于Xero认证采取的是OAuth2的认证方式，因此我们只有再经过授权之后才可以正常使用。我们回到Xero应用，点击授权按钮，跳转到Xero官网进行认证：

![xero8](./images/xero8.png)

认证完成后，页面会自动跳回到我们的App页面，如果没错，那我们可以看到我们的App处于已认证的状态。

![xero9](./images/xero9.png)

这就意味这我们可以使用Odoo正常跟Xero进行对接了。

## Odoo中的应用

首先，我们来看一下如何将Odoo中的发票同步给Xero

### 发票同步

我们在会计模块中打开客户发票：

![xero10](./images/xero10.png)

然后当我们点击确认或同步Xero按钮后，此发票就会被同步到Xero:

![xero11](./images/xero11.png)

同步成功后，我们可以在xero选项卡中看到xero的invoice id和invocie number

我们到Xero官网上也可以看到我们的发票信息：

![xero12](./images/xero12.png)

#### 修改同步

当我们修改了发票信息想要同步到Xero时，直接点击同步发票按钮即可。

![xero13](./images/xero13.png)

### 科目类型匹配

由于Odoo和Xero的科目类型并不严格一致，因此我们在应用增加了科目类型匹配的策略：

![xero14](./images/xero14.png)

用户可以根据自己的需求调整匹配关系。

之后将调整完的策略绑定到Xero App中即可。

### 手动匹配科目

如果不想让odoo的科目和xero的科目代码保持一致，那么我们可以通过手动的填写xero代码的方式完成匹配。具体步骤：
打开科目列表，找到要匹配的科目，点击Xero选项卡，填写匹配的科目代码即可。

![xero15](./images/xero15.png)

匹配完成后，我们就可以在业务中使用xero的code进行数据同步了。例如，此时我们就可以在invoice中点击同步将我们的发票明细同步给xero，xero会根据自己的科目设置应用相关的处理。

