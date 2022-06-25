## 智能合约实例-2: 实现一个多签钱包

让我们尝试创建一个多重签名钱包:

### 多签钱包功能

- 提交交易

- 批准和撤销对待处理交易的批准

- 在足够多的所有者批准后，任何人都可以执行交易

```
 // SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract MultiSigWallet {

    // 存钱 记录
    event Deposit(address indexed sender, uint amount, uint balance);

    // 提交交易 记录
    event SubmitTransaction(
        address indexed owner,
        uint indexed txIndex,
        address indexed to,
        uint value,
        bytes data
    );

    // 确认交易 记录
    event ConfirmTransaction(address indexed owner, uint indexed txIndex);
    
    // 驳回交易 记录
    event RevokeConfirmation(address indexed owner, uint indexed txIndex);
    
    // 执行交易 记录
    event ExecuteTransaction(address indexed owner, uint indexed txIndex);

    // 多签成员 
    address[] public owners;
    mapping(address => bool) public isOwner;

    // 交易多签通过 人数
    uint public numConfirmationsRequired;

    // 交易数据
    struct Transaction {
        address to;
        uint value;
        bytes data;
        bool executed;
        uint numConfirmations;
    }

    // mapping from tx index => owner => bool  每个多签者确认情况
    mapping(uint => mapping(address => bool)) public isConfirmed;

    // 全部交易数据
    Transaction[] public transactions;

    modifier onlyOwner() {
        require(isOwner[msg.sender], "not owner");
        _;
    }

    // 交易存在
    modifier txExists(uint _txIndex) {
        require(_txIndex < transactions.length, "tx does not exist");
        _;
    }

    // 交易未执行
    modifier notExecuted(uint _txIndex) {
        require(!transactions[_txIndex].executed, "tx already executed");
        _;
    }

    // 交易未确认
    modifier notConfirmed(uint _txIndex) {
        require(!isConfirmed[_txIndex][msg.sender], "tx already confirmed");
        _;
    }

    // 初始化, 设置好签名者, 还有签名通过权重
    constructor(address[] memory _owners, uint _numConfirmationsRequired) {
        require(_owners.length > 0, "owners required");
        require(
            _numConfirmationsRequired > 0 &&
                _numConfirmationsRequired <= _owners.length,
            "invalid number of required confirmations"
        );

        // 
        for (uint i = 0; i < _owners.length; i++) {
            address owner = _owners[i];

            require(owner != address(0), "invalid owner");
            require(!isOwner[owner], "owner not unique"); // 确保签名者 不重复 

            isOwner[owner] = true;
            owners.push(owner);
        }

        numConfirmationsRequired = _numConfirmationsRequired;
    }

    // 实现 receive 函数, 可接受 ETH 
    receive() external payable {
        emit Deposit(msg.sender, msg.value, address(this).balance);
    }

    // 提交交易 
    function submitTransaction(
        address _to,
        uint _value,
        bytes memory _data
    ) public onlyOwner {
        uint txIndex = transactions.length;

        transactions.push(
            Transaction({
                to: _to,
                value: _value,
                data: _data,
                executed: false,
                numConfirmations: 0
            })
        );

        emit SubmitTransaction(msg.sender, txIndex, _to, _value, _data);
    }

    // 确认交易 
    function confirmTransaction(uint _txIndex)
        public
        onlyOwner
        txExists(_txIndex)
        notExecuted(_txIndex)
        notConfirmed(_txIndex)
    {
        Transaction storage transaction = transactions[_txIndex];
        transaction.numConfirmations += 1;
        isConfirmed[_txIndex][msg.sender] = true;

        emit ConfirmTransaction(msg.sender, _txIndex);
    }

    // 执行交易
    function executeTransaction(uint _txIndex)
        public
        onlyOwner
        txExists(_txIndex)
        notExecuted(_txIndex)
    {
        Transaction storage transaction = transactions[_txIndex];

        require(
            transaction.numConfirmations >= numConfirmationsRequired,
            "cannot execute tx"
        );

        transaction.executed = true;

        (bool success, ) = transaction.to.call{value: transaction.value}(
            transaction.data
        );
        require(success, "tx failed");

        emit ExecuteTransaction(msg.sender, _txIndex);
    }

    // 驳回交易 
    function revokeConfirmation(uint _txIndex)
        public
        onlyOwner
        txExists(_txIndex)
        notExecuted(_txIndex)
    {
        Transaction storage transaction = transactions[_txIndex];

        require(isConfirmed[_txIndex][msg.sender], "tx not confirmed");

        transaction.numConfirmations -= 1;
        isConfirmed[_txIndex][msg.sender] = false;

        emit RevokeConfirmation(msg.sender, _txIndex);
    }

    // 获得多签者 
    function getOwners() public view returns (address[] memory) {
        return owners;
    }

    // 获得交易数量
    function getTransactionCount() public view returns (uint) {
        return transactions.length;
    }

    // 获得交易
    function getTransaction(uint _txIndex)
        public
        view
        returns (
            address to,
            uint value,
            bytes memory data,
            bool executed,
            uint numConfirmations
        )
    {
        Transaction storage transaction = transactions[_txIndex];

        return (
            transaction.to,
            transaction.value,
            transaction.data,
            transaction.executed,
            transaction.numConfirmations
        );
    }
}
```

### 测试调用合约

这是一个测试从多重签名钱包发送交易的合约

通过以下合约部署完成后, 用多签名调用该合约的方法, 修改函数内容 i 的值. 

最终实现, 通过多重签名授权的方式, 进行合约交互操作的功能. 

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract TestContract {
    uint public i;

    function callMe(uint j) public {
        i += j;
    }

    function getData() public pure returns (bytes memory) {
        return abi.encodeWithSignature("callMe(uint256)", 123);
    }
}
```

### 补充知识

知识点: 数组的值赋给结构体变量, 该变量的值被修改后, 数组内元素同样被修改

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract TestContract {
    
    // 测试数据
    struct Transaction {
        address to;
        uint value;
    }

    // 数据 数组
    Transaction[] public transactions;

    // 初始化 赋值 
    constructor () {
        for (uint i = 0; i < 4; i++) {
            transactions.push(
                Transaction({
                    to: address(0),
                    value: 6666
                })
            );
        }
    }


    // 改变数组的值 
    // 知识点: 数组的值赋给结构体变量, 该变量的值被修改后, 数组内元素同样被修改 
    function doChangeArray(uint _txIndex)
        public
    {
        Transaction storage transaction = transactions[_txIndex];
        transaction.to = address(0);
        transaction.value = _txIndex;
    }

    // 获得交易
    function getTransaction(uint _txIndex)
        public
        view
        returns (
            address to,
            uint value
        )
    {
        Transaction storage transaction = transactions[_txIndex];
        return (
            transaction.to,
            transaction.value
        );
    }

}
```

### 关注更多 

Web3 技术与应用，研究实践分享！
 

*Twitter*:    [@AntCaveClub](https://twitter.com/AntCaveClub)

*蚁穴Web3社区*:  [https://antcave.club/](https://antcave.club/)

