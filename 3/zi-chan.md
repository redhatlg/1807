# 资产

---

恒星分布式网络可用于跟踪，持有和转移任何类型的**资产**：美元，欧元，比特币，股票，黄金和其他价值代币。网络上的任何资产都可以与其他任何资产进行交易和交换。

除流明（见下文）外，所有资产均具有

* **资产类型**
  ：例如，美元或比特币
* **发行人**
  ：创建资产的帐户

## 信任线 {#trustlines}

当您在Stellar中持有资产时，实际上就是从特定发行人那里获得信贷。发行人已同意将您在Stellar网络上的信用额度用于交易Stellar以外的相应资产（例如法定货币，贵金属）。假设斯科特（Scott）在网络上发放橙子作为信用。如果您持有橙色信用，则您和Scott会基于信任或信任线达成协议：你们都同意，当您向Scott授予橙色信用时，他会给您橙色。

持有资产时，您必须信任发行人正确赎回其信用额。由于Stellar的用户将不希望仅信任任何发行人，因此帐户必须明确信任发行帐户，然后才能持有发行人的信用。在上面的示例中，您必须明确信任Scott才能持有橙色积分。

要信任发行帐户，请创建一条**信任线。**信任线是保留在恒星分类账中的条目。他们跟踪您的帐户信任发行帐户的限额以及您帐户当前持有的发行帐户中的信用额度。

从协议版本10开始，每条信任线还跟踪相应帐户和资产的负债。购买负债等于此帐户拥有的所有要约中要出售的资产总额，而销售负债等于此帐户拥有的所有要约中要出售的资产总额。信任线必须始终具有足够大的余额来满足其销售负债，并且余额必须足够低于其限额以容纳其购买负债。

## 流明（XLM） {#lumens-xlm}

**流明（XLM）**是网络的本机货币。流明是可以在Stellar网络上使用的唯一资产类型，不需要发行人或信托线。任何帐户都可以容纳流明。您可以将流明用于网络中其他资产的交易。

## 锚点：发行资产 {#anchors-issuing-assets}

任何帐户都可以在Stellar网络上发行资产。发行资产的实体称为**锚。**锚可以由个人，小型企业，当地社区，非营利组织，组织等运营。任何类型的金融机构（银行，付款处理方）都可以作为锚。

每个锚都有一个从中**发行**资产的**发行帐户**。

