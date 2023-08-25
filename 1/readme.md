# quiz1

合约存储

## Code
```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity ^0.8.0 ;

contract myContract
{
  // Initialising array numbers
  int[] public numbers;
 
  // Function to insert values
  // in the array numbers
  function Numbers() public
  {
    numbers.push(1);
    numbers.push(2);
 
    //Creating a new instance
    int[] storage myArray = numbers;
     
    // Adding value to the
    // first index of the new Instance
    myArray[0] = 0;
  }
}
```
## 
* 调用Numbers函数后，变量numbers为()
* 调用Numbers函数后，合约slot[0]为()

## 以下说法正确的有哪些
* 调用Numbers函数后，变量numbers为[1,2]
* 如果去掉语句"int[] storage myArray = numbers;"中的storage，合约正确，调用Numbers函数后，变量numbers为[1,2]
* 如果语句"int[] storage myArray = numbers;"更新为"int[] memory myArray = numbers;",合约正确，调用Numbers函数后，变量numbers为[1,2]
* 以上都不正确

## 以下说法正确的有哪些
* numbers是合约的第0个成员，所以合约的slot[0]存储numbers的length
* numbers是合约的第0个成员，所以合约的slot[0...n]依次存储numbers[0...n]

## Code
```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity ^0.5.0;

contract myContract
{
    bool public locked = true;
    uint256 public ID = block.timestamp;
    uint8 private flattening = 10;
    uint8 private denomination = 255;
    uint16 private awkwardness = uint16(block.timestamp);
    bytes32[3] private data;
    uint[] public numbers;

    constructor(bytes32[3] memory _data) public {
        data = _data;
    }

    function unlock(bytes16 _key) public {
        require(_key == bytes16(data[2]));
        locked = false;
    }

    function init() public {
        numbers.push(1);
        numbers.push(2);
        uint[] storage myArray = numbers;
        myArray[0] = 0;
    }

    function retract() public {
        numbers.length--;
    }

    function revise(uint i, uint _content) public {
        numbers[i] = _content;
    }
}
```

## 是否可以获得正确的_key来执行unlock？如何获取正确的_key？

## init执行后，numbers的数据是什么？

## 除了通过unlock可以设置locked=false外，时候还有其他方法，怎么执行？
