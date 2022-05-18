## 如何使用 Flashbot?

在本教程中，我们将学习如何使用 Flashbots 来获得矿工/最大可提取价值，也称为 MEV。矿工/最大可提取价值或 MEV 表示可以通过重新排序、插入或审查交易从区块链中提取的金额。当用户向他们的系统发送交易时，不同的 Defi 实现会泄漏 MEV。结果，机器人运营商和矿工通过搜索和捕获这个泄露的价值来获利。

区块链上的这种收入提取过程已经发生了多年。2020 年 1 月，Flashbots 组织创建了一个[MEV 网站 Explorer](https://explore.flashbots.net/)来跟踪 MEV 交易。然后在 2020 年夏天，新的 Defi 项目创造了许多新的套利机会。由于这些新机会，MEV 价值开始飙升，如MEV 网站 Explorer上的每日提取价值图表所示。这对以太坊产生了很多负面影响，从高昂的 Gas 汽油费到由于链拥塞而导致的交易恢复。


## Flashbots简介

由于区块链拥塞，Flashbots 组织提出了一个流程，允许所有参与者（用户、机器人操作员和矿工）从 MEV 机会中受益。Flashbots 设计并实施了一个系统，允许机器人操作员将交易直接发送给矿工。然后，如果对他们有利，矿工会将这些交易包含在下一个区块中。这一过程导致用户获得有保证的执行，而矿工则因将这些交易纳入区块而获得额外费用。

Flashbots 解决了几个用例。他们是：

1. 寻求快速访问区块空间的用户和机器人操作员，而不必担心价格过高或交易被退回

2. 为他们的交易寻找狙击、前端运行、后端运行或三明治保护的用户和机器人操作员

3. 需要解决高级用例的 Dapps 和协议。例如，一次发送多笔交易交易以进行原子执行。(常用于解救私钥被泄漏的钱包中尚未转移的资产)

Flashbots 是对以太坊协议的增强，它增加了一个网络层，供用户向矿工发送私人交易。这些交易不在内存池中，因此其他参与者看不到它们。用户向私人交易池提交交易并收取费用，矿工将这些消息包含在下一个区块中。

为了使这个拍卖过程顺利进行，Flashbot 团队创建了一个名为[MEV-Geth](https://docs.flashbots.net/flashbots-auction/overview/)的 Geth 客户端软件补丁。此外，还有一个 MEV 中继器，它充当交易捆绑中继器。这两个增强功能的结合可以防止气体战争、失败的交易，并消除前端运行漏洞。

下图是用户与操作 MEV-Geth的矿工交互的表示。请记住，所有矿工和搜索者 都不使用MEV-Geth。


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652856388043/8_6n5wsuk.png align="left")


## 谁使用 Flashbots？

搜索者和矿工参与 Flashbot 拍卖。在下面的部分中，我们将回顾如何将 Flashbots 用作搜索器和矿工。


### 搜索者

搜索者直接向中继器提交交易包，而不是通过传统的以太坊 p2p 网络。直接向 Flashbots 中继器提交交易的用户是私有的。网络的其余部分无法看到这些交易。搜索者可以通过增加 gas 或直接向矿工地址转移 ETH 来支付这项服务的费用（这取决于交易处理）。

要访问 Flashbots 网络搜索需要：

- 一个私钥。用于识别

- 一种与 Flashbots 网络交互的方式 (RPC)。Flashbots 运行一个中继，您将发送捆绑包到 `relay.flashbots.net`。

- 交易的“捆绑” 

以下是有关如何设置 Flashbots 提供程序、获取私钥并在 Web3.py 中发送事务包的示例Python代码。下面的代码可以在Flashbots 文档中找到。这个过程也可以通过使用JavaScript来实现。有关更多 Flashbot 信息，请访问他们的 [Github 存储库](https://github.com/flashbots/web3-flashbots)。


```
from eth_account.signers.local import LocalAccount
from web3.middleware import construct_sign_and_send_raw_middleware

from flashbots import flashbot
from flashbots.types import SignTx
from eth_account.account import Account
from web3 import Web3, HTTPProvider
from web3.types import TxParams, Wei

import os

"""
In this example we setup a transaction for 0.1 eth with a gasprice of 1
From here we will use Flashbots to pass a bundle with the needed content
"""

# ETH_ACCOUNT_SIGNATURE is an Ethereum private key that does NOT store funds and is NOT your bot's primary key.
#This is an identifying key for signing payloads to establish reputation and whitelisting
ETH_ACCOUNT_SIGNATURE: LocalAccount = Account.from_key(os.environ.get("ETH_SIGNATURE_KEY"))
ETH_ACCOUNT_FROM: LocalAccount = Account.from_key(os.environ.get("ETH_PRIVATE_FROM"))
ETH_ACCOUNT_TO: LocalAccount = Account.from_key(os.environ.get("ETH_PRIVATE_TO"))

print("Connecting to RPC")

# Create a web3 object with a standard json rpc provider, such as Infura, Alchemy, or your own node.
w3 = Web3(HTTPProvider("http://localhost:8545"))
w3.middleware_onion.add(construct_sign_and_send_raw_middleware(ETH_ACCOUNT_FROM))

# Flashbots providers require both a standard provider and ETH_ACCOUNT_SIGNATURE (to establish reputation)
flashbot(w3, ETH_ACCOUNT_SIGNATURE)

print(f"From account {ETH_ACCOUNT_FROM.address}: {w3.eth.get_balance(ETH_ACCOUNT_FROM.address)}")
print(f"To account {ETH_ACCOUNT_TO.address}: {w3.eth.get_balance(ETH_ACCOUNT_TO.address)}")

# Setting up an transaction with 1 in gasPrice where we are trying to send
print("Sending request")
params: TxParams = {
    "from": ETH_ACCOUNT_FROM.address,
    "to": ETH_ACCOUNT_TO.address,
    "value": w3.toWei("1.0", "gwei"),
    "gasPrice": w3.toWei("1.0", "gwei"),
    "nonce": w3.eth.get_transaction_count(ETH_ACCOUNT_FROM.address),
}

try:
    tx = w3.eth.send_transaction(
        params,
    )
    print("Request sent! Waiting for receipt")
except ValueError as e:
    # Skipping if TX already is added and pending
    if "replacement transaction underpriced" in e.args[0]["message"]:
        print("Have TX in pool we can use for the example")
    else:
        raise


print("Setting up flashbots request")
nonce = w3.eth.get_transaction_count(ETH_ACCOUNT_FROM.address)
bribe = w3.toWei("0.01", "ether")

signed_tx: SignTx = {
    "to": ETH_ACCOUNT_TO.address,
    "value": bribe,
    "nonce": nonce + 1,
    "gasPrice": 0,
    "gas": 25000,
}

signed_transaction = ETH_ACCOUNT_TO.sign_transaction(signed_tx)

bundle = [
    #  some transaction
    {
        "signer": ETH_ACCOUNT_FROM,
        "transaction": {
            "to": ETH_ACCOUNT_TO.address,
            "value": Wei(123),
            "nonce": nonce,
            "gasPrice": 0,
        },
    },
    # the bribe
    {
        "signed_transaction": signed_transaction.rawTransaction,
    },
]

block = w3.eth.block_number

result = w3.flashbots.send_bundle(bundle, target_block_number=w3.eth.blockNumber + 3)
result.wait()
receipts = result.receipts()
block_number = receipts[0].blockNumber

# the miner has received the amount expected
bal_before = w3.eth.get_balance(ETH_ACCOUNT_FROM.address, block_number - 1)
bal_after = w3.eth.get_balance(ETH_ACCOUNT_FROM.address, block_number)
profit = bal_after - bal_before - w3.toWei("2", "ether")  # sub block reward
print("Balance before", bal_before)
print("Balance after", bal_after)
assert profit == bribe

# the tx is successful
print(w3.eth.get_balance(ETH_ACCOUNT_TO.address))
``` 

此外，Flashbots 存储库中还有一些其他有趣的示例：

- TypeScript 中的简单[套利机器人](https://github.com/flashbots/simple-arbitrage)

- 赞助交易(A地址交易,  B地址付款)，它从执行者帐户提交交易，但从 TypeScript 中的赞助者帐户支付交易费用

## 矿工和矿池运营商

矿工是收集所有搜索包并产生块的一方。矿工传统上运行 go-ethereum 客户端 (Geth) 并按 gas 价格订购交易。但是，连接到 Flashbots 网络的矿工运行  由 Flashbots 维护的[mev-geth 客户端版本](https://github.com/flashbots/mev-geth)。

mev-geth 节点使用第一个价格密封投标拍卖来评估捆绑包。系统选择最有利可图的捆绑包放置在块的顶部。矿工可以评估所有收到的捆绑包，并将不冲突的捆绑包组合起来，以尽可能产生最有利可图的块。然后该节点将 Flashbots 块与常规块进行比较，并开始挖掘最有利可图的块。


矿工可以完全访问捆绑内容，并且可以重新排序、窃取和/或审查搜索者和中继者发送给他们的捆绑。请记住，所有矿工都不诚实。

矿工和矿池操作员可以按照此处的说明开始运行 MEV-Geth。他们的快速入门指南中的说明将帮助人们了解如何构建和启动 MEV-Geth，在自定义版本的 Geth 之上实施 MEV-Geth，等等。

随后，mev-geth-demo存储库将允许您运行 MEV-Geth 节点，并展示矿工如何通过直接接受 MEV 捆绑包或通过额外交易从中获利。

此外，如果您在设置 MEV-Get 时需要帮助，请访问[Flashbots Discord 服务器。](https://discord.com/invite/7hvTycdNcK)

## 资源

### 区块链网络

以下是与 EVM 兼容的 Mainnet 和 Testnet 区块链网络的列表。每个链接都包含网络配置、用于测试 ETH 和令牌的多个水龙头的链接、桥接详细信息以及每个区块链的技术资源。基本上，您需要在每条链上测试和部署智能合约或去中心化应用程序所需的一切。要查看流行的以太坊论坛和聊天应用程序列表，请单击此处。

### Web3 软件库
您可以使用以下库与 EVM 兼容的区块链进行交互。

- Python：Web3.py  Python 库，用于与以太坊交互。Web3.py 示例

- Js：web3.js 以太坊 JavaScript API

- Java：web3j  Web3 Java Ethereum Ðapp API

- PHP: web3.php 用于与以太坊区块链和生态系统交互的 php 接口。


## 更多关注 

- Twitter:    [@AntCaveClub](https://twitter.com/AntCaveClub)

- 蚁穴Web3社区:  [https://antcave.club/](https://antcave.club/)

- Youtube:  [链求君](https://www.youtube.com/channel/UCDrmDcLjnmIQk-xtNuJ42Sw)
