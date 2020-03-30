

---

![](https://www.stellar.org/developers/guides/get-started/assets/developers.png "恒星生态系统")

使用Stellar网络，您可以构建自己的手机钱包，银行工具，可自行支付费用的智能设备，以及几乎所有您梦involving以求的涉及支付的东西！即使Stellar是一个复杂的分布式系统，使用它也不需要很复杂。

## API：地平线 {#api-horizon}

**大多数应用程序通过恒星网络交互**[**地平线**](https://www.stellar.org/developers/horizon/reference/)**，**一个RESTful HTTP API服务器。Horizo​​n提供了一种简单的方式来提交事务，检查帐户和订阅事件。由于它只是HTTP，因此您可以使用Web浏览器，简单的命令行工具（例如cURL）或适用于您喜欢的编程语言的Stellar SDK与Horizo​​n通信。

安装Horizo​​n的最简单方法是使用[**stellar / quickstart**docker image](https://hub.docker.com/r/stellar/quickstart/)。

[Stellar.org](http://stellar.org/)维护用于与Horizo​​n通信的基于[JavaScript](https://github.com/stellar/js-stellar-sdk)，[Java](https://github.com/stellar/java-stellar-sdk)和[Go](https://github.com/stellar/go/tree/master/clients/horizon)的SDK。还有针对[Ruby](https://github.com/stellar/ruby-stellar-sdk)，[Python](https://github.com/StellarCN/py-stellar-base)和[C＃的](https://github.com/elucidsoft/dotnet-stellar-sdk)社区维护的SDK。

## 网络骨干：恒星核心 {#network-backbone-stellar-core}

在幕后，每个Horizo​​n服务器都连接到[**Stellar Core**](https://www.stellar.org/developers/stellar-core/software/admin.html)**（Stellar网络的骨干网）。**Stellar Core软件通过[Stellar Consensus Protocol](https://www.stellar.org/developers/guides/concepts/scp.html)（SCP）来完成每笔交易状态的验证并与Core的其他实例达成一致的艰苦工作。恒星网络本身是由世界各地的个人和实体运营的相连恒星核心的集合。有些实例具有您可以与之通信的Horizo​​n服务器，而另一些实例的存在只是为了提高整个网络的可靠性。

安装Stellar Core的最简单方法是使用[**stellar / quickstart**docker image](https://hub.docker.com/r/stellar/quickstart/)。

您可能希望托管自己的Stellar Core实例，以便在不依赖第三方的情况下提交交易，对信任对象有更多控制权，或者只是为了使Stellar网络对其他人更可靠和更可靠。

## 大图：恒星网络 {#big-picture-the-stellar-network}

Stellar网络是Stellar核心的全球集合，每个核心由不同的人员和组织维护。网络的分布式性质使其可靠且安全。

所有这些恒星核心（节点网络）最终都同意交易集。网络上的每笔交易都需要支付少量费用：100列（0.00001XLM）。该费用有助于防止不良行为者向网络发送垃圾邮件。

为了帮助您测试工具和应用程序，[Stellar.org](http://stellar.org/)运营着一个小型测试网络和Horizo​​n实例。[开始使用测试网。](https://www.stellar.org/developers/guides/concepts/test-net.html)![](https://www.stellar.org/developers/guides/get-started/assets/developers.png "恒星生态系统")

使用Stellar网络，您可以构建自己的手机钱包，银行工具，可自行支付费用的智能设备，以及几乎所有您梦involving以求的涉及支付的东西！即使Stellar是一个复杂的分布式系统，使用它也不需要很复杂。

## API：地平线 {#api-horizon}

**大多数应用程序通过恒星网络交互**[**地平线**](https://www.stellar.org/developers/horizon/reference/)**，**一个RESTful HTTP API服务器。Horizo​​n提供了一种简单的方式来提交事务，检查帐户和订阅事件。由于它只是HTTP，因此您可以使用Web浏览器，简单的命令行工具（例如cURL）或适用于您喜欢的编程语言的Stellar SDK与Horizo​​n通信。

安装Horizo​​n的最简单方法是使用[**stellar / quickstart**docker image](https://hub.docker.com/r/stellar/quickstart/)。

[Stellar.org](http://stellar.org/)维护用于与Horizo​​n通信的基于[JavaScript](https://github.com/stellar/js-stellar-sdk)，[Java](https://github.com/stellar/java-stellar-sdk)和[Go](https://github.com/stellar/go/tree/master/clients/horizon)的SDK。还有针对[Ruby](https://github.com/stellar/ruby-stellar-sdk)，[Python](https://github.com/StellarCN/py-stellar-base)和[C＃的](https://github.com/elucidsoft/dotnet-stellar-sdk)社区维护的SDK。

## 网络骨干：恒星核心 {#network-backbone-stellar-core}

在幕后，每个Horizo​​n服务器都连接到[**Stellar Core**](https://www.stellar.org/developers/stellar-core/software/admin.html)**（Stellar网络的骨干网）。**Stellar Core软件通过[Stellar Consensus Protocol](https://www.stellar.org/developers/guides/concepts/scp.html)（SCP）来完成每笔交易状态的验证并与Core的其他实例达成一致的艰苦工作。恒星网络本身是由世界各地的个人和实体运营的相连恒星核心的集合。有些实例具有您可以与之通信的Horizo​​n服务器，而另一些实例的存在只是为了提高整个网络的可靠性。

安装Stellar Core的最简单方法是使用[**stellar / quickstart**docker image](https://hub.docker.com/r/stellar/quickstart/)。

您可能希望托管自己的Stellar Core实例，以便在不依赖第三方的情况下提交交易，对信任对象有更多控制权，或者只是为了使Stellar网络对其他人更可靠和更可靠。

## 大图：恒星网络 {#big-picture-the-stellar-network}

Stellar网络是Stellar核心的全球集合，每个核心由不同的人员和组织维护。网络的分布式性质使其可靠且安全。

所有这些恒星核心（节点网络）最终都同意交易集。网络上的每笔交易都需要支付少量费用：100列（0.00001XLM）。该费用有助于防止不良行为者向网络发送垃圾邮件。

为了帮助您测试工具和应用程序，[Stellar.org](http://stellar.org/)运营着一个小型测试网络和Horizo​​n实例。[开始使用测试网。](https://www.stellar.org/developers/guides/concepts/test-net.html)

