## 智能合约实例-9: 可升级代理

## 合约原理

可升级代理合约的示例。永远不要在生产中使用它。

这个例子展示了调用`fallback`时, `delegatecall`如何在被调用时使用和返回数据。


## 代码实现


```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Proxy {

    // 实现合约的地址
    address public implementation;
    
    // 设置 地址 
    function setImplementation(address _imp) external {
        implementation = _imp;
    }

    // 委托 地址
    function _delegate(address _imp) internal virtual {
        assembly {
            // 调用datacopy(t, f, s)
            // 从位置 f 的 calldata 复制 s 个字节到位置 t 的 mem
            calldatacopy(0, 0, calldatasize())

 
            // delegatecall(g, a, in, insize, out, outsize)
            // - 在地址 a 调用合约
            // - 输入 mem[in…(in+insize))
            // - 提供 g 气体
            // - 和输出区域 mem[out...(out+outsize))
            // - 错误返回 0，成功返回 1
            let result := delegatecall(gas(), _imp, 0, calldatasize(), 0, 0)


            // returndatacopy(t, f, s)
            // 从位置 f 的 returndata 复制 s 个字节到位置 t 的 mem
            returndatacopy(0, 0, returndatasize())

            switch result
            case 0 {
                // revert(p, s)
                // end execution, revert state changes, return data mem[p…(p+s))
                revert(0, returndatasize())
            }
            default {
                // return(p, s)
                // end execution, return data mem[p…(p+s))
                return(0, returndatasize())
            }
        }
    }

    fallback() external payable {
        _delegate(implementation);
    }
}

contract V1 {
    address public implementation;
    uint public x;

    function inc() external {
        x += 1;
    }
}

contract V2 {
    address public implementation;
    uint public x;

    function inc() external {
        x += 1;
    }

    function dec() external {
        x -= 1;
    }
}

```

