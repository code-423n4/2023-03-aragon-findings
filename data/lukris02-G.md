# Gas Optimizations Report for Aragon Protocol contest
## Overview
During the audit, 2 gas issues were found.  

№ | Title | Instance Count 
--- | --- | --- 
G-1 | Use unchecked blocks for incrementing | 2 
G-2 | Use unchecked blocks for subtractions where underflow is impossible | 1 

## Gas Optimizations Findings(2)
### G-1. Use unchecked blocks for incrementing
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops or similar cases, "i" will not overflow because the loop will run out of gas before that or max value never be reached.
##### Instances
- https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95
- https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```
#
### G-2. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L530