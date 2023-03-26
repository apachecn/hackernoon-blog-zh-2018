# 黑客入侵:以太网上的蜜罐

> 原文：<https://medium.com/hackernoon/hacking-the-hackers-honeypots-on-ethereum-network-5baa35a13577>

![](img/f9f1a86d84ea2b3aeb36b4ac3916c263.png)

不仅好人对区块链和加密技术感到兴奋。坏人也被大量的金钱轮流吸引。

黑帽子试图利用他们的逆向工程和研究技能来寻找脆弱的智能合同。与常规软件中发现的漏洞相比，智能合约中的漏洞更具破坏性，可以为攻击者带来即时利润。从[平价 MultiSig 钱包](https://blog.zeppelin.solutions/on-the-parity-wallet-multisig-hack-405a8c12e8f7)被盗的 3000 万美元和被[第二次攻击](/chain-cloud-company-blog/parity-multisig-hack-again-b46771eaa838)冻结的另外 3 亿美元仅仅是赌注的两个例子。

他们中的一些人甚至试图利用他们同类的贪婪！输入蜜罐。

“蜜罐”是一种智能合约，旨在看起来像一个容易的目标，但事实上并非如此。对于一个不老练的攻击者来说，它们看起来很脆弱，但是如果他试图“破坏它”，他反而会输掉他的钱。**狩猎猎人。黑黑客！**

下面是我们团队最近发现的一个有趣的标本:[0 x4 ba 0d 338 a 7 c 41 cc 12778 e0a 2 fa 6 df 2361 e8d 8465](https://etherscan.io/address/0x4ba0d338a7c41cc12778e0a2fa6df2361e8d8465#code)。

```
pragma solidity ^0.4.10;contract Ownable {
    address public Owner = msg.sender;
    function isOwner() returns (bool) {
        if (Owner == msg.sender) return true; return false;
    }
}contract ICO_Hold is Ownable {
    mapping (address => uint) public deposits;
    uint public openDate;
    address public Owner;function() public payable { }

    function setup(uint _openDate) public {
        Owner = msg.sender;
        openDate = _openDate;
    }

    function deposit() public payable {
        if (msg.value >= 0.5 ether) {
            deposits[msg.sender] += msg.value;
        }
    }function withdraw(uint amount) public {
        if (isOwner() && now >= openDate) {
            uint max = deposits[msg.sender];
            if (amount <= max && max > 0) {
                msg.sender.transfer(amount);
            }
        }
    }
}
```

你能说出重点在哪里吗？

为了利用这个漏洞，攻击者必须先存入 0.5 ETH。接下来，他应该通过呼叫`setup()`来要求所有权，因为他不小心没有受到保护。之后，他可以通过多次调用`withdraw()` 来吞噬整个余额。注意提取的金额从来不会从记录的余额中扣除。

真正的诀窍是将`address public Owner`定义两次。一次在`Ownable`中，第二次在主合同中。这就是为什么在`withdraw()`方法中调用`setup()`不会影响`isOwner()`的原因。rush 中想成为黑客的人很容易错过的一句话。

# 智能合同丛林

这只是蜜罐合同的一个相对简单的例子。在野外有很多这样的动物:

*   [https://medium . com/coin monks/classing-an-ether eum-honey-pot-7102 D7 def 5 e 0](/coinmonks/dissecting-an-ethereum-honey-pot-7102d7def5e0)
*   [https://medium . com/@ Gerhard . Wagner/the-phenomenon-of-smart-contract-honey pots-755 C1 f 943 f7b](/@gerhard.wagner/the-phenomena-of-smart-contract-honeypots-755c1f943f7b)
*   [https://hacker noon . com/the-sneakie est-ether eum-scam-ever-6dc 138061235](https://hackernoon.com/the-sneakiest-ethereum-scam-ever-6dc138061235)
*   [https://medium . com/@ jsanjuas/an-analysis-of-a-couple-ether eum-honey pot-contracts-5c 07 c 95 b 0a 8d](/@jsanjuas/an-analysis-of-a-couple-ethereum-honeypot-contracts-5c07c95b0a8d)

是不是听起来太容易太琐碎了？下面是一个真实以太丢失的辛酸故事:[https://www . Reddit . com/r/eth dev/comments/7x 5 rwr/tricked _ by _ a _ honey pot _ contract _ or _ beated _ by/](https://www.reddit.com/r/ethdev/comments/7x5rwr/tricked_by_a_honeypot_contract_or_beaten_by/)这玩意儿居然真的管用！

然而，我想争辩称这些智能合同为蜜罐。蜜罐已经存在很久了。白帽子使用它们来检测和防止攻击，也就是说，是为了做好事，而不是偷别人的钱。这是一个主要的区别。这些合同不是为了防止攻击，而是为了从不太熟练的黑客那里收集附加工作。所以他们需要一个不同的名字。我会称之为捕鼠器。

检查有多少蜜罐是在没有验证源代码的情况下发布的，这将是非常有趣的。这可能是一个全新的游戏水平。

你遇到过蜜罐吗？你认为攻击黑客是道德的吗？

*原载于 2018 年 8 月 24 日*[*www . unchained . team*](https://www.unchained.team/blog/hacking-the-hackers-honeypots-on-ethereum-network)*。*