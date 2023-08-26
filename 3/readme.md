# quiz3

EVM assembly和合约内存

## Code
```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.0;

library LibString {
    function toString(uint256 n) internal pure returns (string memory str) {
        if (n == 0) return "0"; // Otherwise it'd output an empty string for 0.

        assembly {
            let k := 78 // Start with the max length a uint256 string could be.

            // We'll store our string at the first chunk of free memory.
            str := mload(0x40)

            // The length of our string will start off at the max of 78.
            mstore(str, k)

            // Update the free memory pointer to prevent overriding our string.
            // Add 128 to the str pointer instead of 78 because we want to maintain
            // the Solidity convention of keeping the free memory pointer word aligned.
            mstore(0x40, add(str, 128))

            // We'll populate the string from right to left.
            // prettier-ignore
            for {} n {} {
                // The ASCII digit offset for '0' is 48.
                let char := add(48, mod(n, 10))

                // Write the current character into str.
                mstore(add(str, k), char)

                k := sub(k, 1)
                n := div(n, 10)
            }

            // Shift the pointer to the start of the string.
            str := add(str, k)

            // Set the length of the string to the correct value.
            mstore(str, sub(78, k))
        }
    }
}
```

## 关于evm的memory layout，说法正确的有哪些
* memory的free memory pointer初始化是0x00
* free memory pointer存储在保留slot 0x40
* solidity总是存储新对象在free memory pointer位置，而且从不释放memory
* dynamic array的length存储在array对象的第一个slot，后面就是elements
* memory为各个元素保留uint256的存储空间，而不像storage，会根据元素大小组织layout
* memory中，不能使用mapping，不能使用array的push和pop
