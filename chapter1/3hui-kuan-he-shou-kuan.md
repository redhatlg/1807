# 汇款和收款

---

_在继续编写代码中有关Stellar的以下示例之前，请考虑使用_[_Stellar Laboratory_](https://www.stellar.org/laboratory/)_完成以下示例。通过该实验室，您可以通过Endpoint Explorer创建帐户，在测试网上为帐户提供资金，建立交易，运行任何操作以及检查Horizo​​n的响应。_

现在您已经有一个帐户，您可以通过Stellar网络发送和接收资金。如果您尚未创建帐户，请阅读[《入门指南》的第2步](https://www.stellar.org/developers/guides/get-started/create-account.html)。

大多数情况下，您会将钱汇给拥有自己帐户的其他人。但是，对于本交互式指南，您应该使用与创建第一个帐户相同的方法创建第二个帐户进行交易。

## 发送付款 {#send-payments}

更改恒星币中的事物的操作（例如，发送付款，更改帐户或提供报价以交易各种货币）被称为**操作。**[\[1\]](https://www.stellar.org/developers/guides/get-started/transactions.html#fn1)为了实际执行操作，您需要创建一个**交易**，该**交易**只是一组操作，并附带一些额外的信息，例如进行交易的帐户以及验证交易是否真实的密码签名。[\[2\]](https://www.stellar.org/developers/guides/get-started/transactions.html#fn2)

如果事务中的任何操作失败，那么它们都会全部失败。例如，假设您有100流明，并且您进行了两个分别为60流明的支付操作。如果进行两次事务处理（每个事务执行一个操作），则第一个将成功，而第二个将失败，因为您没有足够的流明。您将剩下40流明。但是，如果您将这两种付款归为同一笔交易，它们都会失败，并且您的帐户中仍将剩下全部100流明。

最后，每笔交易都需要支付少量费用。像帐户中的最低余额一样，这笔费用有助于防止人们因大量交易而使系统过载。被称为**基本费用**，它很小，每次操作100步（即0.00001 XLM；比流明的微小部分更容易谈论步步）。一次有两个操作的交易将花费200步。[\[3\]](https://www.stellar.org/developers/guides/get-started/transactions.html#fn3)

### 建立交易 {#building-a-transaction}

恒星币以称为XDR的二进制格式存储和交流交易数据。[\[4\]](https://www.stellar.org/developers/guides/get-started/transactions.html#fn4)幸运的是，Stellar SDK提供了处理所有这些问题的工具。您可以通过以下方法将10流明发送到另一个帐户：

提交交易

的JavaScript

爪哇

走

蟒蛇

```
var
StellarSdk
=
require
(
'stellar-sdk'
);

var
server
=
new
StellarSdk
.
Server
(
'https://horizon-testnet.stellar.org'
);

var
sourceKeys
=
StellarSdk
.
Keypair

  .
fromSecret
(
'SCZANGBA5YHTNYVVV4C3U252E2B6P6F5T3U6MM63WBSBZATAQI3EBTQ4'
);

var
destinationId
=
'GA2C5RFPE6GCKMY3US5PAB6UZLKIGSPIUKSLRB6Q723BM2OARMDUYEJ5'
;

// Transaction will hold a built transaction we can resubmit if the result is unknown.
var
transaction
;


// First, check to make sure that the destination account exists.
// You could skip this, but if the account does not exist, you will be charged
// the transaction fee when the transaction fails.
server
.
loadAccount
(
destinationId
)

// If the account is not found, surface a nicer error message for logging.

  .
catch
(
function
 (
error
) {

if
 (
error
instanceof
StellarSdk
.
NotFoundError
) {

throw
new
Error
(
'The destination account does not exist!'
);
    } 
else
return
error

  })

// If there was no error, load up-to-date information on your account.

  .
then
(
function
() {

return
server
.
loadAccount
(
sourceKeys
.
publicKey
());
  })
  .
then
(
function
(
sourceAccount
) {

// Start building the transaction.
transaction
=
new
StellarSdk
.
TransactionBuilder
(
sourceAccount
, {

fee
: 
StellarSdk
.
BASE_FEE
,

networkPassphrase
: 
StellarSdk
.
Networks
.
TESTNET

    })
      .
addOperation
(
StellarSdk
.
Operation
.
payment
({

destination
: 
destinationId
,

// Because Stellar allows transaction in many currencies, you must
// specify the asset type. The special "native" asset represents Lumens.
asset
: 
StellarSdk
.
Asset
.
native
(),

amount
: 
"10"

      }))

// A memo allows you to add your own metadata to a transaction. It's
// optional and does not affect how Stellar treats the transaction.

      .
addMemo
(
StellarSdk
.
Memo
.
text
(
'Test Transaction'
))

// Wait a maximum of three minutes for the transaction

      .
setTimeout
(
180
)
      .
build
();

// Sign the transaction to prove you are actually the person sending it.
transaction
.
sign
(
sourceKeys
);

// And finally, send it off to Stellar!
return
server
.
submitTransaction
(
transaction
);
  })
  .
then
(
function
(
result
) {

console
.
log
(
'Success! Results:'
, 
result
);
  })
  .
catch
(
function
(
error
) {

console
.
error
(
'Something went wrong!'
, 
error
);

// If the result is unknown (no response body, timeout etc.) we simply resubmit
// already built transaction:
// server.submitTransaction(transaction);

  });
```

到底发生了什么事？让我们分解一下。

1. 通过从Stellar网络加载关联的帐户数据，确认要发送到的帐户ID确实存在。如果您跳过此步骤，一切实际上都可以，但是这样做可以让您有机会避免进行您知道会失败的交易。您也可以使用此调用对目标帐户执行任何其他可能要执行的验证。例如，如果您正在编写银行软件，那么这是插入法规遵从性检查和KYC验证的好地方。

   加载账户  
   的JavaScript  
   爪哇  
   走  
   蟒蛇

   ```
   server
   .
   loadAccount
   (
   destinationId
   )
     .
   then
   (
   function
   (
   account
   ) { 
   /* validate the account */
    })
   ```

2. 加载您要发送的帐户的数据。一个帐户一次只能执行一项交易[\[5\]，](https://www.stellar.org/developers/guides/get-started/transactions.html#fn5)并且有一个称为[**序号的**](https://www.stellar.org/developers/guides/concepts/accounts.html#sequence-number)东西[，](https://www.stellar.org/developers/guides/concepts/accounts.html#sequence-number)这可以帮助Stellar验证交易的顺序。交易的序列号需要与帐户的序列号匹配，因此您需要从网络中获取帐户的当前序列号。

   载入来源帐户  
   的JavaScript  
   爪哇  
   蟒蛇

   ```
   .
   then
   (
   function
   () {

   return
   server
   .
   loadAccount
   (
   sourceKeys
   .
   publicKey
   ());
   })
   ```

   建立交易时，SDK会自动增加帐户的序列号，因此，如果您要执行第二笔交易，则无需再次检索此信息。

3. 开始建立交易。这需要一个帐户对象，而不仅仅是一个帐户ID，因为它将增加帐户的序列号。

   建立交易  
   的JavaScript  
   爪哇  
   走  
   蟒蛇

   ```
   var
   transaction
   =
   new
   StellarSdk
   .
   TransactionBuilder
   (
   sourceAccount
   )
   ```

4. 将支付操作添加到该帐户。请注意，您需要指定要发送的资产的类型-Stellar的“本机”货币是流明，但是您可以发送任何类型的资产或货币，从美元到比特币到您信任发行人的任何资产兑现[（下面有更多详细信息）](https://www.stellar.org/developers/guides/get-started/transactions.html#transacting-in-other-currencies)。不过，到目前为止，我们将坚持使用流明，在SDK中将其称为“本机”资产：

   添加操作  
   的JavaScript  
   爪哇  
   走  
   蟒蛇

   ```
   .
   addOperation
   (
   StellarSdk
   .
   Operation
   .
   payment
   ({

   destination
   : 
   destinationId
   ,

   asset
   : 
   StellarSdk
   .
   Asset
   .
   native
   (),

   amount
   : 
   "10"

   }))
   ```

   您还应该注意，金额是字符串而不是数字。当使用极小的分数或较大的值时，[浮点数学运算可能会引入较小的误差](https://en.wikipedia.org/wiki/Floating_point#Accuracy_problems)。由于并非所有系统都具有一种本机方式来精确表示极小或很大的小数，因此Stellar使用字符串作为一种可靠的方式来表示任何系统中的确切数量。

5. （可选）您可以将自己的元数据（称为[**备忘录）添加**](https://www.stellar.org/developers/guides/concepts/transactions.html#memo)到事务中。Stellar不会对此数据做任何事情，但是您可以将其用于任何您想要的目的。例如，如果您是一家代表他人接收或发送付款的银行，则您可能会在此处包括付款的实际人员。

   添加备忘录  
   的JavaScript  
   爪哇  
   走  
   蟒蛇

   ```
   .
   addMemo
   (
   StellarSdk
   .
   Memo
   .
   text
   (
   'Test Transaction'
   ))
   ```

6. 现在，事务已拥有所需的所有数据，您必须使用秘密种子对它进行加密签名。这证明数据实际上来自您，而不是冒充您的人。

   签署交易  
   的JavaScript  
   爪哇  
   走  
   蟒蛇

   ```
   transaction
   .
   sign
   (
   sourceKeys
   );
   ```

7. 最后，将其发送到Stellar网络！

   提交交易  
   的JavaScript  
   爪哇  
   走  
   蟒蛇

   ```
   server
   .
   submitTransaction
   (
   transaction
   );
   ```

**重要信息**由于错误，网络状况等，您可能不会收到Horizo​​n服务器的响应。在这种情况下，无法确定交易状态。这就是为什么您应该始终将已构建的事务（或以XDR格式编码的事务）保存在变量或数据库中，如果不知道其状态，则重新提交它的原因。如果交易已成功应用于分类帐，Horizo​​n将仅返回保存的结果，而不会尝试再次提交交易。仅在交易状态未知（因此有机会被包含在分类帐中）的情况下，才会重新提交到网络。

## 收到付款 {#receive-payments}

实际上，您无需执行任何操作即可接收到Stellar帐户中的付款-如果付款人成功进行了交易以将资产发送给您，则这些资产将自动添加到您的帐户中。

但是，您会想知道某人实际上已经向您付款。如果您是一家代表他人接受付款的银行，则需要找出发送给您的款项，以便可以将资金分配给预期的接收者。如果您经营的是零售企业，则需要知道您的客户在向他们支付商品之前实际上已经向您付款。而且，如果您是拥有Stellar帐户的自动租赁汽车，您可能需要在该人打开引擎之前先核实您前排座位上的客户是否实际付款。

一个监视网络中付款并打印每个付款的简单程序可能类似于：

收到付款

的JavaScript

爪哇

走

蟒蛇

    var
    StellarSdk
    =
    require
    (
    'stellar-sdk'
    );


    var
    server
    =
    new
    StellarSdk
    .
    Server
    (
    'https://horizon-testnet.stellar.org'
    );

    var
    accountId
    =
    'GC2BKLYOOYPDEFJKLKY6FNNRQMGFLVHJKQRGNSSRRGSMPGF32LHCQVGF'
    ;


    // Create an API call to query payments involving the account.
    var
    payments
    =
    server
    .
    payments
    ().
    forAccount
    (
    accountId
    );


    // If some payments have already been handled, start the results from the
    // last seen payment. (See below in `handlePayment` where it gets saved.)
    var
    lastToken
    =
    loadLastPagingToken
    ();

    if
     (
    lastToken
    ) {

    payments
    .
    cursor
    (
    lastToken
    );
    }


    // `stream` will send each recorded payment, one by one, then keep the
    // connection open and continue to send you new payments as they occur.
    payments
    .
    stream
    ({

    onmessage
    : 
    function
    (
    payment
    ) {

    // Record the paging token so we can start from here next time.
    savePagingToken
    (
    payment
    .
    paging_token
    );


    // The payments stream includes both sent and received payments. We only
    // want to process received payments here.
    if
     (
    payment
    .
    to
    !==
    accountId
    ) {

    return
    ;
        }


    // In Stellar’s API, Lumens are referred to as the “native” type. Other
    // asset types have more detailed information.
    var
    asset
    ;

    if
     (
    payment
    .
    asset_type
    ===
    'native'
    ) {

    asset
    =
    'lumens'
    ;
        }

    else
     {

    asset
    =
    payment
    .
    asset_code
    +
    ':'
    +
    payment
    .
    asset_issuer
    ;
        }


    console
    .
    log
    (
    payment
    .
    amount
    +
    ' '
    +
    asset
    +
    ' from '
    +
    payment
    .
    from
    );
      },


    onerror
    : 
    function
    (
    error
    ) {

    console
    .
    error
    (
    'Error in payment stream'
    );
      }
    });


    function
    savePagingToken
    (
    token
    ) {

    // In most cases, you should save this to a local database or file so that
    // you can load it next time you stream new payments.

    }


    function
    loadLastPagingToken
    () {

    // Get the last paging token from a local database or file

    }

该程序有两个主要部分。首先，创建一个查询，查询涉及给定帐户的付款。像Stellar中的大多数查询一样，这可能返回大量项目，因此API返回分页令牌，您可以稍后使用该令牌从先前中断的同一点开始查询。在上面的示例中，用于保存和加载分页令牌的功能留为空白，但是在实际的应用程序中，您希望将分页令牌保存到文件或数据库中，这样您就可以在程序停下来的地方继续操作崩溃或用户关闭它。

创建付款查询

的JavaScript

爪哇

蟒蛇

```
var
payments
=
server
.
payments
().
forAccount
(
accountId
);

var
lastToken
=
loadLastPagingToken
();

if
 (
lastToken
) {

payments
.
cursor
(
lastToken
);
}
```

第二，查询结果被流式传输。这是监视付款或其他交易的最简单方法。现有的每笔付款都将通过流逐一发送。发送完所有现有付款后，数据流将保持打开状态，并在进行新付款时将其发送出去。

试用：运行该程序，然后在另一个窗口中创建并提交付款。您应该看到该程序记录了付款。

流支付

的JavaScript

爪哇

蟒蛇

```
payments
.
stream
({

onmessage
: 
function
(
payment
) {

// handle a payment

  }
});
```

您也可以按组或页面请求付款。处理完每一页付款后，您需要请求下一页，直到没有剩余的为止。

分页付款

的JavaScript

爪哇

蟒蛇

```
payments
.
call
().
then
(
function
handlePage
(
paymentsPage
) {

paymentsPage
.
records
.
forEach
(
function
(
payment
) {

// handle a payment

  });

return
paymentsPage
.
next
().
then
(
handlePage
);
});
```

## 其他货币交易 {#transacting-in-other-currencies}

关于Stellar网络的神奇之处之一是，您可以发送和接收多种类型的资产，例如美元，尼日利亚奈拉，比特币等数字货币，甚至您自己的新型资产。

尽管Stellar的本钱资产流明相当简单，但可以将所有其他资产视为特定帐户发行的信用。实际上，当您在Stellar网络上交易美元时，实际上并没有交易美元，而是_从特定帐户_交易美元_。_因此，上例中的资产同时具有`code`和`issuer`。该`issuer`是创建资产账户的ID。了解哪个帐户发行资产很重要-您需要相信，如果您想在Stellar网络上兑换美元以获取实际的美元票据，发行者将能够向您提供这些票据。因此，您通常只希望信任代表本国货币的资产的主要金融机构。

Stellar还支持将付款作为一种资产发送而将其作为另一种资产接收。您可以将尼日利亚奈拉发送给德国的朋友，并让他们获得欧元。内置的市场机制使人们可以进行多种货币交易，人们可以提出要约来买卖不同类型的资产。恒星会自动找到最佳的人进行货币兑换，以便您的奈拉兑换成欧元。该系统称为[分布式交换](https://www.stellar.org/developers/guides/concepts/exchange.html)。

您可以在[资产概述中](https://www.stellar.org/developers/guides/concepts/assets.html)了解有关资产详细信息的更多信息。

## 接下来是什么？ {#what-next}

现在，您可以使用Stellar的API发送和接收付款，您就可以编写各种出色的财务软件。试用API的其他部分，然后阅读更详细的主题：

* [安全](https://www.stellar.org/developers/guides/security.html)
* [联邦](https://www.stellar.org/developers/guides/concepts/federation.html)
* [合规](https://www.stellar.org/developers/guides/compliance-protocol.html)

[返回步骤2：创建帐户](https://www.stellar.org/developers/guides/get-started/create-account.html)

---

1. 在[操作页面](https://www.stellar.org/developers/guides/concepts/operations.html)上可以找到所有可能的操作的列表。[↩︎](https://www.stellar.org/developers/guides/get-started/transactions.html#fnref1)

2. 有关交易的完整详细信息，可以在[交易页面](https://www.stellar.org/developers/guides/concepts/transactions.html)上找到。[↩︎](https://www.stellar.org/developers/guides/get-started/transactions.html#fnref2)

3. 这100个部队称为Stellar的**基本费用**。基本费用可以更改，但是恒星币费用的更改不太可能每隔几年发生一次。您可以通过[检查最新分类帐的详细信息](https://www.stellar.org/developers/horizon/reference/endpoints/ledgers-single.html)来[查询](https://www.stellar.org/developers/horizon/reference/endpoints/ledgers-single.html)当前费用。[↩︎](https://www.stellar.org/developers/guides/get-started/transactions.html#fnref3)

4. 即使Horizo​​n REST API的大多数响应都使用JSON，但Stellar中的大多数数据实际上都以称为XDR或外部数据表示的格式存储。XDR比JSON更为紧凑，并且以可预测的方式存储数据，这使得签名和验证XDR编码的消息更加容易。您可以在[我们的XDR页面](https://www.stellar.org/developers/horizon/reference/xdr.html)上获得更多详细信息。[↩︎](https://www.stellar.org/developers/guides/get-started/transactions.html#fnref4)

5. 在需要在短时间内执行大量交易的情况下（例如，一家银行可能代表多个使用一个Stellar帐户的客户进行交易），您可以创建多个同时工作的Stellar帐户。在[频道指南中](https://www.stellar.org/developers/guides/channels.html)阅读有关此内容的更多信息。[↩︎](https://www.stellar.org/developers/guides/get-started/transactions.html#fnref5)



