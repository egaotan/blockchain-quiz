# quiz2

合约安全与重入攻击

## Code
Single-Function Reentrancy
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


## Code
Cross-Function Reentrancy
```solidity
contract myContract {
    mapping (address => uint) private balances;

    function transfer(address to, uint amount) public {
        if (balances[msg.sender] >= amount) {
        balances[to] += amount;
        balances[msg.sender] -= amount;
        }
    }

    function withdraw() public {
        uint amount = balances[msg.sender];
        (bool success, ) = msg.sender.call.value(amount)("");
        require(success);
        balances[msg.sender] = 0;
    }
}
```

## 以下朔方正确的有哪些

## Code
Cross-contract Read-only Reentrancy
```solidity
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract Bank is ReentrancyGuard {
    mapping (address => uint) public balances;

    function deposit() external payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() public nonReentrant {
        uint amount = balances[msg.sender];
        (bool success, ) = payable(msg.sender).call{value: amount}("");
        require(success);
        balances[msg.sender] = 0;
    }
}

contract BankConsumer {
    Bank private bank;

    constructor(address _bank) {
        bank = Bank(_bank);
    }
    function getBalance(address account) public view returns (uint256) {
        return bank.balances(account);
    }
}
```

```solidity
contract Attacker {
    event Checkpoint(uint256 balance);

    Bank private bank;
    BankConsumer private consumer;

    constructor(address _bank, address _consumer) payable {
        bank = Bank(_bank);
        consumer = BankConsumer(_consumer);
    }

    function attack() public {
        emit Checkpoint(consumer.getBalance(address(this)));
        bank.deposit{value: 1 ether}();
        bank.withdraw();
        emit Checkpoint(consumer.getBalance(address(this)));
    }

    receive() external payable  {
        emit Checkpoint(consumer.getBalance(address(this)));
        // more malicious code here
    }
}
```