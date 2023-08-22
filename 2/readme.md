# quiz2

合约安全与重入攻击

## Code
```solidity
// SPDX-License-Identifier: GPL-3.0
contract myContract {
    mapping (address => uint256) public balances;

    function deposit() external payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() external {
        uint256 amount = balances[msg.sender];
        (bool success, ) = msg.sender.call.value(amount)("");
        require(success);
        balances[msg.sender] = 0;
    }
}
```

## 以下说法正确的有哪些
* 