

# 创建一个帐户

---

在我们开始使用Stellar编写代码之前，请考虑使用Stellar Laboratory通过以下示例。通过该实验室，您可以通过Endpoint Explorer创建帐户，在Stellar测试网络上为帐户注资，构建交易，运行任何操作以及检查Horizo​​n的响应。



您需要在Stellar网络上执行任何操作的第一件事就是帐户。账户将您所有的钱都保存在Stellar中，并允许您发送和接收付款-实际上，Stellar中的几乎所有内容都在某种程度上与账户相关联。



每个Stellar帐户都有一个公共密钥和一个秘密种子。Stellar使用公钥加密技术来确保每笔交易都是安全的。公用密钥始终是安全共享的，其他人需要公用密钥来识别您的帐户并验证您是否授权交易。但是，种子是可以证明您拥有帐户的私人信息。您永远不应与任何人分享种子。这有点像一把锁的密码锁，任何知道密码锁的人都可以打开锁。同样，任何知道您帐户种子的人都可以控制您的帐户。



如果您熟悉公钥加密，您可能想知道种子与私钥有何不同。种子实际上是单个秘密数据，用于为您的帐户生成公共密钥和私有密钥。为了方便起见，Stellar的工具使用种子而不是私钥：要完全访问帐户，您只需提供种子即可，而不是提供公钥和私钥。\[1\]



因为必须将种子保密，所以创建帐户的第一步是创建自己的种子和密钥-最终创建帐户时，您只会将公钥发送到Stellar服务器。您可以使用以下命令生成种子和密钥：



产生金钥的JavaScript爪哇走蟒蛇

// create a completely new and unique pair of keys

// see more about KeyPair objects: https://stellar.github.io/js-stellar-sdk/Keypair.html

const pair = StellarSdk.Keypair.random\(\);



pair.secret\(\);

// SAV76USXIJOBMEQXPANUOQM6F5LIOTLPDIDVRJBFFE2MDJXG24TAPUU7

pair.publicKey\(\);

// GCFXHS4GXL6BVUCXBWXGTITROWLVYXQKQLF4YH5O5JT3YZXCYPAFBJZB

现在您有了种子和公钥，您可以创建一个帐户了。为了防止人们制作大量不必要的帐户，每个帐户的最低余额必须为1流明（流明是Stellar网络的内置货币）。\[2\]不过，由于您还没有任何流明，所以您无法为一个帐户付费。在现实世界中，您通常会支付一笔销售流明的交易，以创建一个新帐户。\[3\] \[4\]但是，在Stellar的测试网络上，您可以要求Friendbot（我们的友好机器人，钱包很胖）为您创建一个帐户。



要创建测试帐户，请向Friendbot发送您创建的公共密钥。它将使用该公钥作为帐户ID创建新帐户并为其提供资金。



创建测试帐户的JavaScript爪哇走蟒蛇

// The SDK does not have tools for creating test accounts, so you'll have to

// make your own HTTP request.



// if you're trying this on Node, install the \`node-fetch\` library and

// uncomment the next line:

// const fetch = require\('node-fetch'\);



\(async function main\(\) {

  try {

    const response = await fetch\(

      \`https://friendbot.stellar.org?addr=${encodeURIComponent\(pair.publicKey\(\)\)}\`

    \);

    const responseJSON = await response.json\(\);

    console.log\("SUCCESS! You have a new account :\)\n", responseJSON\);

  } catch \(e\) {

    console.error\("ERROR!", e\);

  }

}\)\(\)

现在进入最后一步：获取帐户的详细信息并检查其余额。帐户可以带有多个余额-每种所持有的货币都可以余额一个。



获取帐户详细信息的JavaScript爪哇走蟒蛇

const server = new StellarSdk.Server\("https://horizon-testnet.stellar.org"\);



// the JS SDK uses promises for most actions, such as retrieving an account

const account = await server.loadAccount\(pair.publicKey\(\)\);

console.log\("Balances for account: " + pair.publicKey\(\)\);

account.balances.forEach\(function\(balance\) {

  console.log\("Type:", balance.asset\_type, ", Balance:", balance.balance\);

}\);

有了帐户后，您就可以开始发送和接收付款了。



上一篇：Stellar Network概述下一篇：汇款和收款

私钥仍用于加密数据和签署交易。KeyPair使用种子创建对象时，私钥立即生成并存储在内部。↩︎



Stellar的其他功能（如信任额度）要求更高的最低余额。有关最低余额的更多信息，请参阅费用 ↩︎



CoinMarketCap在维持交易所出售流明的列表http://coinmarketcap.com/currencies/stellar/\#markets ↩︎



一旦拥有流明余额的帐户，您还可以通过“ 创建帐户”操作创建其他帐户，该操作将流明从现有帐户转移到新帐户。这是在公共网络上创建新帐户的方式，通常在交易所是源帐户，而您的帐户是由它们创建的。要自己执行此操作，请遵循使用“ 创建帐户”操作而不是“ 付款”操作来构建交易的步骤。

