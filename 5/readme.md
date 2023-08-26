# quiz5

合约创建过程

## Code
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Gatekeeper {
    address public entrant;

    modifier gateOne() {
        require(msg.sender != tx.origin);
        _;
    }

    modifier gateTwo() {
        uint x;
        assembly { x := extcodesize(caller()) }
        require(x == 0);
        _;
    }

    function enter(bytes8 _gateKey) public gateOne gateTwo returns (bool) {
        entrant = tx.origin;
        return true;
    }
}
```

## enter不能被执行，因为gateOne要求caller是合约，而gateTwo却要求caller不能是合约

## 以下说法正确的有哪些
* 部署合约交易的to地址为0x00的交易就是部署合约交易，执行部署交易时，计算出新部署合约地址，在部署交易执行之前，合约地址无法预先知道
* 部署合约交易的data就是部署合约的code，执行部署交易时，会存储到新计算的合约地址上
* 部署交易执行时，先计算合约地址，再存储合约code，最后执行构造函数进行初始化
* 以上说法都不正确

## 关于合约创建，一下说法正确的有哪些
* 创建合约交易的data是合约的creation code，而最终链上存储的是合约的runtime code
* 创建合约的交易将执行新合约的creation code，return的就是合约的runtime code
* 合约的构造函数在creation code，而不会存在于runtime code
* 以上说法都正确

## 关于创建合约的create op code
* 有三个参数value，offset，和size，将memory的offset位置开始，size大小的数据作为新合约的creation code，并且发送value的eth到新合约地址
* 计算新合约地址
* creation code包含合约构造code，将会在新合约地址上被执行，code return的就是runtime code，会被存储到新合约地址的code上
* create op最后将新合约地址push到stack