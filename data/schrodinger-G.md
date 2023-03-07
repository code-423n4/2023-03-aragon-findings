
## Summary

### Gas Optimizations
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [G-01] | Don't Initialize Variables with Default Value | 1 |  
| [G-02] | Use != 0 instead of > 0 for Unsigned Integer Comparison | 8 | 
| [G&#x2011;03] | <x> += <y> costs more gas than <x> = <x> + <y> for state variables (-= too) | 1 | 
| [G&#x2011;04] | Using bools for storage incurs overhead | 3 | 
| [G&#x2011;05] | <array>.length should not be looked up in every loop of a for-loop| 1 | 

Total: 14 instances over 5 issues .

## Gas Optimizations

### [G&#x2011;01]  Don't Initialize Variables with Default Value
If a variable is not set/initialized, the default value is assumed (0, false, 0x0 … depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

*There are 1 instances of this issue:*

```solidity
File: src\core\dao\DAO.sol

184   for (uint256 i = 0; i < _actions.length; )
```

### [G&#x2011;02]  Use != 0 instead of > 0 for Unsigned Integer Comparison
This change saves 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

*There are 8 instances of this issue:*

```solidity
File: src\core\utils\BitMap.sol

10 return bitValue > 0;

```

```solidity
File: src\framework\dao\DAORegistry.sol

59 if ((bytes(subdomain).length > 0))

```

```solidity
File: src\framework\plugin\repo\PluginRepo.sol

181 if (_releaseMetadata.length > 0)

```

```solidity
File: src\framework\plugin\repo\PluginRepoRegistry.sol

55  if (!(bytes(subdomain).length > 0))
```

```solidity
File: src\framework\plugin\setup\PluginSetupProcessor.sol

378 => if (_params.permissions.length > 0)

535 => if (_params.permissions.length > 0)

669 => if (_initData.length > 0)

```

```solidity
File: src\core\utils\BitMap.sol

139  return votingToken.getVotes(_account) > 0;

```

### [G&#x2011;03] <x> += <y> costs more gas than <x> = <x> + <y> for state variables (-= too)

Using the addition operator instead of plus-equals saves 113 gas. Subtractions act the same way.

*affected code:*

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276

## [G&#x2011;04] Using bools for storage incurs overhead
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from false to true, after having been true in the past

*affected code 3 instances:*

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L38

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L108

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L212


## [G-5] <array>.length should not be looked up in every loop of a for-loop
The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

There are 1 instances of this issue:

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L184

