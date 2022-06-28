## 智能合约实例-7: 使用 Create2 预计算合约地址

##  实现功能 


使用`create2`, 可以在部署合约之前预先计算合约地址


## 源码示例

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;


// 创建合约时，合约的地址是根据创建合约的地址和随着每次合约创建而增加的计数器计算出来的。

// 如果您指定选项salt（bytes32 值），则合约创建将使用不同的机制来提供新合约的地址：

// 它将根据创建合约的地址、给定的盐值、创建合约的（创建）字节码和构造函数参数来计算地址。


contract Factory {
    // 返回新部署的合约地址  
    function deploy(
        address _owner,
        uint _foo,
        bytes32 _salt
    ) public payable returns (address) {

        // 此语法是一种无需组装即可调用 create2 的较新方法，您只需要传递 salt
        // https://docs.soliditylang.org/en/latest/control-structures.html#salted-contract-creations-create2
        return address(new TestContract{salt: _salt}(_owner, _foo));
    }
}

// 这是使用汇编的旧方法
contract FactoryAssembly {
    event Deployed(address addr, uint salt);

    // 1. 获取要部署的合约字节码
    // 注意：_owner 和 _foo 是 TestContract 构造函数的参数
    function getBytecode(address _owner, uint _foo) public pure returns (bytes memory) {
        bytes memory bytecode = type(TestContract).creationCode;

        return abi.encodePacked(bytecode, abi.encode(_owner, _foo));
    }

    // 2. 计算要部署的合约地址
    // 注意：_salt 是用于创建地址的随机数
    function getAddress(bytes memory bytecode, uint _salt)
        public
        view
        returns (address)
    {
        bytes32 hash = keccak256(
            abi.encodePacked(bytes1(0xff), address(this), _salt, keccak256(bytecode))
        );

        // 注意：将最后 20 个字节的哈希值转换为地址
        return address(uint160(uint(hash)));
    }

    // 3. 部署合约
    // 注意：
    // 检查事件日志 Deployed，其中包含已部署的 TestContract 的地址。
    // 日志中的地址应该等于从上面计算的地址。
    function deploy(bytes memory bytecode, uint _salt) public payable {
        address addr;

        /*
        注意: 如何调用 create2 ? 

        create2(v, p, n, s)
        使用内存 p 到 p + n 的代码创建新合约
        并发送 v wei
        并返回新地址
        where new address = 前 20 bytes of keccak256(0xff + address(this) + s + keccak256(mem[p…(p+n)))
              s = big-endian 256-bit value
        */
        assembly {
            addr := create2(
                callvalue(), // wei sent with current call
                // Actual code starts after skipping the first 32 bytes
                add(bytecode, 0x20),
                mload(bytecode), // Load the size of code contained in the first 32 bytes
                _salt // Salt from function arguments
            )

            if iszero(extcodesize(addr)) {
                revert(0, 0)
            }
        }

        emit Deployed(addr, _salt);
    }
}


contract TestContract {
    address public owner;
    uint public foo;

    constructor(address _owner, uint _foo) payable {
        owner = _owner;
        foo = _foo;
    }

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }

    function convert(uint256 n) public pure returns (bytes32) {
        return bytes32(n);
    }
}




```




## 实例演示 

- 采用 Create 

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;


contract CreateDemo {
    function deploy (address owner, uint foo) public returns(address) {
        TestContract new_address = new TestContract(owner, foo);

        return address(new_address);
    }
    // 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,1     ==>      0xD3e2008b4Da2cD6DEAF73471590fF30C86778A48


    // create 计算地址方式 
    function getCreateAddress(address senderAddress, uint8 nonce) public pure returns(address){
    
        address nonce0 = address(uint160(uint256(keccak256(abi.encodePacked(bytes1(0xd6), bytes1(0x94), senderAddress, bytes1(nonce))))));
        // 注意：将最后 20 个字节的哈希值转换为地址
        return nonce0;

        // nonce 0 : 0xa131AD247055FD2e2aA8b156A11bdEc81b9eAD95
    }


    // keccak256( 0xff ++ senderAddress ++ salt ++ keccak256(init_code))[12:]

}

contract TestContract {
    address public owner;
    uint public foo;

    constructor(address _owner, uint _foo) payable {
        owner = _owner;
        foo = _foo;
    }

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }

    function convert(uint256 n) public pure returns (bytes32) {
        return bytes32(n);
    }
}
```



## 关注更多 

Web3 技术与应用，研究实践分享！
 

*Twitter*:    [@AntCaveClub](https://twitter.com/AntCaveClub)

*蚁穴Web3社区*:  [https://antcave.club/](https://antcave.club/)