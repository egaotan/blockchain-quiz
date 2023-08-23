# quiz7

delegatecall & evm存储

## Code
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Preservation {

  // public library contracts 
  address public timeZone1Library;
  address public timeZone2Library;
  address public owner; 
  uint storedTime;
  // Sets the function signature for delegatecall
  bytes4 constant setTimeSignature = bytes4(keccak256("setTime(uint256)"));

  constructor(address _timeZone1LibraryAddress, address _timeZone2LibraryAddress) {
    timeZone1Library = _timeZone1LibraryAddress; 
    timeZone2Library = _timeZone2LibraryAddress; 
    owner = msg.sender;
  }
 
  // set the time for timezone 1
  function setFirstTime(uint _timeStamp) public {
    timeZone1Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }

  // set the time for timezone 2
  function setSecondTime(uint _timeStamp) public {
    timeZone2Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }
}

// Simple library contract to set the time
contract LibraryContract {

  // stores a timestamp 
  uint storedTime;  

  function setTime(uint _time) public {
    storedTime = _time;
  }
}
```

## 以下说法正确的有哪些
* setFirstTime和setSecondTime是delegatecall,执行的是timeZone1Library和timeZone2Library合约的代码，修改的是Preservation合约的状态
* 如果是正常的部署合约，timeZone1Library是LibraryContract合约的一个实例，执行setFirstTime后，timeZone1Library的setTime将执行，会更新Preservation合约的storedTime

## 以上合约可以被Attack，获取到合约的owner？如何实现？