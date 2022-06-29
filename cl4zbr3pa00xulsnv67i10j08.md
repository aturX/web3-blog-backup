## 智能合约实例-8:  最小代理合约


如果你有一个将被多次部署的合约，请使用最少的代理合约来廉价地部署它们。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// original code
// https://github.com/optionality/clone-factory/blob/master/contracts/CloneFactory.sol

contract MinimalProxy {

    function clone(address target) external returns (address result) {

        // 将地址转换成 20 bytes
        bytes20 targetBytes = bytes20(target);

        // actual code //
        // 3d602d80600a3d3981f3363d3d373d3d3d363d73bebebebebebebebebebebebebebebebebebebebe5af43d82803e903d91602b57fd5bf3

        // creation code //
        // copy runtime code into memory and return it
        // 3d602d80600a3d3981f3

        // runtime code //
        // 代码委托调用地址
        // 363d3d373d3d3d363d73 address 5af43d82803e903d91602b57fd5bf3

        assembly {
            /*
            从存储在 0x40 的指针开始读取 32 字节的内存

            在 Solidity 中，内存中的 0x40 插槽很特殊：它包含“空闲内存指针”
            它指向当前分配的内存的末尾。
            */
            let clone := mload(0x40)
            // 从“clone”开始将 32 个字节存储到内存中
            mstore(
                clone,
                0x3d602d80600a3d3981f3363d3d373d3d3d363d73000000000000000000000000
            )

            /*
              |              20 bytes                |
            0x3d602d80600a3d3981f3363d3d373d3d3d363d73000000000000000000000000
                                                      ^
                                                      pointer
            */
            // store 32 bytes to memory starting at "clone" + 20 bytes
            // 0x14 = 20
            mstore(add(clone, 0x14), targetBytes)

            /*
              |               20 bytes               |                 20 bytes              |
            0x3d602d80600a3d3981f3363d3d373d3d3d363d73bebebebebebebebebebebebebebebebebebebebe
                                                                                              ^
                                                                                              pointer
            */
            // store 32 bytes to memory starting at "clone" + 40 bytes
            // 0x28 = 40
            mstore(
                add(clone, 0x28),
                0x5af43d82803e903d91602b57fd5bf30000000000000000000000000000000000
            )

            /*
              |               20 bytes               |                 20 bytes              |           15 bytes          |
            0x3d602d80600a3d3981f3363d3d373d3d3d363d73bebebebebebebebebebebebebebebebebebebebe5af43d82803e903d91602b57fd5bf3
            */
            // create new contract
            // send 0 Ether
            // code starts at pointer stored in "clone"
            // code size 0x37 (55 bytes)
            result := create(0, clone, 0x37)
        }
    }
}

```