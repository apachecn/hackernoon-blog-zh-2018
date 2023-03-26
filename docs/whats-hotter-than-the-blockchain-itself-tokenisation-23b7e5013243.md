# 有什么比区块链本身更火？令牌化？

> 原文：<https://medium.com/hackernoon/whats-hotter-than-the-blockchain-itself-tokenisation-23b7e5013243>

> 将引领 Web 3.0 革命的事实上的商业理念是符号化。

![](img/8b4b9c5fc62d5c3949b99a3e33cd67b6.png)

Source: [https://www.pexels.com/photo/gray-and-gold-steel-gears-159275/](https://www.pexels.com/photo/gray-and-gold-steel-gears-159275/)

就在世人以为[区块链](https://hackernoon.com/tagged/blockchain)是新的大东西的时候，上的**[**令牌化**](https://hackernoon.com/tagged/tokenisation) **的炸弹爆炸了。人们刚刚开始了解区块链，在这种背景下，一个新的范式转变已经开始酝酿。****

> ****我不骗你；区块链的象征化比香草区块链本身更热。****

**![](img/a04ddfdb75c0deabfc84fdbb7d8c4a62.png)**

**Source: [https://mashable.com/2013/07/09/excitement-gifs/](https://mashable.com/2013/07/09/excitement-gifs/)**

## **前瞻**

**令牌化背后的理论与区块链背后的理论不谋而合。**

> **区块链的精髓在于分散一切的思想:权力、金钱、价值。**

**如果有人告诉你，你可以拥有泰姬陵或月球或(极其牵强)土星的一部分，那该怎么办？这个想法听起来很激进，不是吗？**

**如果你碰巧拥有上述任何一个物体的一部分，那么现在的拥有者(政府，或者上帝……)的力量就会消失。事实上，谁拥有的份额越多，谁就越有发言权。**

**这将摧毁所有的企业帝国，普通人将会获益并繁荣。这将在宇宙中产生一种全新的经济——代币经济。这种经济不会受到政府的限制，也不会被官僚们的既定规则所控制，在陆地上不会有任何边界。国家和公民的概念将逐渐消失。所有由中间人设立的机构都会减少。一切都将是自主的。一切将合而为一。**

**话虽如此；一切都可以被令牌化——你的车、你的房子、你电脑上的免费存储空间等等。**

## **简单来说就是符号化**

**假设您拥有一个 2TB (1 TB = 1024 GB)存储容量的硬盘，但其中大约 70%的容量大部分时间都没有使用。因此，你的朋友告诉你，如果你向别人提供你未使用的存储空间，有一个应用程序可以让你赚钱。这意味着，人们可以使用你未使用的磁盘空间，作为回报，他们将向你付费。磁盘未使用的部分被分成更小的部分，这样每个部分都可以放在“存储市场”上供人们购买。这本质上就是记号化。**

**同样，任何东西(资产)都可以被分解成可分割的小块，然后按部分出售——本质上就是所谓的令牌化。**

# **怎么做？**

**有像以太坊、BigchainDB 这样的平台可以实现这一点。**

**在本文中，我将向您展示如何在 BigchainDB 上做到这一点。**

## **步骤 1:安装 BigchainDB 驱动程序**

```
npm i bigchaindb-driver
```

## **步骤 2:连接到 BigchainDB 节点**

```
**const** BigchainDB = require('bigchaindb-driver')

**const** API_PATH = 'https://test.bigchaindb.com/api/v1/'
**const** conn = **new** BigchainDB.Connection(API_PATH, {
    app_id: 'Get one from testnet.bigchaindb.com',
    app_key: 'Get one from testnet.bigchaindb.com'
})
```

**下面的代码片段演示了如何创建一个有 100，000 个相关令牌的可分割资产。**

```
**const** nTokens = 100000
**let** tokensLeft
**const** tokenCreator = **new** BigchainDB
.Ed25519Keypair(bip39.mnemonicToSeed('seedPhrase').slice(0,32))

**function** tokenLaunch() {
    *// Construct a transaction payload*
    **const** tx = BigchainDB.Transaction.makeCreateTransaction({
            token: 'TT (Tutorial Tokens)',
            number_tokens: nTokens
        },
        *// Metadata field, contains information about the transaction itself*
        *// (can be `null` if not needed)*
        {
            datetime: **new** Date().toString()
        },
        *// Output: Divisible asset, include nTokens as parameter*
        [BigchainDB.Transaction.makeOutput(BigchainDB.Transaction
          .makeEd25519Condition(tokenCreator.publicKey), nTokens.toString())],
        tokenCreator.publicKey
    )

    *// Sign the transaction with the private key of the token creator*
    **const** txSigned = BigchainDB.Transaction
      .signTransaction(tx, tokenCreator.privateKey)

    *// Send the transaction off to BigchainDB*
    conn.postTransactionCommit(txSigned)
        .then(res => {
            tokensLeft = nTokens
            document.body.innerHTML ='<h3>Transaction created</h3>';
            *// txSigned.id corresponds to the asset id of the tokens*
            document.body.innerHTML +=txSigned.id
        })
}
```

**现在代币已经铸造好了，你可以开始把它们分发给所有者了。代币可以转让给无限数量的参与者。**

**在本例中，您现在将进行一项转账交易，向名为 Bill 的新用户转账 500 个代币。为此，您首先需要创建一个新用户，然后进行转移。下面的代码显示了这一点。**

```
**const** amountToSend = 500

**const** newUser = **new** BigchainDB
    .Ed25519Keypair(bip39.mnemonicToSeed('newUserseedPhrase')
        .slice(0, 32))

**function** transferTokens() {
    *// User who will receive the 500 tokens*
    **const** newUser = **new** BigchainDB.Ed25519Keypair()

    *// Search outputs of the transactions belonging the token creator*
    *// False argument to retrieve unspent outputs*
    conn.listOutputs(tokenCreator.publicKey, 'false')
        .then((txs) => {
            *// Just one transaction available with outputs not being spent by*
            *// tokenCreator. Therefore, txs[0]*
            **return** conn.getTransaction(txs[0].transaction_id)
        })
        .then((txOutputs) => {
            *// Create transfer transaction*
            **const** createTranfer = BigchainDB.Transaction
                .makeTransferTransaction(
                    [{
                        tx: txOutputs,
                        output_index: 0
                    }],
                    *// Transaction output: Two outputs, because the whole input*
                    *// must be spent*
                    [BigchainDB.Transaction.makeOutput(
                            BigchainDB.Transaction
                            .makeEd25519Condition(tokenCreator.publicKey),
                            (tokensLeft - amountToSend).toString()),
                        BigchainDB.Transaction.makeOutput(
                            BigchainDB.Transaction
                            .makeEd25519Condition(newUser.publicKey),
                            amountToSend)
                    ],
                    *// Metadata (optional)*
                    {
                        transfer_to: 'bill',
                        tokens_left: tokensLeft
                    }
                )

            *// Sign the transaction with the tokenCreator key*
            **const** signedTransfer = BigchainDB.Transaction
                .signTransaction(createTranfer, tokenCreator.privateKey)

            **return** conn.postTransactionCommit(signedTransfer)
        })
        .then(res => {
            *// Update tokensLeft*
            tokensLeft -= amountToSend
            document.body.innerHTML += '<h3>Transfer transaction created</h3>'
            document.body.innerHTML += res.id
        })

}
```

**官方指南可以在这里找到[。](https://www.bigchaindb.com/developers/guide/tutorial-token-launch/)**

**一些值得阅读的资源:**

1.  **[https://blog . bigchaindb . com/token ize-the-enterprise-23d 51 bafb 536](https://blog.bigchaindb.com/tokenize-the-enterprise-23d51bafb536)**
2.  **[https://masterthecrypto . com/token ization-tokens-create-liquid-world/](https://masterthecrypto.com/tokenization-tokens-create-liquid-world/)**
3.  **[https://medium . com/prysmeconomics/the-economics-of-token ization-part-I-not-everything-can-or-should-to-token izing-ed 9b 32 f 5a 0 f 2](/prysmeconomics/the-economics-of-tokenization-part-i-not-everything-can-or-should-be-tokenized-ed9b32f5a0f2)**

**如果你还有什么要补充的，欢迎在下面的评论中提出来。我很想知道你的想法。非常感谢！**