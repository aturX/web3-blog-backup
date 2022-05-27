## 如何部署 Solidity 可升级合约?

今天我将讨论一个我认为对所有 Solidity 开发人员都非常有用的主题，即可升级的智能合约。

在我们开始之前，我们需要区分“可升级”并不意味着“可变”，区块链中的代码本质上是不可变的，一旦部署，它将永远存在。


## 究竟什么是可升级合约？

当我们说合约是可升级的时，我们指的是使用**代理模式**的合约。


此过程涉及部署两个合约：

- 代理合约

- 实现合约 

代理模式的结构类似于：


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653621721990/XPBRj_3eN.png align="left")


与客户端(RPC)直接与实现合约交互（常见的不可升级合约）不同，可升级合约在两者之间有一个代理合约，用于指出智能合约实现的不同版本。


简而言之，这种模式实际上所做的是，每次我们需要升级合约时，我们都会部署一个新的实现合约，并更改指向具有新地址的实现合约的代理合约的值。

这种模式给人一种智能合约可升级的感觉，因为用户与之交互的智能合约 (代理合约地址) 地址永远不会改变。

有两种流行的代理模式：

- Transparent Proxy  透明代理

- UUPS (通用可升级代理标准)


## 可升级 VS 不可升级

除了一个可升级而另一个不可升级这一显而易见的事实之外，不可升级的智能合约与可升级的智能合约之间的另一个**主要区别是缺少构造函数**。


相反，我们使用了一个名为`initialize`的函数（请注意，这个名称不是强制性的，你可以使用任何名称）。


## 具体演示

让我们深入研究代码以获得更直观的理解。

在这个例子中，我将使用 `UUPS 代理模式` 实现一个简单的 ERC1115 合约，并使用 `openzeppelin`库来简化编写过程。


```
// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.7.0 <0.9.0;

import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC1155/ERC1155Upgradeable.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/access/OwnableUpgradeable.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/Initializable.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/UUPSUpgradeable.sol";


contract ERC1155UpgradeableV1 is 
    Initializable, 
    OwnableUpgradeable, 
    ERC1155Upgradeable, 
    UUPSUpgradeable
{

    function initialize() public initializer {
        __ERC1155_init("");
        __Ownable_init();
        __UUPSUpgradeable_init();
    }
}
```

首先，让我们看一下我们将要导入的依赖库。

- ERC1115Upgradable：ERC1115 合约的可升级版本。

- OwnableUpgradable：Ownable合约的可升级版本

- Initializable：这是一个基础合约，可帮助将部署在代理后面的任何类型的合约。

- UUPSUpgradable：UUPS 模式合约。

现在，让我谈谈初始化函数: `initialize()`。

在这个函数中，我们需要初始化我们导入的合约。

`__ERC1115_init`：设置代币 URI 并初始化代币合约。
`__Ownable_init`和`__UUPSUpgradeable_init`：它们都初始化了各自的合约
`Initializable` 合约不需要初始化，因为我们只需要`initializer`修饰符。


非常重要的一点是，由于我们不使用构造函数，我们必须使用`initializer`修饰符保护我们的初始化函数，以确保它只能被调用一次以避免代理黑客攻击。


```
// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.7.0 <0.9.0;

import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC1155/ERC1155Upgradeable.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/access/OwnableUpgradeable.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/Initializable.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/UUPSUpgradeable.sol";


contract ERC1155UpgradeableV1 is 
    Initializable, 
    OwnableUpgradeable, 
    ERC1155Upgradeable, 
    UUPSUpgradeable
{

    function initialize() public initializer {
        __ERC1155_init("");
        __Ownable_init();
        __UUPSUpgradeable_init();
    }

    function setURI(string memory newuri) public {
        _setURI(newuri);
    }

    function mint(
        address account,
        uint256 id,
        uint256 amount
    ) public {
        _mint(account, id, amount, "");
    }

    function mintBatch(
        address to,
        uint256[] memory ids,
        uint256[] memory amounts,
        bytes memory data
    ) public {
        _mintBatch(to, ids, amounts, data);
    }

    function _authorizeUpgrade(address newImplementation) internal override onlyOwner {}
}
```


其余的合约函数与普通的 ERC1115合约是一样的，唯一的区别是`_authorizeUpgrade`函数。

由于我们的合约继承自 UUPSUpgradable 合约，我们**需要定义并覆盖这个函数**。

在我的例子中，我没有实现这个函数的内容，因为它通常用于检查谁被授权更新合约，所以我使用 OwnableUpgradable 合约中的onlyOwner修饰符来实现这个目的。

一般来说，使用 openzeppelin 编写代理合约会让你感觉就像在编写普通合约，只是有一些不同之处。

现在我们的合约 v1 已经准备好部署了，所以让我们编写我们的部署脚本，它会略有不同，因为我们需要一次部署两个合约。



`Openzeppelin` 有两个升级插件来帮助我们部署和升级我们的可升级智能合约。一个用于 `Hardhat`，另一个用于 `Truffle`。

我将使用 `Truffle` 插件。

你可以查看官方的 openzeppelin文档进行安装和设置。

安装后，我们现在可以编写我们的脚本了。

```
const { deployProxy } = require("@openzeppelin/truffle-upgrades");

const ERC1155UpgradeableV1 = artifacts.require("ERC1155UpgradeableV1");

module.exports = async function (deployer) {
  const instance = await deployProxy(ERC1155UpgradeableV1, [], {
    deployer,
    kind: "uups",
  });
};
```

这就是我们的部署脚本的样子，正如您所见，它与普通的部署脚本略有不同。

我们不使用`deployer.deploy`函数，而是使用 Truffle 插件中的`deployProxy`函数。

该函数接收三个参数：

- 合约

- 初始化函数的参数（在我们的例子中，我们将此参数传递为空，因为我们的合约初始化函数`initialize()`不接收任何参数）。

- 包含 `deployer` 参数和一组可选参数的对象，在这种情况下，我们传递带有值“uups”的kind参数，声明我们的代理模式是 `UUPS 模式`。

现在我们的可升级合约的部署脚本已经完成。

在部署它之前，请确保对智能合约运行各种测试，虽然它是可升级的，并不意味着我们希望它有任何错误。


## 结论

这是对可升级合约如何工作以及如何部署合约的快速教程。

希望这可以帮助到你. 

###  关注我们, 获得更多优质内容

- *Twitter*:    [@AntCaveClub](https://twitter.com/AntCaveClub)

- *蚁穴Web3社区*:  [https://antcave.club/](https://antcave.club/)

- *Youtube*:  [链求君](https://www.youtube.com/channel/UCDrmDcLjnmIQk-xtNuJ42Sw)



## 参考

- https://docs.openzeppelin.com/learn/upgrading-smart-contracts

- https://docs.openzeppelin.com/upgrades-plugins/1.x/

- https://docs.openzeppelin.com/contracts/4.x/upgradeable
85



