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