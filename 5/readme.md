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