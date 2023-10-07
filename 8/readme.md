# quiz8

ETH transfer

## Code
```solidity
contract CannotReceiveETH {
    receive() external payable {
        revert();
    }

    function hasETH() external view returns (bool) {
        return address(this).balance > 0;
    }
}
```

## 以下说法正确的有哪些
* hasETH不可能返回true
* 如果在部署合约时同时发送ETH，则该合约可以收到ETH
* 如果这个合约地址为SELFDESTRUCT opcode的目标地址，则该合约可以收到ETH
* 如果这个合约地址为beacon chain的withdrawal地址，则该合约可以收到ETH

## code
```solidity
contract Example {
    event FallbackExecuted(bytes data, uint256 value);
    event ReceiveExecuted(uint256 value);

    fallback() external payable {
        emit FallbackExecuted(msg.data, msg.value);
    }

    receive() external payable {
        emit ReceiveExecuted(msg.value);
    }    
}
```

## 以下说法正确的有哪些
* 当使用call的方式来发送ETH，无论calldata有没有，ReceiveExecuted event会emitted
* 当使用call的方式来发送ETH，并且calldata有值时，FallbackExecuted event会emitted
* 当使用call的方式，没有发送ETH，并且calldata为空时，FallbackExecuted event会emitted
* 当使用call的方式来发送ETH，并且calldata的function selector为0xa3e76c0f(the function signature of receive())时，ReceiveExecuted event会emitted
* receive function只有2300 gas可用，无论caller指定多少gas

## code
```solidity
contract Example {
    function foo(uint8 data, uint64 length) external {
        // ...
    }
}
```

## 以下说明的，有哪些安全检查是编译器自动加入的
* 当call的时候，指定了发送的ETH时，会panic
* 当calldata的size小于4 bytes时，会panic
* 当calldata的size大于68 bytes时，会panic
* 当第一个参数不能ABI-decoded到uint8类型时，会panic
* 当第二个参数不能ABI-decoded到uint64类型时，会panic

## code
```solidity
contract Example {
    uint8[] public someArray = new uint8[](300);

    function foo(uint32 a, uint32 b, uint32 index) external {
        unchecked {
            someArray[index] = uint8(a / b);
        }
    }
}
```

## 以下说法正确的有哪些
* 由于使用了unchecked，数组的越界访问保护不启用，当index大于300时，不会panic
* 由于使用了unchecked，当b为0时，a /b 不会panic，结果为0
* 由于使用了unchecked，编译器自动overflow检查不会启用，a / b 类型转为为uint8不会panic
* 以上说法都不正确
