## 智能合约实例-1:   实现一个 Eth 钱包

## 合约代码

*Ether Wallet 合约地址:  *  https://rinkeby.etherscan.io/address/0x7f89e7fddf3e2aca143d5c35e40cf7169a0e39e3#code



## 实现功能

一个基本钱包的例子。

- 任何人都可以发送 ETH 到合约

- 只有所有者可以撤回提出 ETH 


## 源码解释 

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract EtherWallet {

    // 定义合约owner , 可支付地址
    address payable public owner;

    constructor() {
        // 初始化: 部署合约的地址 设置为 owner 
        owner = payable(msg.sender);
    }

    // receive 函数 , 用于接收 ETH , 没有 该函数的合约, 无法接收ETH 
    receive() external payable {}


    // 提取 ETH , 只能 owner 提取 
    function withdraw(uint _amount) external {
        require(msg.sender == owner, "caller is not owner");
        payable(msg.sender).transfer(_amount);
    }

    // 查询合约余额 
    function getBalance() external view returns (uint) {
        return address(this).balance;
    }
}

```
