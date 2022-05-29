## 智能合约工厂：如何用Solidity合约创建另一个合约?

## 介绍

solidity 中的 `new` 关键字允许使用模板创建和部署新的合约实例。“XxxContractFactory”是一种命名约定和用于创建“XxxContract”合约的模式。

本文的目的是了解制作简单合约工厂的过程，同时了解它的工作原理并想象一些潜在的用例。让我们直接开始。

合约工厂是一种基于模板创建合约的设计模式。用户可以在字段中引入值，作为输入来初始化新部署的合约的状态变量。为什么这很有用？根据一些用例，你可以做一些很酷的事情，例如：

- 允许用户创建流动资金池：[Uniswap](https://docs.uniswap.org/protocol/V2/reference/smart-contracts/factory)

- 利用每个地址的 [Adidas 2 NFT](https://etherscan.io/tx/0x6a3d8584a6272a1d73ff297592b401fe10d3a90fd385efff55f68f32f29ecf61) 铸币限制：交易

- 无代码平台以帮助用户创建自己的代币：[ThirdWeb](https://portal.thirdweb.com/)

- 其他例子：业务流程管理和医疗保健


## 实现原理

对于这个例子，我将使用我编写的 Bank 合约（模板）和 BankFactory 合约（工厂）。目的是部署一次工厂Factory合约，并根据需要创建多个银行Bank合约，而无需自己部署每个合约。



![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653816654934/9wD27Raxr.png align="left")


1、首先是银行 Bank（模板）：

构造函数初始化由 BankFactory 传递的状态变量。在这里，我们传递Bank拥有的资金数量，谁是Bank的所有者以及谁创建了Bank合约。


```
//  这是一个模板合约, 拥有初始化 Bank 及其变量 

contract Bank {
  uint256 public bank_funds;
  address public owner;
  address public deployer;

  constructor(address _owner, uint256 _funds) {
    bank_funds = _funds;
    owner = _owner;
    deployer = msg.sender;
  }
}
```


2、第二个 BankFactory（工厂）：

在这里，我们实例化银行合约 Bank，创建一个类型为银行的列表 `Bank[] public list_of_banks` 来跟踪新创建的合约，最后是`createBank()`一个将银行的构造函数参数作为参数的函数。

这是创建新合约的行：

`bank = new Bank(_owner,  _funds)`

数组中索引 0 处的值`list_of_banks`成为创建合约的地址！


```
contract BankFactory {
  // instantiate Bank contract
  Bank bank;
  //keep track of created Bank addresses in array 
  Bank[] public list_of_banks;

  // function arguments are passed to the constructor of the new created contract 
  function createBank(address _owner, uint256 _funds) external {
    bank = new Bank(_owner, _funds);
    list_of_banks.push(bank);
  }
}
```

这就是全部。现在，当 Factory 已部署后，`createBank(.., ..)` 使用所需值作为参数调用函数将导致新Bank合约保存这些值！

想看看如何用 HardHat 做到这一点？

## 使用 HardHat 进行测试

1、创建一个新目录并安装安全帽：

```
npm install --save-dev hardhat
```


2、创建一个新项目并安装依赖项：

```
npx hardhat
```

3、使用你选择的文本编辑器打开文件夹。我喜欢 Visual Studio 代码：


4、创建一个新的 Solidity 文件并将代码复制到其中：

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "hardhat/console.sol";

// Template used to initialize state variables 
contract Bank {

  uint256 public bank_funds;
  address public owner;
  address public deployer;

  constructor(address _owner, uint256 _funds) {
    bank_funds = _funds;
    owner = _owner;
    deployer = msg.sender;
  }
}


contract BankFactory {

  // instantiate Bank contract
  Bank bank;

  //keep track of created Bank addresses in array 
  Bank[] public list_of_banks;


  // function arguments are passed to the constructor of the new created contract 
  function createBank(address _owner, uint256 _funds) external {
    bank = new Bank(_owner, _funds);
    list_of_banks.push(bank);
  }
}
```

5、编译合约：

```
npx hardhat compile 
```


6、测试它！注意测试脚本。将代码复制粘贴到其中并运行它！

```
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("Deploying a BankFactory and creating Bank instances ", function () {

  it("Should instantiate the created Bank and call its functions", async () => {

    // ContractFactory in ethers.js is an abstraction used to deploy new smart contracts, so Token here is a factory for instances of our token contract.
    const BF = await ethers.getContractFactory('BankFactory');
    const Bank = await ethers.getContractFactory('Bank');

    // deploy bank factory
    const bf = await BF.deploy();
    await bf.deployed();

    const owner = "0x0d8D32B83aaBBB675333468E4794eE0cDE666666"
    // call function with (address owner, uint256 number) parameters
    const tx1_receipt = await bf.createBank(owner, 69420);
    const tx2_receipt = await bf.createBank(owner, 00100);
    const tx3_receipt = await bf.createBank(owner, 113377);

    console.log("BankFactory address at: ", tx1_receipt.to);
    console.log("\n")


    // Get the addresses of the deployed bank contracts 
    const bank1_address = await bf.list_of_banks(0);
    const bank2_address = await bf.list_of_banks(1);
    const bank3_address = await bf.list_of_banks(2);
    console.log("BankFactory array of banks, address at (index 0): ", bank1_address);
    console.log("BankFactory array of banks, address at (index 1): ", bank2_address);
    console.log("BankFactory array of banks, address at (index 2): ", bank3_address);
    console.log("\n")


    // Attach the created Bank instance to the address it's located at. Call functions.
    const bank1 = await Bank.attach(bank1_address);
    const bank2 = await Bank.attach(bank2_address);
    const bank3 = await Bank.attach(bank3_address);
    
    
    // Get Bank 1 variables
    const bank1_owner = await bank1.owner();
    const bank1_bank_funds = await bank1.bank_funds();
    const bank1_deployer = await bank1.deployer()
    console.log(`Bank 1 \nowner: ${bank1_owner}\nbank_funds: ${bank1_bank_funds}\ndeployer: ${bank1_deployer}`)
    console.log("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~")


    // Get Bank 2 variables
    const bank2_owner = await bank2.owner();
    const bank2_bank_funds = await bank2.bank_funds();
    const bank2_deployer = await bank2.deployer()
    console.log(`Bank 2 \nowner: ${bank2_owner}\nbank_funds: ${bank2_bank_funds}\ndeployer: ${bank2_deployer}`)
    console.log("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~")

    // Get Bank 3 variables
    const bank3_owner = await bank3.owner();
    const bank3_bank_funds = await bank3.bank_funds();
    const bank3_deployer = await bank3.deployer()
    console.log(`Bank 3 \nowner: ${bank3_owner}\nbank_funds: ${bank3_bank_funds}\ndeployer: ${bank3_deployer}`)
    console.log("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~")
  })

});
```


希望你和我一样完成的这个挑战! 


### 参考链接：

https://docs.uniswap.org/protocol/V2/reference/smart-contracts/factory

https://research.csiro.au/blockchainpatterns/general-patterns/contract-structural-patterns/factory-contract/

https://arxiv.org/abs/1706.03700

https://www.researchgate.net/publication/303996559_Untrusted_Business_Process_Monitoring_and_Execution_Using_Blockchain

https://docs.openzeppelin.com/learn/developing-smart-contracts



###  关注我们, 获得更多优质内容

- *Twitter*:    [@AntCaveClub](https://twitter.com/AntCaveClub)

- *蚁穴Web3社区*:  [https://antcave.club/](https://antcave.club/)

- *Youtube*:  [链求君](https://www.youtube.com/channel/UCDrmDcLjnmIQk-xtNuJ42Sw)




