## DeFi 原理:  Dex 流通性添加/删除 实现

通过代码演示, 如何添加流动性, 如何删除流动性. 



```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract TestUniswapLiquidity {
    address private constant FACTORY = 0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f;
    address private constant ROUTER = 0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D;
    address private constant WETH = 0xc778417E063141139Fce010982780140Aa0cD5Ab;

    // 添加流动性
    function addLiquidity(
        address _tokenA,
        address _tokenB,
        uint _amountA,
        uint _amountB
    ) external {
        // 将 A,B Token 转移到合约地址下
        IERC20(_tokenA).transferFrom(msg.sender, address(this), _amountA);
        IERC20(_tokenB).transferFrom(msg.sender, address(this), _amountB);

        // 为 Router 地址授权
        IERC20(_tokenA).approve(ROUTER, _amountA);
        IERC20(_tokenB).approve(ROUTER, _amountB);

        // 添加流动性:  获得结果值
        (uint amountA, uint amountB, uint liquidity) = IUniswapV2Router(ROUTER).addLiquidity(
                _tokenA,
                _tokenB,
                _amountA,
                _amountB,
                1,
                1,
                address(this),
                block.timestamp
            );
    }

    // 删除流动性
    function removeLiquidity(address _tokenA, address _tokenB) external {

        // 获得交易对 池子地址
        address pair = IUniswapV2Factory(FACTORY).getPair(_tokenA, _tokenB);

        // 获得其 LP 数量
        uint liquidity = IERC20(pair).balanceOf(address(this));

        // 授权给Router
        IERC20(pair).approve(ROUTER, liquidity);

        // 删除流动性, 返回结果数量
        (uint amountA, uint amountB) = IUniswapV2Router(ROUTER).removeLiquidity(
            _tokenA,
            _tokenB,
            liquidity,
            1,
            1,
            address(this),
            block.timestamp
        );
    }
}

// v2 router 接口
interface IUniswapV2Router {
    function addLiquidity(
        address tokenA,
        address tokenB,
        uint amountADesired,
        uint amountBDesired,
        uint amountAMin,
        uint amountBMin,
        address to,
        uint deadline
    )
        external
        returns (
            uint amountA,
            uint amountB,
            uint liquidity
        );

    function removeLiquidity(
        address tokenA,
        address tokenB,
        uint liquidity,
        uint amountAMin,
        uint amountBMin,
        address to,
        uint deadline
    ) external returns (uint amountA, uint amountB);
}


// v2 工厂 接口 
interface IUniswapV2Factory {
    function getPair(address token0, address token1) external view returns (address);
}


// ERC20 接口
interface IERC20 {
    function totalSupply() external view returns (uint);

    function balanceOf(address account) external view returns (uint);

    function transfer(address recipient, uint amount) external returns (bool);

    function allowance(address owner, address spender) external view returns (uint);

    function approve(address spender, uint amount) external returns (bool);

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);
}
```