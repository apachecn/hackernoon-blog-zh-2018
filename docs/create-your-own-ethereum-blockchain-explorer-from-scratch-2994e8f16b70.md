# 从头开始创建自己的以太坊区块链浏览器

> 原文：<https://medium.com/hackernoon/create-your-own-ethereum-blockchain-explorer-from-scratch-2994e8f16b70>

区块链的美丽在于它的区块链探索者。如果你真的拥有 Ethers(以太坊的加密货币)，你就会知道 [etherscan.io](https://etherscan.io) 的重要性

![](img/d1855f87b095876743ffc64af74fc819.png)

Source: [https://etherscan.io/txs](https://etherscan.io/txs)

如果你的组织在私人以太坊[网络](https://hackernoon.com/tagged/network)上运行，那么[区块链](https://hackernoon.com/tagged/blockchain)探索者将是一种享受。但是很少有人会陷入从哪里开始的困境。

在本教程中，我们将使用 Infura 连接到以太坊的主网络。如果你掌握了这篇文章的精髓，那么你也可以为以太坊私人区块链开发一个区块链探索者。

# 我将使用的技术堆栈:

*   [Web3.js](https://web3js.readthedocs.io/en/1.0/index.html) (以太坊的 JavaScript API)
*   [Infura](https://infura.io/) 键
*   HTML(你也可以在前端使用 React/Angular)

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Blockchain Explorer</title>
    <!-- Bootstrap -->
    <link rel="stylesheet" href="[https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css)" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <!-- Styles -->
    <style>
      body {
        padding-top: 20px;
      }
      h1 {
        margin: 20px 0px;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="row">
        <div class="col-lg-12 text-center" >
          <img src="img/picture.png">
          <h1>Blockchain Explorer</h1>
          <h3> Latest 20 blocks </h3>
          <table class="table">
            <thead>
              <tr>
                <th scope="col">TxHash</th>
                <th scope="col">Block</th>
                <th scope="col">Timestamp</th>
                <th scope="col">Gas Used</th>
              </tr>
            </thead>
            <tbody>
            </tbody>
          </table>
        </div>
      </div>
    </div><!-- Bootstrap -->
    <script src="[https://code.jquery.com/jquery-3.2.1.slim.min.js](https://code.jquery.com/jquery-3.2.1.slim.min.js)" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="[https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js](https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js)" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="[https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js](https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js)" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script><!-- Web3 -->
    <script src="[https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js](https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js)"></script> <script>
      var provider = '[https://mainnet.infura.io/v3/'](https://mainnet.infura.io/v3/32d7425cd6b741f8a54653b6aa4c6b3c'); //Your Infura Endpoint
      var web3Provider = new Web3.providers.HttpProvider(provider);
      var web3 = new Web3(web3Provider);var latestBlock = web3.eth.blockNumber;// List blocks in table
      for (var i = 0; i < 20; i++) {
        var block = web3.eth.getBlock(latestBlock - i);
        var number = block.number;
        var hash = block.hash;
        var time = block.timestamp;
        var gas = block.gasUsed;
        //var sender = web3.eth.getTransaction(hash.from);
        convertTimestamp(time);function convertTimestamp(time) {
            var d = new Date(time * 1000), // Convert the passed timestamp to milliseconds
                yyyy = d.getFullYear(),
                mm = ('0' + (d.getMonth() + 1)).slice(-2),  // Months are zero based. Add leading 0.
                dd = ('0' + d.getDate()).slice(-2),         // Add leading 0.
                hh = d.getHours(),
                h = hh,
                min = ('0' + d.getMinutes()).slice(-2),     // Add leading 0.
                ampm = 'AM',
                time;if (hh > 12) {
                h = hh - 12;
                ampm = 'PM';
            } else if (hh === 12) {
                h = 12;
                ampm = 'PM';
            } else if (hh == 0) {
                h = 12;
            }// ie: 2014-03-24, 3:00 PM
            time1 = yyyy + '-' + mm + '-' + dd + ', ' + h + ':' + min + ' ' + ampm;
            return time1;
        } $('tbody').append("<tr><td>" + hash + "</td><td>" + number + "</td><td>" + time1 + "</td><td>" + gas + "</tr>");
      } </script>
  </body>
</html>
```

如果要访问您的私人区块链，请将提供商 URL 替换为您的私人区块链元掩码 URL。

实际上，我们使用的是 Infura 密钥来访问主以太网。Web3 来查询网络。从 Web3 收到的 JSON 响应被放入 HTML。仅此而已。

![](img/ea47948f5f19a4895311a04a35a1215e.png)

This is what it looks like