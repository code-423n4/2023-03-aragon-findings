## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: UncheckedMath.sol#L8-L13](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/UncheckedMath.sol#L8-L13)

```diff
- function _uncheckedIncrement(uint256 i) pure returns (uint256) {
+ function _uncheckedIncrement(uint256 i) pure returns (uint256 incrementedNum) {
     unchecked {
         ++i;
     }
-     return i;
+     incrementedNum i;
 }
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: RegistryUtils.sol#L20](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L20)

```diff
-        if (char > 96 && char < 123) {
+        if (!(char <= 96 || char >= 123)) {
```
## Use smaller uint128 or smaller type and pack structs variables to use lesser amount of storage slots
As the solidity EVM works with 32 bytes, most variables work fine with less than 32 bytes and may be packed inside a struct when sitting next to each other so that they can be stored in the same slot, this saves gas when writing to storage ~2000 gas.

For instance, struct `Tally` of MajorityVotingBase.sol may be refactored as follows:

[File: MajorityVotingBase.sol#L166-L170](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L166-L170)

```diff
    struct Tally {
-        uint256 abstain;
+        uint128 abstain;
-        uint256 yes;
+        uint64 yes;
-        uint256 no;
+        uint64 no;
    }
```
This alone will save ~4000 gas with 3 slots of storage reduced to 1 slot. Additionally, the respective max value of `uint64` and `uint128` are more than sufficient catering to the limits of the struct variables as can be evidenced from the data table below:

```
uint		Digits	        Max value
-----------------------------
uint64		20		18,446,744,073,709,551,615
uint128		39		340,282,366,920,938,463,463,374,607,431,768,211,455
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, the following `>=` inequality instance may be refactored as follows:

[File: MajorityVotingBase.sol#L347-L348](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L347-L348)

```diff
-            proposal_.tally.yes + proposal_.tally.no + proposal_.tally.abstain >=
-            proposal_.parameters.minVotingPower
// Rationale for subtracting 1 on the right side of the inequality:
// x >= 10; [10, 11, 12, ...]
// x > 10 - 1 is the same as x > 9; [10, 11, 12 ...]
+            proposal_.tally.yes + proposal_.tally.no + proposal_.tally.abstain >
+            proposal_.parameters.minVotingPower - 1
```
Similarly, the `<=` instance below may be replaced with `<` as follows:

[File: MajorityVotingBase.sol#L519](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L519)

```diff
-            proposal_.parameters.startDate <= currentTime &&
// Rationale for adding 1 on the right side of the inequality:
// x <= 10; [10, 9, 8, ...]
// x < 10 + 1 is the same as x < 11; [10, 9, 8 ...]
+            proposal_.parameters.startDate < currentTime + 1 &&
```
## Ternary over `if ... else`
Using ternary operator instead of the if else statement saves gas.

For instance, the specific instance below may be refactored as follows:

[File: MajorityVotingBase.sol#L582-L585](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L582-L585)

```diff
-        if (_end == 0) {
-            endDate = earliestEndDate;
-        } else {
-            endDate = _end;

+        _end == 0
+            ? endDate = earliestEndDate;
+            : endDate = _end;

// The following if block originally nested in the else block is now stand alone and remains functional as intended     
            if (endDate < earliestEndDate) {
                revert DateOutOfBounds({limit: earliestEndDate, actual: endDate});
            }
-        }
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

For instance, the specific example below may be refactored as follows:

[File: MultisigSetup.sol#L29](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L29)

```diff
-        (address[] memory members, Multisig.MultisigSettings memory multisigSettings) = abi.decode(
+        (address[] storage members, Multisig.MultisigSettings storage multisigSettings) = abi.decode(
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For instance, the `-=` instance below may be refactored as follows:

[File: Multisig.sol#L276](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276)

```diff
-            proposal_.approvals += 1;
+            proposal_.approvals = proposal_.approvals + 1;
```
## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: DAOFactory.sol#L95-L118](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95-L118)

```diff
-        for (uint256 i; i < _pluginSettings.length; ++i) {
+        for (uint256 i; i < _pluginSettings.length;) {
              // Prepare plugin.

              [ ... ]

+            unchecked {
+                ++i;
+            }
          }
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.17",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## `uint256` Can be Cheaper Than `uint8` and Other Unsigned Integer Type of Smaller Bit Size
When dealing with function arguments or memory values, there is no inherent benefit because the compiler does not pack these values. Your contract’s gas usage may be higher because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. The EVM needs to properly enforce the limits of this smaller type.

It is only more efficient when you can pack variables of uint8 into the same storage slot with other neighboring variables smaller than 32 bytes. Here are some of the instances entailed:

[File: PluginRepo.sol#L129](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L129)

```solidity
        uint8 _release,
```
[File: BitMap.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L8)

```solidity
function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {
```
[File: BitMap.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L16)

```solidity
function flipBit(uint256 bitmap, uint8 index) pure returns (uint256) {
```