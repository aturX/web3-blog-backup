## 智能合约实例-3: 用默克尔树进行加密验证

## 默克尔树

Merkle 树允许你以加密方式证明某个集合中包含某一个元素, 而不透露整个集合信息。


## 实现功能

常用场景: 

- 离线验证权限
- NFT 白名单校验 
- Token 空投名单校验

通过简单的 Merkle 树 方式, 可以轻松的验证, 你的地址是否符合上万个白名单中的某一个. (即有许可资格)



## 源代码

一起来看看, 具体如何实现的: 

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract MerkleProof {

    // 验证函数: 用于通过 proof 验证数据
    function verify(
        bytes32[] memory proof,    
        bytes32 root,
        bytes32 leaf,
        uint index
    ) public pure returns (bool) {
        bytes32 hash = leaf;
        
        // 该段循环功能:  从要验证的子节点开始, 逐层加密, 获得最后的 root hash 值
        //  然后和构造函数中 root 对比是否相等
        for (uint i = 0; i < proof.length; i++) {
            bytes32 proofElement = proof[i];

            if (index % 2 == 0) {
                hash = keccak256(abi.encodePacked(hash, proofElement));
            } else {
                hash = keccak256(abi.encodePacked(proofElement, hash));
            }

            index = index / 2;
        }

        return hash == root;
    }
}

contract TestMerkleProof is MerkleProof {
    bytes32[] public hashes;

    constructor() {

        //  基础信息 
        string[4] memory transactions = [
            "alice -> bob",
            "bob -> dave",
            "carol -> alice",
            "dave -> bob"
        ];

        // 讲信息的 hash 值存储. 
        // 不管数据多么大, 内容有多长, 当通过  keccak256 函数后, 都会获得一个独一无二的hash值, 所以数据任何内容被修改, 都会得到一个完全不同的 hash 值.  
        // 同样的数据, 不管什么时候, 都会得到相同的 hash 值 
        for (uint i = 0; i < transactions.length; i++) {
            hashes.push(keccak256(abi.encodePacked(transactions[i])));
        }
        

        uint n = transactions.length;
        uint offset = 0;
        

        // 这段循环的功能是: 将所有hash, 按照顺序, 两两连接hash 组成新的hash
        // 如此循环, 直到层层编码, 最后将全部的数据, 整理成了 一个 root hash , 就类似树状结构的树根 hash 
        // 那么就会有一个特性:  任何一个 子节点数据被 修改, root hash 的值都会完全变化. 这样就可证明, 数据是否符合条件 
        while (n > 0) {
            for (uint i = 0; i < n - 1; i += 2) {
                hashes.push(
                    keccak256(
                        abi.encodePacked(hashes[offset + i], hashes[offset + i + 1])
                    )
                );
            }
            offset += n;
            n = n / 2;
        }
    }
    
    // 获得root hash:  通过上面的 hash计算可知, 数组中最后一个值, 就是 root 的hash 
    function getRoot() public view returns (bytes32) {
        return hashes[hashes.length - 1];
    }

    /* verify
    3rd leaf
    0xdca3326ad7e8121bf9cf9c12333e6b2271abe823ec9edfe42f813b1e768fa57b

    root
    0xcc086fcc038189b4641db2cc4f1de3bb132aefbd65d510d817591550937818c7

    index
    2

    proof
    0x8da9e1c820f9dbd1589fd6585872bc1063588625729e7ab0797cfc63a00bd950
    0x995788ffc103b987ad50f5e5707fd094419eb12d9552cc423bd0cd86a3861433
    */
}

```