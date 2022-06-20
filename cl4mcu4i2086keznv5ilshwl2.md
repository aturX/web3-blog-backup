## Solidity 教程:  基础知识


## 1. 第一个Solidity 程序:   Hello World 

```
// SPDX-License-Identifier: MIT
// compiler version must be greater than or equal to 0.8.13 and less than 0.9.0
pragma solidity ^0.8.13;

contract HelloWorld {
    string public greet = "Hello World!";
}
```

上面的一段Solidity程序, 涉及三个部分:   

- 开源协议声明
```
// SPDX-License-Identifier: MIT
```

      这里我们采用的开源协议是 [MIT 协议](https://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html)


- Solidity编译版本声明

```
pragma solidity ^0.8.13;
```

        这里我们采用的 [编译器版本](https://solidity-cn.readthedocs.io/zh/develop/installing-solidity.html#id1)是 `^0.8.13` . 


- `合约创建`及`变量创建`
```
contract HelloWorld {
    string public greet = "Hello World!";
}
```

这里创建了一个合约`HelloWorld`, 在合约的内部进行了变量`greet`的定义和赋值. 
在变量之前, 还存在类型声明`string`, 访问范围是`public`就表示可以在合约外部被任意调用查看.  





## 2. 完成一个简单合约:  计数器  

该合约实现功能:   你可以在该合约中获取、递增和递减计数并存储。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Counter {
    uint public count;

    // Function to get the current count
    function get() public view returns (uint) {
        return count;
    }

    // Function to increment count by 1
    function inc() public {
        count += 1;
    }

    // Function to decrement count by 1
    function dec() public {
        // This function will fail if count = 0
        count -= 1;
    }
}
``` 

在有了上节的基础知识后, 可以看到该段代码中, 主要新增的要点是 函数`function`. 

函数定义时, 采用`public`表示外部可以随意调用, 加了`view`修饰符表示该函数内部, 不修改数据状态, 只是读取了变量的数据. 

`returns(uint)` 表示函数具有返回值, 并且返回值的类型是`uint` .



## 3. 原始数据类型:  Primitive Data Types

在Solidity中, 原始数据一般包含下面的内容: 

- boolean
- int
- uint
- address
- bytes32

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Primitives {
    bool public boo = true;

    /*
    uint 代表无符号整数，意思是非负整数
    有不同的大小可供选择

        uint8   范围在 0 到 2 ** 8 - 1
        uint16  范围在 0 到 2 ** 16 - 1
        ...
        uint256 范围在 0 到 2 ** 256 - 1
    */

    uint8 public u8 = 1;
    uint public u256 = 456;
    uint public u = 123; // uint 就是 uint256 的别名

    /*
    int 类型允许使用负数。
    与 uint 一样，从 int8 到 int256 都有不同的范围可用
    
    int256  范围从 -2 ** 255 to 2 ** 255 - 1
    int128   范围从 -2 ** 127 to 2 ** 127 - 1
    */

    int8 public i8 = -1;
    int public i256 = 456;
    int public i = -123;    // int 默认就是 int256

    // int 的最小值和最大值 
    int public minInt = type(int).min;
    int public maxInt = type(int).max;

    address public addr = 0xCA35b7d915458EF540aDe6068dFe2F44E8fa733c;

    /*
    在 Solidity 中，数据类型 byte 表示一个字节序列。
    Solidity 提供了两种字节类型：

     - 固定大小的字节数组
     - 动态大小的字节数组。
     
     Solidity 中的 bytes 表示一个动态的字节数组。
     它是 byte[] 的简写。
    */
    bytes1 a = 0xb5;        //  [10110101]
    bytes1 b = 0x56;       //  [01010110]
    bytes32 public  b32 = 0x89c58ced8a9078bdef2bb60f22e58eeff7dbfed6c2dff3e7c508b629295926fa;


    // 默认变量值
    // 变量定义时 如果不赋值, 将会有一个默认值 

    bool public defaultBoo;               // false
    uint public defaultUint;                // 0
    int public defaultInt;                    // 0
    address public defaultAddr;       // 0x0000000000000000000000000000000000000000
}
```


## 4. 变量、常量、不可变量


- 变量 

对于Solidity中, 变量会分为三种:  本地变量(local) 、 状态变量(state)、 全局变量(global)  

```

/* 
1.  本地变量(local)
  - 在函数内部声明
  - 不存储在区块链上

2.  状态变量(state)
  - 在函数外声明
  - 存储在区块链上

3.  全局变量(global)  
  - 提供有关区块链的信息:  区块高度、时间戳等
 */

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Variables {
    // 状态变量 State variables ,   它们被存储在区块链上
    string public text = "Hello";
    uint public num = 123;

    function doSomething() public {
        // 本地变量, 不存储在区块链上
        uint i = 456;

        // 全局变量, 可以直接被查询出
        uint timestamp = block.timestamp;    //   当前时间戳
        address sender = msg.sender;           //    掉用者的地址
    }
}
```


- 常量 

常量是不能修改的变量。

它们的值是硬编码的，使用常量可以节省 gas 成本。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Constants {
    // 用大写字母 命名常量 
    address public constant MY_ADDRESS = 0x777788889999AaAAbBbbCcccddDdeeeEfFFfCcCc;
    uint public constant MY_UINT = 123;
}

```


- 不可变量

不可变变量就像常量。不可变变量的值可以在构造函数中设置，但之后不能修改。

```

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Immutable {
    // 用大写字母 命名不可变变量 
    address public immutable MY_ADDRESS;
    uint public immutable MY_UINT;

    constructor(uint _myUint) {
        MY_ADDRESS = msg.sender;
        MY_UINT = _myUint;
    }
}
```



## 5. 状态变量的读、写、存储

要写入或更新状态变量，你需要发送交易。

另一方面，你可以免费读取状态变量，无需任何交易费用。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract SimpleStorage {
    // 状态变量 存储一个数字
    uint public num;

    // 你需要发起一个交易, 调用set 函数修改 num 的值 
    function set(uint _num) public {
        num = _num;
    }

    // 你可以读取一个状态变量, 但不需要发起交易
    function get() public view returns (uint) {
        return num;
    }
}
```



## 6. Solidity中的单位:    Ether 和  Wei

发起交易时, 需要花费费用, 支付采用 `ether` 即 `ETH`. 

类似于1美元等于 100 美分，`1 ether` 等于 `10**18 wei`。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract EtherUnits {
    uint public oneWei = 1 wei;
    // 1 wei 就是 1
    bool public isOneWei = 1 wei == 1;

    uint public oneEther = 1 ether;
    // 1 ether 就是 10^18 wei
    bool public isOneEther = 1 ether == 1e18;
}
```



## 7. 需要为交易支付多少`ether`?   Gas 和 Gas Price


- 你需要为交易支付多少`ether`费用？

你支付 `gas spent * gas price` 的`ether`金额，如何计算? 

- `gas`  是一个计算单位
- `gas spent` 是交易中使用 `gas` 的总量 
- `gas price` 是你愿意支付为每个 `gas` 支付的`ether` 价格

具有较高gas价格的交易具有更高的优先级被打包在一个区块中。

未用完的gas将被退还。

- `gas limit` 气体限制

你可以所有花费的gas量由 2 个上限所限制:

- `gas limit`（你愿意为该交易使用的最大gas量，由你自己设置, 如果超出设置量,交易失败）

- `block gas limit`（区块中允许的最大gas量，由网络设置）

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Gas {
    uint public i = 0;

    // 消耗完所有的gas , 交易会失败
    // 状态变量不会被修改
    // 消耗掉的gas 不会退还

    function forever() public {
        // 这里设置了一个死循环, 将所有的 gas 消耗完了
        // 最终交易会失败


        while (true) {
            i += 1;
        }
    }
}
```


## 8. 逻辑判断:  If / Else

Solidity 支持条件语句 `if`, `else if` 和 `else`.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract IfElse {
    function foo(uint x) public pure returns (uint) {
        // 根据条件, 进行判断
        if (x < 10) {
            return 0;
        } else if (x < 20) {
            return 1;
        } else {
            return 2;
        }
    }

    function ternary(uint _x) public pure returns (uint) {
        // if (_x < 10) {
        //     return 1;
        // }
        // return 2;

        // if / else 语句的简写方式 
        // 这 ”？”运算符称为三元运算符

        return _x < 10 ? 1 : 2;
    }
}
```


## 9. 循环语法:  For 和 While

Solidity 支持 `for`, `while`, 还有 `do while` 循环方式.

不要编写无限循环，因为这可能会达到 gas 限制，从而导致你的交易失败。

由于上述原因，很少使用 `while` 和 `do while` 循环。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Loop {
    function loop() public {
        // for loop
        for (uint i = 0; i < 10; i++) {
            if (i == 3) {
                // 跳过后续逻辑, 继续下一次循环
                continue;
            }
            if (i == 5) {
                // 直接退出整个循环
                break;
            }
        }

        // while loop
        uint j;
        while (j < 10) {
            j++;
        }
    }
}
```


## 10. 映射: Mapping

Mapping 的创建语法是 `mapping(keyType => valueType)`。

keyType 可以是任何内置值类型、字节、字符串或任何合约。

valueType 可以是任何类型，包括另一个mapping或array。

Mapping 是不可迭代的。

```

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Mapping {
    // 映射,  地址 => 数字     通过地址,访问对应数字 
    mapping(address => uint) public myMap;

    function get(address _addr) public view returns (uint) {
        // 映射总是 返回一个值
        // 如果未设定值, 就会返回默认值
        return myMap[_addr];
    }

    function set(address _addr, uint _i) public {
        // 更新这个地址映射的值 
        myMap[_addr] = _i;
    }

    function remove(address _addr) public {
        // 删除, 也就是重置映射的值 为默认值
        delete myMap[_addr];
    }
}

contract NestedMapping {
    // 嵌套映射, 通过地址映射另一个映射  Nested mapping (mapping from address to another mapping)
    mapping(address => mapping(uint => bool)) public nested;

    function get(address _addr1, uint _i) public view returns (bool) {
        // 你可以通过嵌套 映射获得 多个值 
        return nested[_addr1][_i];
    }

    function set(
        address _addr1,
        uint _i,
        bool _boo
    ) public {
        nested[_addr1][_i] = _boo;
    }

    function remove(address _addr1, uint _i) public {
        delete nested[_addr1][_i];
    }
}
```


## 11. 数组: Array

数组可以分成两类,  固定大小或动态大小。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Array {
    // 初始化数组的几种方式 
    uint[] public arr;
    uint[] public arr2 = [1, 2, 3];

    // 固定大小的数组,  所有元素初始化为 0 
    uint[10] public myFixedSizeArr;

    function get(uint i) public view returns (uint) {
        return arr[i];
    }

    // Solidity 可以返回整个数组 
    // 下面这个函数应该避免这样做, 因为数组的长度不确定, 大小无限
    function getArr() public view returns (uint[] memory) {
        return arr;
    }

    function push(uint i) public {
        // 添加值 到数组中
        // 这将会使得数组长度 增加 1 
        arr.push(i);
    }

    function pop() public {
        // 删除最近的一个元素, 从数组中
        // 这将使得数组的长度减少 1 
        arr.pop();
    }

    function getLength() public view returns (uint) {
        return arr.length;
    }

    function remove(uint index) public {
        // 删除 不会改变数组长度
        // 它实际上是将数组对应的值重置为默认值
        // 比如下面  值将会变为 0 
        delete arr[index];
    }

    function examples() external {
        // 在 memory 内存中创建数组,  只能固定大小的数组被创建. 
        uint[] memory a = new uint[](5);
    }
}
```

#### 删除数组元素的两种方式: 

- 通过从右向左移动元素来删除数组元素

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ArrayRemoveByShifting {
    // [1, 2, 3] -- remove(1) --> [1, 3, 3] --> [1, 3]
    // [1, 2, 3, 4, 5, 6] -- remove(2) --> [1, 2, 4, 5, 6, 6] --> [1, 2, 4, 5, 6]
    // [1, 2, 3, 4, 5, 6] -- remove(0) --> [2, 3, 4, 5, 6, 6] --> [2, 3, 4, 5, 6]
    // [1] -- remove(0) --> [1] --> []

    uint[] public arr;

    function remove(uint _index) public {
        require(_index < arr.length, "index out of bound");

        for (uint i = _index; i < arr.length - 1; i++) {
            arr[i] = arr[i + 1];
        }
        arr.pop();
    }

    function test() external {
        arr = [1, 2, 3, 4, 5];
        remove(2);
        // [1, 2, 4, 5]
        assert(arr[0] == 1);
        assert(arr[1] == 2);
        assert(arr[2] == 4);
        assert(arr[3] == 5);
        assert(arr.length == 4);

        arr = [1];
        remove(0);
        // []
        assert(arr.length == 0);
    }
}
```

- 通过将最后一个元素复制到要删除的位置来删除数组元素

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ArrayReplaceFromEnd {
    uint[] public arr;

    // 将数组最后一个值 赋值给 要删除的元素, 
    // 然后,删除最后一个值
    // 虽然数组顺序被打乱了, 但是指定元素还是被删除掉了. 
    // 这样和上面一种方法比较, 减少了操作步骤
    function remove(uint index) public {
        // Move the last element into the place to delete
        arr[index] = arr[arr.length - 1];
        // Remove the last element
        arr.pop();
    }

    // 测试函数 是否符合预期结果 
    function test() public {
        arr = [1, 2, 3, 4];

        remove(1);
        // [1, 4, 3]
        assert(arr.length == 3);
        assert(arr[0] == 1);
        assert(arr[1] == 4);
        assert(arr[2] == 3);

        remove(2);
        // [1, 4]
        assert(arr.length == 2);
        assert(arr[0] == 1);
        assert(arr[1] == 4);
    }
}
```



## 12. 枚举: Enum

Solidity 支持枚举，它们对于建模选择和跟踪状态很有用。

枚举可以在合约之外声明, 所有可采用文件导入的方式使用。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Enum {
    // 用枚举跟踪购物状态 

    enum Status {
        Pending,
        Shipped,
        Accepted,
        Rejected,
        Canceled
    }

    // 枚举的默认值, 是列出的变量中的第一个
    //  在这里就是 “Pending”
    Status public status;

    // 返回类型:  uint
    // Pending  - 0
    // Shipped  - 1
    // Accepted - 2
    // Rejected - 3
    // Canceled - 4

    function get() public view returns (Status) {
        return status;
    }

    // 通过 input 输入变量, 更新状态 
    function set(Status _status) public {
        status = _status;
    }

    // 你可以通过下面的方式,重新更新成特定的枚举选项
    function cancel() public {
        status = Status.Canceled;
    }

    // 删除枚举, 就是将其值变为第一个 元素, 即 0
    function reset() public {
        delete status;
    }
}

```
声明和导入枚举

- 声明枚举的文件 👇

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
// 该文件保存为 'EnumDeclaration.sol'

enum Status {
    Pending,
    Shipped,
    Accepted,
    Rejected,
    Canceled
}
```
导入上述枚举的文件

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "./EnumDeclaration.sol";

contract Enum {
    Status public status;
}
```



## 13. 结构体: Structs

你可以创建一个`struct`.

它们对于将相关数据分组在一起很有用。

结构可以在合约之外声明并在另一个合约中导入。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Todos {

     // 声明了 结构体
    struct Todo {
        string text;
        bool completed;
    }

    //   'Todo' 结构体的数组 
    Todo[] public todos;

    function create(string calldata _text) public {
        // 3 种方式, 初始化结构体 

        // - 像一个函数一样  直接调用它
        todos.push(Todo(_text, false));

        // - 通过健值对, 进行初始化
        todos.push(Todo({text: _text, completed: false}));

        // - 初始化一个空的结构体, 然后更新它的值  
        Todo memory todo;
        todo.text = _text;
  
        todos.push(todo);
    }

    // Solidity 会自动为 todos 创建 getter 函数 
    //  实际情况下, 你不需要下面这个函数 
    function get(uint _index) public view returns (string memory text, bool completed) {
        Todo storage todo = todos[_index];
        return (todo.text, todo.completed);
    }

    // 更新 text 内容 
    function updateText(uint _index, string calldata _text) public {
        Todo storage todo = todos[_index];
        todo.text = _text;
    }

    // 更新 completed 值 
    function toggleCompleted(uint _index) public {
        Todo storage todo = todos[_index];
        todo.completed = !todo.completed;
    }
}

```

#### 声明和导入结构体

- 声明结构体的文件

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
// This is saved 'StructDeclaration.sol'

struct Todo {
    string text;
    bool completed;
}

```

导入上述结构的文件

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "./StructDeclaration.sol";

contract Todos {
    // An array of 'Todo' structs
    Todo[] public todos;
}
```




## 14. 数据存储位置:   Storage, Memory, Calldata

变量的声明被分为三种: `storage` , `memory`, `calldata` 

- storage:  该变量是状态变量（存储在区块链上）
- memory:  该变量在内存中，并且在调用函数时存在
- calldata: 包含函数参数的特殊数据位置

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract DataLocations {
    uint[] public arr;
    mapping(uint => address) map;
    struct MyStruct {
        uint foo;
    }
    mapping(uint => MyStruct) myStructs;

    function f() public {
        // 调用 _f 函数, 传入 状态变量
        _f(arr, map, myStructs[1]);

        // 从 mapping 中获得 一个结构体
        MyStruct storage myStruct = myStructs[1];

        // 在 内存中 创建一个 结构体
        MyStruct memory myMemStruct = MyStruct(0);
    }

    function _f(
        uint[] storage _arr,
        mapping(uint => address) storage _map,
        MyStruct storage _myStruct
    ) internal {
        // 对传入的 “状态变量” 进行操作
    }

    // 函数 返回 “内存变量”
    function g(uint[] memory _arr) public returns (uint[] memory) {
        // do something with memory array
    }

    function h(uint[] calldata _arr) external {
        //  对传入的 “参数变量” 进行操作
    }
}

```


## 15. 函数:  Function 及其 View、 Pure 修饰符

- 有几种方法可以从函数返回输出。

- 公共函数不能接受某些数据类型作为输入或输出


```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Function {

    //  函数可以 返回多个变量 
    function returnMany()
        public
        pure
        returns (
            uint,
            bool,
            uint
        )
    {
        return (1, true, 2);
    }

    //  返回的变量 可以被命名
    function named()
        public
        pure
        returns (
            uint x,
            bool b,
            uint y
        )
    {
        return (1, true, 2);
    }

    // 返回的变量 可以被自动分配, 并且return部分可以被省略, 如下所示 
    function assigned()
        public
        pure
        returns (
            uint x,
            bool b,
            uint y
        )
    {
        x = 1;
        b = true;
        y = 2;
    }

    // 当调用别的函数, 获得返回值, 可用解构的方式 赋值
    // 函数也可以返回多个变量值
    function destructuringAssignments()
        public
        pure
        returns (
            uint,
            bool,
            uint,
            uint,
            uint
        )
    {
        (uint i, bool b, uint j) = returnMany();

        // Values can be left out.
        (uint x, , uint y) = (4, 5, 6);

        return (i, b, j, x, y);
    }

    // 输入和输出 都不可以使用 map 

    // 可以使用 数组 作为输入值
    function arrayInput(uint[] memory _arr) public {}

    // 可以使用数组 作为输出值
    uint[] public arr;

    function arrayOutput() public view returns (uint[] memory) {
        return arr;
    }
}


```

- 视图 view 和纯函数 pure 

可以声明 Getter 函数为 view 或 pure.

View函数声明不会更改任何状态。

Pure函数声明不会更改或读取任何状态变量。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ViewAndPure {
    uint public x = 1;

    // 函数未修改任何值
    function addToX(uint y) public view returns (uint) {
        return x + y;
    }

    // 函数未修改和读取任何值
    function add(uint i, uint j) public pure returns (uint) {
        return i + j;
    }
}
```


## 16. 报错处理:  Error

错误发生后, 将撤消事务期间对状态所做的所有更改。

你可以通过调用 require , assert , revert来, 引发错误。


- `require` 用于在执行前验证输入和条件。

- `revert` 类似于 `require`。有关详细信息，请参阅下面的代码。

- `assert` 用于检查不应该为假的代码。断言失败可能意味着存在错误。
使用自定义错误来节省气体。


```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Error {
    function testRequire(uint _i) public pure {
        // Require 被使用的条件是 :
        // - “输入值” 
        // - 符合执行的“判断条件”
        // - 其他值
        require(_i > 10, "Input must be greater than 10");
    }

    function testRevert(uint _i) public pure {
        // 当要检查的条件很复杂时，Revert 很有用。
        // 这段代码和上面的例子功能效果完全一样
        if (_i <= 10) {
            revert("Input must be greater than 10");
        }
    }

    uint public num;

    function testAssert() public view {
        // 断言只能用于测试内部错误，
        // 并检查不变量。

        // 这里我们断言num总是等于0
        // 因为不可能更新 num 的值
        assert(num == 0);
    }

    // 自定义错误 
    error InsufficientBalance(uint balance, uint withdrawAmount);

    function testCustomError(uint _withdrawAmount) public view {
        uint bal = address(this).balance;
        if (bal < _withdrawAmount) {
            revert InsufficientBalance({balance: bal, withdrawAmount: _withdrawAmount});
        }
    }
}
```


## 17. 函数修饰器:  Function Modifier

修饰符是可以在函数调用之前和/或之后运行的代码。

修饰符可用于：

- 限制访问
- 验证输入
- 防范重入攻击 

```

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract FunctionModifier {
    // 通过这些变量 演示如何使用  “修饰符” 
    // modifiers.
    address public owner;
    uint public x = 10;
    bool public locked;

    constructor() {
        //  设置 调用者地址 为owner (即 合约部署者地址) 
        owner = msg.sender;
    }

    // 修饰符检查调用者 是否是合约的 owner 
    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        // 下划线是只在内部使用的特殊字符
        // 一个函数修饰符，它会告诉 Solidity 执行此处的代码。
        _;
    }

    // 修饰符可以接受输入。这个修饰符检查
    // 传入的地址不是零地址。
    modifier validAddress(address _addr) {
        require(_addr != address(0), "Not valid address");
        _;
    }

    function changeOwner(address _newOwner) public onlyOwner validAddress(_newOwner) {
        owner = _newOwner;
    }

 
    // 修饰符可以在函数之前和/或之后调用。
    // 这个修饰符防止函数被 while 重复调用
    // 它仍在执行中
    modifier noReentrancy() {
        require(!locked, "No reentrancy");

        locked = true;
        _;
        locked = false;
    }

    function decrement(uint i) public noReentrancy {
        x -= i;

        if (i > 1) {
            // 在这里 函数被重复 调用, 由于修饰符的作用, 此处会报错 
            decrement(i - 1);
        }
    }
}
```




## 18. 事件:  Events

events 可以记录以太坊区块链上的日志信息, 再链下的一些业务中被广泛的使用。

事件的一些用例是：

- 监听事件和更新用户界面信息
- 一种低成本的存储方式

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Event {
    // 事件声明
    // 最多可以索引 3 个参数。
    // 索引参数帮助你通过索引参数过滤日志

    event Log(address indexed sender, string message);
    event AnotherLog();

    function test() public {
        emit Log(msg.sender, "Hello World!");
        emit Log(msg.sender, "Hello EVM!");
        emit AnotherLog();
    }
}
```


## 19. 构造函数:  Constructor

constructor 函数是在合约创建时执行的可选函数。

以下是如何将参数传递给constructors.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// 基础合约 X 
contract X {
    string public name;

    constructor(string memory _name) {
        name = _name;
    }
}

// 基础合约 Y
contract Y {
    string public text;

    constructor(string memory _text) {
        text = _text;
    }
}

// 有两种方法初始化 父合约的 参数

// 直接在继承列表中传递参数。
contract B is X("Input to X"), Y("Input to Y") {

}

contract C is X, Y {
    // 在构造函数 传入时写入参数
    // 就类似函数修饰符一样
    constructor(string memory _name, string memory _text) X(_name) Y(_text) {}
}


// 总是按照“继承顺序”调用父构造函数
// 无论父合约中列出的顺序如何
// 最后调用 子合约的构造函数

// 构造函数调用的顺序:
// 1.  X
// 2. Y
// 3. D
contract D is X, Y {
    constructor() X("X was called") Y("Y was called") {}
}

// 构造函数调用的顺序:
// 1. X
// 2. Y
// 3. E
contract E is X, Y {
    constructor() Y("Y was called") X("X was called") {}
}
```


## 20. 继承: Inheritance

Solidity 支持多重继承。合约可以使用`is`关键字继承其他合约。

将被子合约覆盖的函数必须声明为`virtual`.

将要覆盖父函数的函数必须使用关键字`override`。

继承顺序很重要。

必须按照从“最基础”到“最衍生”的顺序​​列出父合约。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

/* 继承关系图
    A
   / \
  B   C
 / \ /
F  D,E

*/

contract A {
    function foo() public pure virtual returns (string memory) {
        return "A";
    }
}

// 合约通过使用关键字“is”继承其他合约。
contract B is A {
    // 重写 A.foo()
    function foo() public pure virtual override returns (string memory) {
        return "B";
    }
}

contract C is A {
    // 重写 A.foo()
    function foo() public pure virtual override returns (string memory) {
        return "C";
    }
}

// 合约可以从多个父合约继承。
// 当一个函数被调用时，该函数被多次定义
// 不同的合约，从父合约中搜索
// 从右到左，深度优先。

contract D is B, C {
    // D.foo() 返回 "C"
    // 因为 C 是继承顺序最右边的 
    function foo() public pure override(B, C) returns (string memory) {
        return super.foo();
    }
}

contract E is C, B {
    // E.foo() 返回 "B"
    // 因为 B 是继承顺序最右边的
    function foo() public pure override(C, B) returns (string memory) {
        return super.foo();
    }
}

// 继承必须从“最基类”到“最派生”排序。
// 交换 A 和 B 的顺序会抛出编译错误。
contract F is A, B {
    function foo() public pure override(A, B) returns (string memory) {
        return super.foo();
    }
}
```


## 21:  覆盖继承的状态变量 

与函数不同，状态变量不能通过在子合约中重新声明来覆盖。

让我们学习如何正确覆盖继承的状态变量。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract A {
    string public name = "Contract A";

    function getName() public view returns (string memory) {
        return name;
    }
}

// 覆盖 状态变量 在 Solidity 0.6 之前是禁止被使用的
// 将会无法通过编译 
// contract B is A {
//     string public name = "Contract B";
// }

contract C is A {
    // 这是覆盖继承的状态变量的正确方法。
    constructor() {
        name = "Contract C";
    }

    // C.getName returns "Contract C"
}

```


## 22.  调用父合约:  Calling Parent Contracts

可以直接调用父合约，也可以使用关键字`super`.

通过使用关键字super，将调用所有直接父合约。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

/* 继承树
   A
 /  \
B   C
 \ /
  D
*/

contract A {
    //这是一个调用事件, 这里的信息会被记录在区块链日志中, 便于索引
    event Log(string message);

    function foo() public virtual {
        emit Log("A.foo called");
    }

    function bar() public virtual {
        emit Log("A.bar called");
    }
}

contract B is A {
    function foo() public virtual override {
        emit Log("B.foo called");
        A.foo();
    }

    function bar() public virtual override {
        emit Log("B.bar called");
        super.bar();
    }
}

contract C is A {
    function foo() public virtual override {
        emit Log("C.foo called");
        A.foo();
    }

    function bar() public virtual override {
        emit Log("C.bar called");
        super.bar();
    }
}

contract D is B, C {
    // Try:
    // - 调用 D.foo 检查交易 log 
    //   虽然 D 继承了 A, B 和 C,但是它只是被 C 调用了, 然后是 A.
    // - 调用 D.bar 检查交易 log 
    //   D 调用了 C, 然后是 B,最后是 A.
    //  虽然 super被 (B 和 C)调用了两次, 但是它只被 A 调用了一次.

    function foo() public override(B, C) {
        super.foo();
    }

    function bar() public override(B, C) {
        super.bar();
    }
}

```



## 23.  可见性: Visibility

函数和状态变量必须声明它们是否可以被其他合约访问。

函数可以声明为

- `public` 任何合约和账户都可以调用
- `private` 仅在定义函数的合约内
- `internal` 仅在继承`internal`函数的内部合约
- `external` 只有其他合约和账户可以调用

状态变量可以声明为public,private或internal但不是external。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Base {
    // 私有函数只能在这个合约里面被调用
    // 继承这个合约的合约不能调用这个函数。
    function privateFunc() private pure returns (string memory) {
        return "private function called";
    }

    function testPrivateFunc() public pure returns (string memory) {
        return privateFunc();
    }


    // 可以调用内部函数需满足: 
    // - 在这个合约里面
    // - 继承此合约的内部合约
    function internalFunc() internal pure returns (string memory) {
        return "internal function called";
    }

    function testInternalFunc() public pure virtual returns (string memory) {
        return internalFunc();
    }

    // 可以调用公共函数需满足: 
    // - 在这个合约里面
    // - 继承此合约的内部合约
    // - 通过其他合约和账户
    function publicFunc() public pure returns (string memory) {
        return "public function called";
    }


    // 调用外部函数需满足
    // - 通过其他合约和账户
    function externalFunc() external pure returns (string memory) {
        return "external function called";
    }

    // 这个函数不会被编译，因为我们试图调用一个外部函数。
    // function testExternalFunc() public pure returns (string memory) {
    //     return externalFunc();
    // }

    // 状态变量对访问访问 State variables
    string private privateVar = "my private variable";
    string internal internalVar = "my internal variable";
    string public publicVar = "my public variable";

    // 状态变量不能是外部的，所以这段代码不会编译。
    // string external externalVar = "my external variable";
}

contract Child is Base {
 
    // 继承的合约不能访问私有函数和状态变量
    // function testPrivateFunc() public pure returns (string memory) {
    //     return privateFunc();
    // }

    // 在子合约内部调用内部函数。
    function testInternalFunc() public pure override returns (string memory) {
        return internalFunc();
    }
}

```

## 24.  接口: Interface

你可以通过`Interface`声明一个接口.

Interface

- 不能实现任何功能
- 可以从其他接口继承
- 所有声明的函数必须是外部的
- 不能声明构造函数
- 不能声明状态变量


```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Counter {
    uint public count;

    function increment() external {
        count += 1;
    }
}

interface ICounter {
    function count() external view returns (uint);

    function increment() external;
}

contract MyContract {
    function incrementCounter(address _counter) external {
        ICounter(_counter).increment();
    }

    function getCount(address _counter) external view returns (uint) {
        return ICounter(_counter).count();
    }
}

// Uniswap example
interface UniswapV2Factory {
    function getPair(address tokenA, address tokenB)
        external
        view
        returns (address pair);
}

interface UniswapV2Pair {
    function getReserves()
        external
        view
        returns (
            uint112 reserve0,
            uint112 reserve1,
            uint32 blockTimestampLast
        );
}

contract UniswapExample {
    address private factory = 0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f;
    address private dai = 0x6B175474E89094C44Da98b954EedeAC495271d0F;
    address private weth = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;

    function getTokenReserves() external view returns (uint, uint) {
        address pair = UniswapV2Factory(factory).getPair(dai, weth);
        (uint reserve0, uint reserve1, ) = UniswapV2Pair(pair).getReserves();
        return (reserve0, reserve1);
    }
}

```


## 25.  支付: Payable

`payable` 声明的函数和地址可以接收`ether`到合约中。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Payable {
    // Payable 修饰的地址可以接收 Ether
    address payable public owner;

    // Payable 修饰的 constructor 可以接收 Ether
    constructor() payable {
        owner = payable(msg.sender);
    }

    // 将以太币存入该合约的函数。
    // 与一些 Ether 一起调用此函数。
    // 该合约的余额会自动更新。
    function deposit() public payable {}

    // 与一些 Ether 一起调用此函数。
    // 该函数将抛出一个错误，因为该函数是不可支付的。
    function notPayable() public {}


   // 从该合约中提取所有以太币的函数。
    function withdraw() public {
        
        // 获取存储在该合约中的以太币数量
        uint amount = address(this).balance;

       
        // 将所有以太币发送给所有者
        // 所有者可以收到以太币，因为所有者的地址是可支付的
        (bool success, ) = owner.call{value: amount}("");
        require(success, "Failed to send Ether");
    }

    // 将以太币从这个合约转移到输入地址的函数
    function transfer(address payable _to, uint _amount) public {
        // 请注意，“to”被声明为应付
        (bool success, ) = _to.call{value: _amount}("");
        require(success, "Failed to send Ether");
    }
}

```


## 26.  转账: Sending Ether - Transfer, Send, 和 Call

### 如何发送以太币？
你可以通过以下方式将以太币发送到其他合约

- `transfer`（2300 gas，抛出错误）

- `send`（2300 gas，返回布尔值）

- `call`（转发所有gas或设置gas，返回布尔值）

### 如何接收以太币？

接收 `Ether` 的合约必须至少具有以下功能之一

- `receive() external payable`

- `fallback() external payable`

- 如果`msg.data`为空则调用`receive()` ，否则调用`fallback()`。

### 应该使用哪种方法？
`call` 是在 2019 年 12 月后推荐使用与重入防护结合使用的方法。

通过以下方式防止重新进入

在调用其他合约之前进行所有状态更改
使用重入保护修饰符

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ReceiveEther {
    /*
    那个函数被调用了?  是 fallback() 还是 receive()?

              send Ether
                      |
         msg.data  是否为空?
              /              \
            是              否
            /                   \
receive() 存在?       fallback()
         /            \
        是           否
        /                \
    receive()   fallback()
    */

    // 执行 receive 函数时,  msg.data 必须是空的
    receive() external payable {}

    // 当 msg.data 不是空的时候,  Fallback  函数被调用 
    fallback() external payable {}

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}

contract SendEther {

    function sendViaTransfer(address payable _to) public payable {
        // 这个函数不再建议用来发送ETH 
        _to.transfer(msg.value);
    }

    function sendViaSend(address payable _to) public payable {
        // 发送结果会返回一个 布尔类型, 成功或者失败
        // 这个函数也不建议用来发送 ETH  
        bool sent = _to.send(msg.value);
        require(sent, "Failed to send Ether");
    }

    function sendViaCall(address payable _to) public payable {
        // Call 调用返回一个布尔类型结果,  成功或者失败 
        // 这个是当前建议使用的方法
        (bool sent, bytes memory data) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}

```


## 27.  回退函数:  Fallback

`fallback` 是一个不接受任何参数且不返回任何内容的函数。

它在何时执行: 

- 调用不存在的函数

- 以太币直接发送到合约但`receive()`不存在或`msg.data`不为空
由调用`transfer`或`send`引发的`fallback`会有 2300 gas 限制。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Fallback {
    event Log(uint gas);

    // Fallback 函数必须被声明为 external 函数
    fallback() external payable {
        // send / transfer (将 2300 gas 转发到这个 fallback 函数)
        // call (将全部 gas 转发到这个 fallback 函数)
        emit Log(gasleft());
    }

    // 检查合约余额的函数
    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}

contract SendToFallback {

    // 用transfer转账给 fallback 函数
    function transferToFallback(address payable _to) public payable {
        _to.transfer(msg.value);
    }

    // 用call转账给 fallback 函数
    function callFallback(address payable _to) public payable {
        (bool sent, ) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}

```


## 28. 低级调用方法:  Call


`call` 是与其他合约交互的低级功能。

当你只是通过调用该`fallback`函数发送 Ether 时，这是推荐使用的方法。

但是，这不是调用现有函数的推荐方法。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Receiver {
    event Received(address caller, uint amount, string message);

    fallback() external payable {
        emit Received(msg.sender, msg.value, "Fallback was called");
    }

    function foo(string memory _message, uint _x) public payable returns (uint) {
        emit Received(msg.sender, msg.value, _message);

        return _x + 1;
    }
}

contract Caller {
    event Response(bool success, bytes data);

    // 假设合约 B 没有合约 A的源代码,
    // 但我们确实知道 A 的地址和要调用的函数。
    function testCallFoo(address payable _addr) public payable {
        // 你可以发送以太币并指定自定义气体量
        (bool success, bytes memory data) = _addr.call{value: msg.value, gas: 5000}(
            abi.encodeWithSignature("foo(string,uint256)", "call foo", 123)
        );

        emit Response(success, data);
    }

    // 调用不存在的函数会触发回退函数。
    function testCallDoesNotExist(address _addr) public {
        (bool success, bytes memory data) = _addr.call(
            abi.encodeWithSignature("doesNotExist()")
        );

        emit Response(success, data);
    }
}

```


## 29. 委托调用方法: Delegatecall

`delegatecall`是一个类似于 `call` 的低级函数。

当合约A执行`delegatecall` 到合约B时，B的代码会被执行, 但伴随着

合约A的存储，`msg.sender`和`msg.value`。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// 注意: 首先部署这个合约
contract B {

    // 注意:  存储格式必须与合约A相同
    uint public num;
    address public sender;
    uint public value;

    function setVars(uint _num) public payable {
        num = _num;
        sender = msg.sender;
        value = msg.value;
    }
}

contract A {
    uint public num;
    address public sender;
    uint public value;

    function setVars(address _contract, uint _num) public payable {
        // A 合约的值被存储了, B 合约没有被修改.
        (bool success, bytes memory data) = _contract.delegatecall(
            abi.encodeWithSignature("setVars(uint256)", _num)
        );
    }
}

```


## 30. 函数选择器: Function Selector

调用函数时，`calldata`前 4 个字节指定调用哪个函数。

这 4 个字节称为函数选择器。

例如，下面的这段代码。它用于在地址`addr`上`call`执行`transfer`合约。
```
addr.call(abi.encodeWithSignature("transfer(address,uint256)", 0xSomeAddress, 123))
```

`abi.encodeWithSignature(....)`返回的前 4 个字节是函数选择器。

如果在代码中预先计算并内联函数选择器，也许可​​以节省少量气体？

以下是函数选择器的计算方式。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract FunctionSelector {
    /*
    "transfer(address,uint256)"
    0xa9059cbb
    "transferFrom(address,address,uint256)"
    0x23b872dd
    */
    function getSelector(string calldata _func) external pure returns (bytes4) {
        return bytes4(keccak256(bytes(_func)));
    }
}
```


## 31. 调用其他合约: Calling Other Contract

合约可以通过 2 种方式调用其他合约。

最简单的方法就是直接调用它，比如`A.foo(x, y, z)`.

调用其他合约的另一种方法是使用低级函数`call`.

不推荐这种方法。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Callee {
    uint public x;
    uint public value;

    function setX(uint _x) public returns (uint) {
        x = _x;
        return x;
    }

    function setXandSendEther(uint _x) public payable returns (uint, uint) {
        x = _x;
        value = msg.value;

        return (x, value);
    }
}

contract Caller {
    function setX(Callee _callee, uint _x) public {
        uint x = _callee.setX(_x);
    }

    function setXFromAddress(address _addr, uint _x) public {
        Callee callee = Callee(_addr);
        callee.setX(_x);
    }

    function setXandSendEther(Callee _callee, uint _x) public payable {
        (uint x, uint value) = _callee.setXandSendEther{value: msg.value}(_x);
    }
}
  
```



## 32. 用合约创建其他合约:  Creating Contracts from a Contract

其他合约可以使用`new`关键字创建合约。从` 0.8.0 `开始，`new`关键字可以采用`create2`通过指定`salt`选项来实现此功能。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Car {
    address public owner;
    string public model;
    address public carAddr;

    constructor(address _owner, string memory _model) payable {
        owner = _owner;
        model = _model;
        carAddr = address(this);
    }
}

contract CarFactory {
    Car[] public cars;

    function create(address _owner, string memory _model) public {
        Car car = new Car(_owner, _model);
        cars.push(car);
    }

    function createAndSendEther(address _owner, string memory _model) public payable {
        Car car = (new Car){value: msg.value}(_owner, _model);
        cars.push(car);
    }

    function create2(
        address _owner,
        string memory _model,
        bytes32 _salt
    ) public {
        Car car = (new Car){salt: _salt}(_owner, _model);
        cars.push(car);
    }

    function create2AndSendEther(
        address _owner,
        string memory _model,
        bytes32 _salt
    ) public payable {
        Car car = (new Car){value: msg.value, salt: _salt}(_owner, _model);
        cars.push(car);
    }

    function getCar(uint _index)
        public
        view
        returns (
            address owner,
            string memory model,
            address carAddr,
            uint balance
        )
    {
        Car car = cars[_index];

        return (car.owner(), car.model(), car.carAddr(), address(car).balance);
    }
}

```


## 33. Try / Catch
`try / catch` 只能从外部函数调用和合约创建中捕获错误。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// 调用 External 合约时, 采用  try / catch 的例子
contract Foo {
    address public owner;

    constructor(address _owner) {
        require(_owner != address(0), "invalid address");
        assert(_owner != 0x0000000000000000000000000000000000000001);
        owner = _owner;
    }

    function myFunc(uint x) public pure returns (string memory) {
        require(x != 0, "require failed");
        return "my func was called";
    }
}

contract Bar {
    event Log(string message);
    event LogBytes(bytes data);

    Foo public foo;

    constructor() {
        // 这个 foo  合约被用作 try catch 的外部调用实例
        foo = new Foo(msg.sender);
    }

    // try / catch 的 external call 示例
    // tryCatchExternalCall(0) => Log("external call failed")
    // tryCatchExternalCall(1) => Log("my func was called")
    function tryCatchExternalCall(uint _i) public {
        try foo.myFunc(_i) returns (string memory result) {
            emit Log(result);
        } catch {
            emit Log("external call failed");
        }
    }

    // try / catch 创建 contract 的示例 
    // tryCatchNewContract(0x0000000000000000000000000000000000000000) => Log("invalid address")
    // tryCatchNewContract(0x0000000000000000000000000000000000000001) => LogBytes("")
    // tryCatchNewContract(0x0000000000000000000000000000000000000002) => Log("Foo created")
    function tryCatchNewContract(address _owner) public {
        try new Foo(_owner) returns (Foo foo) {
            // 在这里, 你可以使用变量 foo 
            emit Log("Foo created");
        } catch Error(string memory reason) {
            // catch 捕获到 revert() 和 require()
            emit Log(reason);
        } catch (bytes memory reason) {
            // catch 捕获到 assert()
            emit LogBytes(reason);
        }
    }
}

```

## 34. 导入:  Import

你可以在 Solidity 中导入本地和外部文件。

#### 本地的

这是我们的文件夹结构。

```
├── Import.sol
└── Foo.sol
```


Foo.sol

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

struct Point {
    uint x;
    uint y;
}

error Unauthorized(address caller);

function add(uint x, uint y) pure returns (uint) {
    return x + y;
}

contract Foo {
    string public name = "Foo";
}

```

导入.sol

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// 从当前目录导入  Foo.sol  文件
import "./Foo.sol";

// import {symbol1 as alias, symbol2} from "filename";
import {Unauthorized, add as func, Point} from "./Foo.sol";

contract Import {
    // 初始化 Foo.sol
    Foo public foo = new Foo();

    // 通过获得它的名字测试 Foo.sol 
    function getFooName() public view returns (string memory) {
        return foo.name();
    }
}

```

#### 外部的

你也可以通过简单地复制 `url` 从 GitHub 导入


```
// 文件 URL:  https://github.com/owner/repo/blob/branch/path/to/Contract.sol
import "https://github.com/owner/repo/blob/branch/path/to/Contract.sol";

// 例如导入 ECDSA.sol 从 openzeppelin-contract 仓库, release-v4.5 branch
// https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.5/contracts/utils/cryptography/ECDSA.sol
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.5/contracts/utils/cryptography/ECDSA.sol";

```


## 35. 库:  Library

库类似于合约，但你不能声明任何状态变量，也不能发送以太币。

如果所有库函数都是内部的，则将库嵌入到合约中。

否则，必须先部署库，然后在部署合约之前进行链接。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

library SafeMath {
    function add(uint x, uint y) internal pure returns (uint) {
        uint z = x + y;
        require(z >= x, "uint overflow");

        return z;
    }
}

library Math {
    function sqrt(uint y) internal pure returns (uint z) {
        if (y > 3) {
            z = y;
            uint x = y / 2 + 1;
            while (x < z) {
                z = x;
                x = (y / x + x) / 2;
            }
        } else if (y != 0) {
            z = 1;
        }
        // else z = 0 (default value)
    }
}

contract TestSafeMath {
    using SafeMath for uint;

    uint public MAX_UINT = 2**256 - 1;

    function testAdd(uint x, uint y) public pure returns (uint) {
        return x.add(y);
    }

    function testSquareRoot(uint x) public pure returns (uint) {
        return Math.sqrt(x);
    }
}

// 数组函数删除索引处的元素并重新组织数组
// 使它们在元素之间没有间隙。
library Array {
    function remove(uint[] storage arr, uint index) public {
        // Move the last element into the place to delete
        require(arr.length > 0, "Can't remove from empty array");
        arr[index] = arr[arr.length - 1];
        arr.pop();
    }
}

contract TestArray {
    using Array for uint[];

    uint[] public arr;

    function testArrayRemove() public {
        for (uint i = 0; i < 3; i++) {
            arr.push(i);
        }

        arr.remove(1);

        assert(arr.length == 2);
        assert(arr[0] == 0);
        assert(arr[1] == 2);
    }
}

```



## 36. 解码:  ABI Decode

 `abi.encode`将数据编码为`bytes`.

`abi.decode`解码`bytes`回数据。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract AbiDecode {
    struct MyStruct {
        string name;
        uint[2] nums;
    }

    function encode(
        uint x,
        address addr,
        uint[] calldata arr,
        MyStruct calldata myStruct
    ) external pure returns (bytes memory) {
        return abi.encode(x, addr, arr, myStruct);
    }

    function decode(bytes calldata data)
        external
        pure
        returns (
            uint x,
            address addr,
            uint[] memory arr,
            MyStruct memory myStruct
        )
    {
        // (uint x, address addr, uint[] memory arr, MyStruct myStruct) = ...
        (x, addr, arr, myStruct) = abi.decode(data, (uint, address, uint[], MyStruct));
    }
}

```

## 37. 用 Keccak256 散列:  Hashing with Keccak256

`keccak256` 计算输入的 `Keccak-256` 哈希。

一些用例是：

- 从输入创建确定性唯一 ID
- 提交-显示方案
- 紧凑的加密签名（通过签名散列而不是更大的输入）

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract HashFunction {
    function hash(
        string memory _text,
        uint _num,
        address _addr
    ) public pure returns (bytes32) {
        return keccak256(abi.encodePacked(_text, _num, _addr));
    }

    // 哈希冲突示例
    // 传递多个动态数据类型时 abi.encodePacked会发生哈希冲突
    // 在这种情况下，你应该使用 abi.encode 代替。
    function collision(string memory _text, string memory _anotherText)
        public
        pure
        returns (bytes32)
    {
        // encodePacked(AAA, BBB) -> AAABBB
        // encodePacked(AA, ABBB) -> AAABBB
        return keccak256(abi.encodePacked(_text, _anotherText));
    }
}

contract GuessTheMagicWord {
    bytes32 public answer =
        0x60298f78cc0b47170ba79c10aa3851d7648bd96f2f8e46a19dbc777c36fb0c00;

    // 解码后数据内容是 "Solidity"
    function guess(string memory _word) public view returns (bool) {
        return keccak256(abi.encodePacked(_word)) == answer;
    }
}

```

## 38. 验证签名:  Verifying Signature

  
消息可以在链下签名，然后使用智能合约在链上进行验证。

[使用 ethers.js 的示例](https://github.com/t4sk/hello-erc20-permit/blob/main/test/verify-signature.js)

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

/* Signature Verification

How to Sign and Verify
# Signing
1. 创建消息( message )去签名( sign )
2. 对 message 进行 哈希处理 Hash
3. 签名 Sign 该哈希值 hash (离线操作, 确保你私钥是安全的)

# Verify
1. 通过原始消息, 重新创建 hash 值 
2. 通过签名方式再次签名该hash 值 
3. 对比两次获得到的签名是否一致 

contract VerifySignature {
    /* 1. Unlock MetaMask account
    ethereum.enable()
    */

    /* 2. Get message hash to sign
    getMessageHash(
        0x14723A09ACff6D2A60DcdF7aA4AFf308FDDC160C,
        123,
        "coffee and donuts",
        1
    )

    hash = "0xcf36ac4f97dc10d91fc2cbb20d718e94a8cbfe0f82eaedc6a4aa38946fb797cd"
    */
    function getMessageHash(
        address _to,
        uint _amount,
        string memory _message,
        uint _nonce
    ) public pure returns (bytes32) {
        return keccak256(abi.encodePacked(_to, _amount, _message, _nonce));
    }

    /* 3. Sign message hash
    # using browser
    account = "copy paste account of signer here"
    ethereum.request({ method: "personal_sign", params: [account, hash]}).then(console.log)

    # using web3
    web3.personal.sign(hash, web3.eth.defaultAccount, console.log)

    Signature will be different for different accounts
    0x993dab3dd91f5c6dc28e17439be475478f5635c92a56e17e82349d3fb2f166196f466c0b4e0c146f285204f0dcb13e5ae67bc33f4b888ec32dfe0a063e8f3f781b
    */
    function getEthSignedMessageHash(bytes32 _messageHash)
        public
        pure
        returns (bytes32)
    {
        /*
        Signature is produced by signing a keccak256 hash with the following format:
        "\x19Ethereum Signed Message\n" + len(msg) + msg
        */
        return
            keccak256(
                abi.encodePacked("\x19Ethereum Signed Message:\n32", _messageHash)
            );
    }

    /* 4. Verify signature
    signer = 0xB273216C05A8c0D4F0a4Dd0d7Bae1D2EfFE636dd
    to = 0x14723A09ACff6D2A60DcdF7aA4AFf308FDDC160C
    amount = 123
    message = "coffee and donuts"
    nonce = 1
    signature =
        0x993dab3dd91f5c6dc28e17439be475478f5635c92a56e17e82349d3fb2f166196f466c0b4e0c146f285204f0dcb13e5ae67bc33f4b888ec32dfe0a063e8f3f781b
    */
    function verify(
        address _signer,
        address _to,
        uint _amount,
        string memory _message,
        uint _nonce,
        bytes memory signature
    ) public pure returns (bool) {
        bytes32 messageHash = getMessageHash(_to, _amount, _message, _nonce);
        bytes32 ethSignedMessageHash = getEthSignedMessageHash(messageHash);

        return recoverSigner(ethSignedMessageHash, signature) == _signer;
    }

    function recoverSigner(bytes32 _ethSignedMessageHash, bytes memory _signature)
        public
        pure
        returns (address)
    {
        (bytes32 r, bytes32 s, uint8 v) = splitSignature(_signature);

        return ecrecover(_ethSignedMessageHash, v, r, s);
    }

    function splitSignature(bytes memory sig)
        public
        pure
        returns (
            bytes32 r,
            bytes32 s,
            uint8 v
        )
    {
        require(sig.length == 65, "invalid signature length");

        assembly {
            /*
            前 32 个字节存储签名的长度

            add(sig, 32) = sig 的指针 + 32
            有效地跳过签名的前 32 个字节

            mload(p) 将从内存地址 p 开始的接下来的 32 个字节加载到内存中
            
            */

            // first 32 bytes, after the length prefix
            r := mload(add(sig, 32))
            // second 32 bytes
            s := mload(add(sig, 64))
            // final byte (first byte of the next 32 bytes)
            v := byte(0, mload(add(sig, 96)))
        }

        // implicitly return (r, s, v)
    }
}
```


## 39. Gas 优化:  Gas Optimizations

一些节省Gas的技术。

- 替换memory为calldata
- 将状态变量加载到内存
- 将 for 循环替换i++为++i
- 缓存数组元素
- 短路

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// gas golf
contract GasGolf {
    // start - 50908 gas
    // use calldata - 49163 gas
    // load state variables to memory - 48952 gas
    // short circuit - 48634 gas
    // loop increments - 48244 gas
    // cache array length - 48209 gas
    // load array elements to memory - 48047 gas
    // uncheck i overflow/underflow - 47309 gas

    uint public total;

    // start - 未做Gas优化
    // function sumIfEvenAndLessThan99(uint[] memory nums) external {
    //     for (uint i = 0; i < nums.length; i += 1) {
    //         bool isEven = nums[i] % 2 == 0;
    //         bool isLessThan99 = nums[i] < 99;
    //         if (isEven && isLessThan99) {
    //             total += nums[i];
    //         }
    //     }
    // }

    // 进行过 gas 优化
    // [1, 2, 3, 4, 5, 100]
    function sumIfEvenAndLessThan99(uint[] calldata nums) external {
        uint _total = total;
        uint len = nums.length;

        for (uint i = 0; i < len; ) {
            uint num = nums[i];
            if (num % 2 == 0 && num < 99) {
                _total += num;
            }
            unchecked {
                ++i;
            }
        }

        total = _total;
    }
}

```

## 关注更多

Web3 技术与应用，研究实践分享！
 

*Twitter*:    [@AntCaveClub](https://twitter.com/AntCaveClub)

*蚁穴Web3社区*:  [https://antcave.club/](https://antcave.club/)