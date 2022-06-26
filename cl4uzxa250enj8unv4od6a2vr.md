## 智能合约实例-4:  可迭代的映射 mapping 实现

## 可迭代 mapping

一般对于数据结构mapping 来说, 我们无法去遍历.当有时又需要这样的使用场景. 

所以这里演示一个如何创建可迭代的 mapping 示例。


## 实现功能 

- 可迭代的`mapping` 

- 通过 `library` 库的方式调用



## 源代码 

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

// 自定义库
library IterableMapping {
    // Iterable mapping from address to uint;
    struct Map {
        address[] keys;
        mapping(address => uint) values;
        mapping(address => uint) indexOf;
        mapping(address => bool) inserted;
    }

    function get(Map storage map, address key) public view returns (uint) {
        return map.values[key];
    }

    function getKeyAtIndex(Map storage map, uint index) public view returns (address) {
        return map.keys[index];
    }

    function size(Map storage map) public view returns (uint) {
        return map.keys.length;
    }

    function set(
        Map storage map,
        address key,
        uint val
    ) public {
        if (map.inserted[key]) {
            map.values[key] = val;
        } else {
            map.inserted[key] = true;
            map.values[key] = val;
            map.indexOf[key] = map.keys.length;
            map.keys.push(key);
        }
    }

    function remove(Map storage map, address key) public {
        if (!map.inserted[key]) {
            return;
        }

        delete map.inserted[key];
        delete map.values[key];

        uint index = map.indexOf[key];
        uint lastIndex = map.keys.length - 1;
        address lastKey = map.keys[lastIndex];

        map.indexOf[lastKey] = index;
        delete map.indexOf[key];

        map.keys[index] = lastKey;
        map.keys.pop();
    }
}

contract TestIterableMap {
    using IterableMapping for IterableMapping.Map;

    IterableMapping.Map private map;

    function testIterableMap() public returns (address, address, address){

        // 对 map 赋值
        map.set(address(0), 0);
        map.set(address(1), 100);
        map.set(address(2), 200); // insert
        map.set(address(2), 200); // update
        map.set(address(3), 300);

        // 遍历 map 的值 
        for (uint i = 0; i < map.size(); i++) {
            address key = map.getKeyAtIndex(i);

            assert(map.get(key) == i * 100);
        }

        return (map.getKeyAtIndex(1), map.getKeyAtIndex(2), map.getKeyAtIndex(3));
    }
}
```

## 关注更多

Web3 技术与应用，研究实践分享！
 

*Twitter*:    [@AntCaveClub](https://twitter.com/AntCaveClub)

*蚁穴Web3社区*:  [https://antcave.club/](https://antcave.club/)
