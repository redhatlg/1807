## 账目

---

帐户是Stellar中的中央数据结构。帐户由公共密钥标识并保存在分类帐中。分类帐中的其他所有内容，例如要约或[信任线](https://www.stellar.org/developers/guides/concepts/assets.html#trustlines)，都由特定帐户拥有。

使用“[创建帐户”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#create-account)操作[创建帐户](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#create-account)。

帐户访问由公用/专用密钥加密控制。为了使帐户执行交易（例如付款），交易必须由与该帐户的公钥相对应的私钥签名。您还可以设置更复杂的[多重签名](https://www.stellar.org/developers/guides/concepts/multi-sig.html)方案来授权帐户上的交易。

## 帐户字段 {#account-fields}

帐户具有以下字段：

####    帐户ID {#account-id}

   最初用于创建帐户的公钥。您可以使用其他公用密钥替换用于签署帐户交易的密钥，但是原始帐户ID将始终用于标识该帐户。

####    平衡 {#balance}

    帐户拥有的流明数。余额以1 / 10,000,000流明（最小的流明可分单位）计价。

####     序列号 {#sequence-number}

    帐户的当前交易序列号。此数字开头等于创建帐户的分类帐编号。

    子条目数 

     帐户拥有的其他[条目](https://www.stellar.org/developers/guides/concepts/ledger.html#ledger-entries)数。该数字用于计算帐户的[最低余额](https://www.stellar.org/developers/guides/concepts/fees.html#minimum-account-balance)。

####     通货膨胀目的地 {#inflation-destination}

     （可选）指定用于接收通货膨胀的帐户。每个余额至少为100 XLM的帐户都可以投票将[通货膨胀](https://www.stellar.org/developers/guides/concepts/inflation.html)发送到目标帐户。

    标志

     当前，[资产](https://www.stellar.org/developers/guides/concepts/assets.html)发行人使用三个标志。

         **需要授权（0x1）**

         ：要求发卡帐户授予其他帐户权限，然后他们才能持有发卡帐户的信

         **可撤销授权（0x2）  **

        允许发行帐户撤销其他帐户持有的信用

> **不可变授权（0x4）**
> ：如果设置了该权限，则不能更改任何授权标志，并且永远不能删除该帐户。
> #### 归属域 {#home-domain}
>
> 可以选择将其添加到帐户中的域名。客户可以从该域中查找[stellar.toml](https://www.stellar.org/developers/guides/concepts/stellar-toml.html)。该格式应为[完全限定域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)的格式，例如`example.com`。
>
> 联合协议可以使用归属域来查找有关交易备忘录的更多详细信息或有关帐户的[地址](https://www.stellar.org/developers/learn/concepts/federation.html#stellar-addresses)详细信息。有关联合的更多信息，请参见[联合指南](https://www.stellar.org/developers/guides/concepts/federation.html)。
>
> #### 门槛 {#thresholds}
>
> 操作具有不同的访问级别。此字段指定低，中和高访问级别的阈值以及主密钥的权重。有关更多信息，请参见[multi-sig](https://www.stellar.org/developers/guides/concepts/multi-sig.html)。
>
> #### 签名人 {#signers}
>
> 用于[多信号](https://www.stellar.org/developers/guides/concepts/multi-sig.html)。该字段列出了其他公用密钥及其权重，可用于授权该帐户的交易。
>
> #### 负债 {#liabilities}
>
> 从协议版本10开始，每个帐户还跟踪其流明负债。购买负债等于该帐户拥有的所有要约中提供的购买总流明总数，而销售负债等于此帐户拥有的所有要约中所提供的出售总流明总数。账户的余额必须始终高于最低准备金，才能满足其流明销售负债；余额必须低于最高储备，以适应其流明购买负债。

  
帐户是Stellar中的中央数据结构。帐户由公共密钥标识并保存在分类帐中。分类帐中的其他所有内容，例如要约或[信任线](https://www.stellar.org/developers/guides/concepts/assets.html#trustlines)，都由特定帐户拥有。

使用“[创建帐户”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#create-account)操作[创建帐户](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#create-account)。

帐户访问由公用/专用密钥加密控制。为了使帐户执行交易（例如付款），交易必须由与该帐户的公钥相对应的私钥签名。您还可以设置更复杂的[多重签名](https://www.stellar.org/developers/guides/concepts/multi-sig.html)方案来授权帐户上的交易。

## 帐户字段 {#account-fields}

帐户具有以下字段：

> #### 帐户ID {#account-id}
>
> 最初用于创建帐户的公钥。您可以使用其他公用密钥替换用于签署帐户交易的密钥，但是原始帐户ID将始终用于标识该帐户。
>
> #### 平衡 {#balance}
>
> 帐户拥有的流明数。余额以1 / 10,000,000流明（最小的流明可分单位）计价。
>
> #### 序列号 {#sequence-number}
>
> 帐户的当前交易序列号。此数字开头等于创建帐户的分类帐编号。
>
> #### 子条目数 {#number-of-subentries}
>
> 帐户拥有的其他[条目](https://www.stellar.org/developers/guides/concepts/ledger.html#ledger-entries)数。该数字用于计算帐户的[最低余额](https://www.stellar.org/developers/guides/concepts/fees.html#minimum-account-balance)。
>
> #### 通货膨胀目的地 {#inflation-destination}
>
> （可选）指定用于接收通货膨胀的帐户。每个余额至少为100 XLM的帐户都可以投票将[通货膨胀](https://www.stellar.org/developers/guides/concepts/inflation.html)发送到目标帐户。
>
> #### 标志 {#flags}
>
> 当前，[资产](https://www.stellar.org/developers/guides/concepts/assets.html)发行人使用三个标志。
>
> * **需要授权（0x1）**
>   ：要求发卡帐户授予其他帐户权限，然后他们才能持有发卡帐户的信用。
> * **可撤销授权（0x2）**
>   ：允许发行帐户撤销其他帐户持有的信用。
> * **不可变授权（0x4）**
>   ：如果设置了该权限，则不能更改任何授权标志，并且永远不能删除该帐户。
>
> #### 归属域 {#home-domain}
>
> 可以选择将其添加到帐户中的域名。客户可以从该域中查找[stellar.toml](https://www.stellar.org/developers/guides/concepts/stellar-toml.html)。该格式应为[完全限定域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)的格式，例如`example.com`。
>
> 联合协议可以使用归属域来查找有关交易备忘录的更多详细信息或有关帐户的[地址](https://www.stellar.org/developers/learn/concepts/federation.html#stellar-addresses)详细信息。有关联合的更多信息，请参见[联合指南](https://www.stellar.org/developers/guides/concepts/federation.html)。
>
> #### 门槛 {#thresholds}
>
> 操作具有不同的访问级别。此字段指定低，中和高访问级别的阈值以及主密钥的权重。有关更多信息，请参见[multi-sig](https://www.stellar.org/developers/guides/concepts/multi-sig.html)。
>
> #### 签名人 {#signers}
>
> 用于[多信号](https://www.stellar.org/developers/guides/concepts/multi-sig.html)。该字段列出了其他公用密钥及其权重，可用于授权该帐户的交易。
>
> #### 负债 {#liabilities}
>
> 从协议版本10开始，每个帐户还跟踪其流明负债。购买负债等于该帐户拥有的所有要约中提供的购买总流明总数，而销售负债等于此帐户拥有的所有要约中所提供的出售总流明总数。账户的余额必须始终高于最低准备金，才能满足其流明销售负债；须低于最高储备，以适应其流明购买负债。

  


