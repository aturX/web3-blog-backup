## 智能合约实例-5: 发行erc20代币

## ERC20 代币

任何遵循ERC20 标准的合约都是 ERC20 代币。

ERC20 标准:    https://eips.ethereum.org/EIPS/eip-20


## 实现功能

ERC20 代币提供以下功能

- 转移代币
- 允许其他人代表代币持有者转移代币


接下来查看, ERC20的具体实现.


## 代码讲解 


- ERC20 的标准接口

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.0.0/contracts/token/ERC20/IERC20.sol
interface IERC20 {

    // 总供应量
    function totalSupply() external view returns (uint);
    
    // 某地址的余额
    function balanceOf(address account) external view returns (uint);
    

    // 转移 Token
    function transfer(address recipient, uint amount) external returns (bool);
    
    // 返回spender仍然允许提取的金额
    function allowance(address owner, address spender) external view returns (uint);
    
    // 授权spender 及金额数量
    function approve(address spender, uint amount) external returns (bool);
    
    // 该transferFrom方法用于提款工作流程，允许合约代表你转移代币。
    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);
    
    // 触发事件: 转移代币
    event Transfer(address indexed from, address indexed to, uint value);

    // 触发事件: 授权代币
    event Approval(address indexed owner, address indexed spender, uint value);
}

```

- ERC20 的代币合约示例

查看一个完整的代币发行合约, 需要实现那些函数和功能, 让其符合ERC20 标准. 

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "./IERC20.sol";

contract ERC20 is IERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "Solidity ERC20 Test";
    string public symbol = "SET";
    uint8 public decimals = 18;

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }
    
    // 铸造新的token 
    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }
    
    // 销毁token 数量
    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}

```

## 实战案例 

- 创建自己的ERC20 的最佳实践 

使用Open Zeppelin:  https://github.com/OpenZeppelin/openzeppelin-contracts

这是一个智能合约代码仓库, 它帮我们实现了大部分合约的细节, 并且确保了合约的安全性. 

通过使用Open Zeppelin，创建自己的 ERC20 代币真的很容易。

这是一个例子

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// 导入Github 上的源代码 
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.0.0/contracts/token/ERC20/ERC20.sol";

// 继承 Open Zeppelin 的 ERC20 , 它帮我们实现了所有 ERC20标准的细节. 

contract MyToken is ERC20 {

    // 初始化, Token 的名称符号, 发行一部分数量.
    constructor(string memory name, string memory symbol) ERC20(name, symbol) {
        // Mint 了 100 个Token  给 msg.sender (合约部署者) 
       
        // 关于精度decimals:    默认会是 18 
   
        // 1 token = 1 * (10 ** decimals)
        _mint(msg.sender, 100 * 10**uint(decimals()));
    }
}

```

- 代币的交换合约实现:  Swap 

类似 [Uniswap](https://uniswap.org/) , 我们创建一个交换合约, 将一种代币交换成另外一种代币. 

这是一个示例合约，TokenSwap用于将一个 ERC20 代币换成另一个。

该合约将通过调用`transferFrom`来交换代币.
 
```
transferFrom(address sender, address recipient, uint256 amount)

```
这会实现,  `amount` 数量的代币从`sender`转移到`recipient`。

为了`transferFrom`成功，`sender`必须:

- 在余额中有至少`amount`数量的代币

- 允许提取 `amount` 数量的代币必须先调用`TokenSwap`的 `approve` 函数授权



```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.0.0/contracts/token/ERC20/IERC20.sol";

/*
如何用 Swap 兑换 Token 代币?  

1. Alice 有 100 个代币 AliceCoin, 它是一个 ERC20 代币.
2. Bob 有 100  个代币 BobCoin, 它也是一个  ERC20 代币.
3. Alice 和 Bob 想要用 10 AliceCoin 换取 20 BobCoin.
4. Alice 或者 Bob 部署了一个 TokenSwap 合约
5. Alice 授权 TokenSwap 去提取 10 AliceCoin 代币
6. Bob 授权 TokenSwap 去提取 20  BobCoin 代币
7. Alice 或者 Bob 调用 TokenSwap.swap()
8. Alice 和 Bob 交易完成 ✅ 
*/

contract TokenSwap {
    IERC20 public token1;
    address public owner1;
    uint public amount1;
    IERC20 public token2;
    address public owner2;
    uint public amount2;

    constructor(
        address _token1,
        address _owner1,
        uint _amount1,
        address _token2,
        address _owner2,
        uint _amount2
    ) {
        token1 = IERC20(_token1);
        owner1 = _owner1;
        amount1 = _amount1;
        token2 = IERC20(_token2);
        owner2 = _owner2;
        amount2 = _amount2;
    }

    function swap() public {
        require(msg.sender == owner1 || msg.sender == owner2, "Not authorized");
        require(
            token1.allowance(owner1, address(this)) >= amount1,
            "Token 1 allowance too low"
        );
        require(
            token2.allowance(owner2, address(this)) >= amount2,
            "Token 2 allowance too low"
        );

        _safeTransferFrom(token1, owner1, owner2, amount1);
        _safeTransferFrom(token2, owner2, owner1, amount2);
    }

    function _safeTransferFrom(
        IERC20 token,
        address sender,
        address recipient,
        uint amount
    ) private {
        bool sent = token.transferFrom(sender, recipient, amount);
        require(sent, "Token transfer failed");
    }
}

```