作为锚点，发行资产时，请为其提供**资产代码**。资产由资产代码和发行人唯一标识。最终，由发行人来设置资产代码。但是，按照惯例，货币应使用适当的[ISO 4217代码表示](https://en.wikipedia.org/wiki/ISO_4217)。对于股票和债券，请使用适当的[ISIN号](https://en.wikipedia.org/wiki/International_Securities_Identification_Number)。对于您的橘子，山羊，青睐或啤酒主角，您自己动手-编写适当的代码！

当前，资产代码支持两种格式。

#### 最多4个字母数字的字符 {#alphanumeric-4-character-maximum}

允许使用\[az\] \[AZ\] \[0-9\]中的任何字符。该代码可以少于4个字符，但是结尾字符必须全部为空。

#### 字母数字（最多12个字符） {#alphanumeric-12-character-maximum}

允许使用\[az\] \[AZ\] \[0-9\]中的任何字符。该代码可以是5到12之间的任意数量的字符，但是结尾字符必须全部为空。

### 控制资产持有人 {#controlling-asset-holders}

默认情况下，任何人都可以与资产发行人创建信任线以接受资产。但是，作为锚，您可以通过在发行帐户中启用以下标志来**显式授权**和**撤消**用户对资产的访问权限（[在此处](https://www.stellar.org/developers/guides/concepts/accounts.html#flags)了解更多[信息](https://www.stellar.org/developers/guides/concepts/accounts.html#flags)）。

* `AUTHORIZATION REQUIRED`
  ：使用此设置，主播必须批准任何想要持有其资产的人，从而允许其控制谁是客户。
  锚点通过
  使用
  [Allow Trust](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#allow-trust)
  操作
  `Authorize`
  将现有信任线
  的
  标志
  设置
  为
  **true**
  来完成批准
  。
* `AUTHORIZATION REVOCABLE`
  ：使用此设置，锚可以
  通过“
  [允许信任”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#allow-trust)
  操作
  `Authorize`
  将现有信任行的标志
  设置
  为
  ，以冻结另一个帐户持有的资产。
  冻结某个特定帐户的资产后，该帐户将无法将该资产转移到任何其他帐户，甚至不能转移回锚点。
  此设置允许发行帐户撤销其意外发行或不正确获取的资产。
  要使用此设置，
  还必须启用。
  `false`

  `AUTHORIZATION REQUIRED`

**具有`AUTHORIZATION REQUIRED`和已`AUTHORIZATION REVOCABLE`启用的帐户的示例流程：**

1. 用户决定他/她想要接受资产
2. 用户使用此资产的发行帐户打开信任行
3. 发行方授权用户的信任线
4. 用户可以接受资产并将资产发送给与发行人建立信任关系的其他任何人
5. 发行人希望冻结用户对资产的访问
6. 发行人取消用户信任线的授权
7. 用户无法发送或接受此资产

**替代流程：**请注意，以后可以设置这些标志。也许您最初允许任何人打开信任线，但后来意识到这不是一个好主意。发行此资产后，您可以设置以上**两个**标志。此时，拥有开放信任线的每个人都将保留其授权状态，但是您现在可以撤消信任（假设您尚未调整主密钥权重和/或[帐户阈值](https://www.stellar.org/developers/guides/concepts/multi-sig.html#thresholds)）。

**注意：**当锚发行资产时，他们通常希望限制流通令牌的供应。由于“[允许信任”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#allow-trust)操作`low threshold`处于“[设置选项”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#set-options)和“[付款”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#payment)操作的状态，因此仍然有可能创建这种有限的供应并保持授权和撤消的能力`high/medium threshold`。要了解有关创建资产和限制令牌供应的更多信息，[请在此处阅读](https://www.stellar.org/developers/guides/walkthroughs/custom-assets.html#optional-transaction-a-limit-token-supply)。

**确保资产持有人不会被撤消**：上述功能对于希望控制谁可以和不能持有/交易其资产的资产发行人来说非常有用。但是，如果我是资产持有人并且担心发行人会冻结我持有的资产怎么办？为了灌输对潜在资产持有者的信任，发行帐户可以启用以下标志：

* `AUTHORIZATION IMMUTABLE`
  ：使用此设置，不能设置任何授权标志，并且永远不能删除该帐户。

## 数量精度和表示 {#amount-precision-and-representation}

每个资产金额在[XDR结构中](https://www.stellar.org/developers/guides/concepts/xdr.html)被编码为带符号的64位整数。将资产金额单位（最终用户看到的资产单位）缩小一千万（10,000,000）倍，以得出本机的64位整数表示形式。例如，整数值`25,123,456`等于`2.5123456`资产的单位。此缩放比例允许以人类友好的数量单位表示的**七个小数位**精度。

最小的非零量单位是`0.0000001`整数值1表示的（十分之一）。可能的最大数量单位是`((2^63)-1)/(10^7)`（根据缩小的最大int64得出）`922,337,203,685.4775807`。

数字表示为`int64`s。数量值仅存储为有符号整数，以避免由于混合有符号整数和无符号整数而引起的错误。

### Horizo​​n和Stellar客户端库中的相关性 {#relevance-in-horizon-and-stellar-client-libraries}

在Horizo​​n和客户端库（如）中`js-stellar-sdk`，整数编码值被抽象掉。许多API期望金额单位值（显示给最终用户的按比例增加的金额）。

### 使用“大量”库保持精度 {#maintaining-precision-with-big-number-libraries}

某些编程语言（例如JavaScript）在保持一定数量的精度方面存在问题。建议使用“大数”库，该库可以记录任意精度的十进制数而不会损失精度。

### 一支，多支 {#one-stroop-multiple-stroops}

“ Stroop”是最小的数量单位。它是百万分之一：`1/10000000`或`0.0000001`。术语stroop用作引用这些少量金额度量的便捷方式。复数形式是“ stroops”（例如“ 100 stroops”）。有趣的事实：该术语源自Stroopy，这是恒星吉祥物的名称，其名称源自[stroopwafels](https://en.wikipedia.org/wiki/Stroopwafel)资产

恒星分布式网络可用于跟踪，持有和转移任何类型的**资产**：美元，欧元，比特币，股票，黄金和其他价值代币。网络上的任何资产都可以与其他任何资产进行交易和交换。

除流明（见下文）外，所有资产均具有

* **资产类型**
  ：例如，美元或比特币
* **发行人**
  ：创建资产的帐户

## 信任线 {#trustlines}

当您在Stellar中持有资产时，实际上就是从特定发行人那里获得信贷。发行人已同意将您在Stellar网络上的信用额度用于交易Stellar以外的相应资产（例如法定货币，贵金属）。假设斯科特（Scott）在网络上发放橙子作为信用。如果您持有橙色信用，则您和Scott会基于信任或信任线达成协议：你们都同意，当您向Scott授予橙色信用时，他会给您橙色。

持有资产时，您必须信任发行人正确赎回其信用额。由于Stellar的用户将不希望仅信任任何发行人，因此帐户必须明确信任发行帐户，然后才能持有发行人的信用。在上面的示例中，您必须明确信任Scott才能持有橙色积分。

要信任发行帐户，请创建一条**信任线。**信任线是保留在恒星分类账中的条目。他们跟踪您的帐户信任发行帐户的限额以及您帐户当前持有的发行帐户中的信用额度。

从协议版本10开始，每条信任线还跟踪相应帐户和资产的负债。购买负债等于此帐户拥有的所有要约中要出售的资产总额，而销售负债等于此帐户拥有的所有要约中要出售的资产总额。信任线必须始终具有足够大的余额来满足其销售负债，并且余额必须足够低于其限额以容纳其购买负债。

## 流明（XLM） {#lumens-xlm}

**流明（XLM）**是网络的本机货币。流明是可以在Stellar网络上使用的唯一资产类型，不需要发行人或信托线。任何帐户都可以容纳流明。您可以将流明用于网络中其他资产的交易。

## 锚点：发行资产 {#anchors-issuing-assets}

任何帐户都可以在Stellar网络上发行资产。发行资产的实体称为**锚。**锚可以由个人，小型企业，当地社区，非营利组织，组织等运营。任何类型的金融机构（银行，付款处理方）都可以作为锚。

每个锚都有一个从中**发行**资产的**发行帐户**。

作为锚点，发行资产时，请为其提供**资产代码**。资产由资产代码和发行人唯一标识。最终，由发行人来设置资产代码。但是，按照惯例，货币应使用适当的[ISO 4217代码表示](https://en.wikipedia.org/wiki/ISO_4217)。对于股票和债券，请使用适当的[ISIN号](https://en.wikipedia.org/wiki/International_Securities_Identification_Number)。对于您的橘子，山羊，青睐或啤酒主角，您自己动手-编写适当的代码！

当前，资产代码支持两种格式。

#### 最多4个字母数字的字符 {#alphanumeric-4-character-maximum}

允许使用\[az\] \[AZ\] \[0-9\]中的任何字符。该代码可以少于4个字符，但是结尾字符必须全部为空。

#### 字母数字（最多12个字符） {#alphanumeric-12-character-maximum}

允许使用\[az\] \[AZ\] \[0-9\]中的任何字符。该代码可以是5到12之间的任意数量的字符，但是结尾字符必须全部为空。

### 控制资产持有人 {#controlling-asset-holders}

默认情况下，任何人都可以与资产发行人创建信任线以接受资产。但是，作为锚，您可以通过在发行帐户中启用以下标志来**显式授权**和**撤消**用户对资产的访问权限（[在此处](https://www.stellar.org/developers/guides/concepts/accounts.html#flags)了解更多[信息](https://www.stellar.org/developers/guides/concepts/accounts.html#flags)）。

* `AUTHORIZATION REQUIRED`
  ：使用此设置，主播必须批准任何想要持有其资产的人，从而允许其控制谁是客户。
  锚点通过
  使用
  [Allow Trust](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#allow-trust)
  操作
  `Authorize`
  将现有信任线
  的
  标志
  设置
  为
  **true**
  来完成批准
  。
* `AUTHORIZATION REVOCABLE`
  ：使用此设置，锚可以
  通过“
  [允许信任”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#allow-trust)
  操作
  `Authorize`
  将现有信任行的标志
  设置
  为
  ，以冻结另一个帐户持有的资产。
  冻结某个特定帐户的资产后，该帐户将无法将该资产转移到任何其他帐户，甚至不能转移回锚点。
  此设置允许发行帐户撤销其意外发行或不正确获取的资产。
  要使用此设置，
  还必须启用。
  `false`

  `AUTHORIZATION REQUIRED`

**具有`AUTHORIZATION REQUIRED`和已`AUTHORIZATION REVOCABLE`启用的帐户的示例流程：**

1. 用户决定他/她想要接受资产
2. 用户使用此资产的发行帐户打开信任行
3. 发行方授权用户的信任线
4. 用户可以接受资产并将资产发送给与发行人建立信任关系的其他任何人
5. 发行人希望冻结用户对资产的访问
6. 发行人取消用户信任线的授权
7. 用户无法发送或接受此资产

**替代流程：**请注意，以后可以设置这些标志。也许您最初允许任何人打开信任线，但后来意识到这不是一个好主意。发行此资产后，您可以设置以上**两个**标志。此时，拥有开放信任线的每个人都将保留其授权状态，但是您现在可以撤消信任（假设您尚未调整主密钥权重和/或[帐户阈值](https://www.stellar.org/developers/guides/concepts/multi-sig.html#thresholds)）。

**注意：**当锚发行资产时，他们通常希望限制流通令牌的供应。由于“[允许信任”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#allow-trust)操作`low threshold`处于“[设置选项”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#set-options)和“[付款”](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#payment)操作的状态，因此仍然有可能创建这种有限的供应并保持授权和撤消的能力`high/medium threshold`。要了解有关创建资产和限制令牌供应的更多信息，[请在此处阅读](https://www.stellar.org/developers/guides/walkthroughs/custom-assets.html#optional-transaction-a-limit-token-supply)。

**确保资产持有人不会被撤消**：上述功能对于希望控制谁可以和不能持有/交易其资产的资产发行人来说非常有用。但是，如果我是资产持有人并且担心发行人会冻结我持有的资产怎么办？为了灌输对潜在资产持有者的信任，发行帐户可以启用以下标志：

* `AUTHORIZATION IMMUTABLE`
  ：使用此设置，不能设置任何授权标志，并且永远不能删除该帐户。

## 数量精度和表示 {#amount-precision-and-representation}

每个资产金额在[XDR结构中](https://www.stellar.org/developers/guides/concepts/xdr.html)被编码为带符号的64位整数。将资产金额单位（最终用户看到的资产单位）缩小一千万（10,000,000）倍，以得出本机的64位整数表示形式。例如，整数值`25,123,456`等于`2.5123456`资产的单位。此缩放比例允许以人类友好的数量单位表示的**七个小数位**精度。

最小的非零量单位是`0.0000001`整数值1表示的（十分之一）。可能的最大数量单位是`((2^63)-1)/(10^7)`（根据缩小的最大int64得出）`922,337,203,685.4775807`。

数字表示为`int64`s。数量值仅存储为有符号整数，以避免由于混合有符号整数和无符号整数而引起的错误。

### Horizo​​n和Stellar客户端库中的相关性 {#relevance-in-horizon-and-stellar-client-libraries}

在Horizo​​n和客户端库（如）中`js-stellar-sdk`，整数编码值被抽象掉。许多API期望金额单位值（显示给最终用户的按比例增加的金额）。

### 使用“大量”库保持精度 {#maintaining-precision-with-big-number-libraries}

某些编程语言（例如JavaScript）在保持一定数量的精度方面存在问题。建议使用“大数”库，该库可以记录任意精度的十进制数而不会损失精度。

### 一支，多支 {#one-stroop-multiple-stroops}

“ Stroop”是最小的数量单位。它是百万分之一：`1/10000000`或`0.0000001`。术语stroop用作引用这些少量金额度量的便捷方式。复数形式是“ stroops”（例如“ 100 stroops”）。有趣的事实：该术语源自Stroopy，这是恒星吉祥物的名称，其名称源自[stroopwafels](https://en.wikipedia.org/wiki/Stroopwafel)

