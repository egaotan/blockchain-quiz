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

