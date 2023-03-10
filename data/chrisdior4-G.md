## Gas Report

| N-N    |Issue|
|:------:|:----|
| [G&#x2011;01] | Use x != 0 instead of x > 0 for uint types | 2 |
| [G&#x2011;02] | x = x - y costs less gas than x -= y, same for addition | 11 |
| [G&#x2011;03] | Remove public visibility from constant variables | 19 |
| [G&#x2011;04] | Don't use _msgSender() if not supporting EIP-2771 | 31 |
| [G&#x2011;05] | ++I/I++ should be UNCHECKED{++I}/UNCHECKED{I++}  when it is not possible for them to overflow | 2 |
| [G&#x2011;06] | ++i costs 5 gas less than i++ | 1 |
| [G&#x2011;07] | Cache Array Length Outside of Loop | 1 |
| [G&#x2011;08] | Some public functions can be changed to external | 1 |

Total: 8 issues

### [G-01] Use x != 0 instead of x > 0 for uint types
The != operator costs less gas than > and for uint types you can use it to check for non-zero values to save gas

File: `PluginRepo.sol` 

```solidity
if (_releaseMetadata.length > 0) 
```

Lines of code:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L181 

File: `TokenVoting.sol`

```solidity
return votingToken.getVotes(_account) > 0;
```

Lines of code:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L139

### [G-02] x = x - y costs less gas than x -= y, same for addition
You can replace all -= and += occurrences to save gas.

Example:

File: `Multisig.sol`

```solidity
proposal_.approvals += 1;
```

Lines of code:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276

### [G-03] Remove public visibility from constant variables
constant variables are custom to the contract and won't need to be read on-chain - anyone can just see their values from the source code and, if needed, hardcode them into other contracts. Removing the public visibility will optimise deployment cost since no automatically generated getters will exist in the bytecode of the contract.

File: `PermissionLib.sol`

```solidity
address public constant NO_CONDITION = address(0);
```

Lines of code:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionLib.sol#L10

### [G-04] Don't use _msgSender() if not supporting EIP-2771

This one is present in a lot of the contracts. Use msg.sender if the code does not implement EIP-2771 trusted forwarder support.

Example:

File: `MajorityVotingBase.sol` 

```solidity
address account = _msgSender();
```

Lines of code:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L270

### [G-05] ++I/I++ should be UNCHECKED{++I}/UNCHECKED{I++}  when it is not possible for them to overflow
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

File: `RegistryUtils.sol`

```solidity
for (uint256 i; i < nameLength; i++) {
```

Line of code: - 16

### [G-06] ++i costs 5 gas less than i++

File: `RegistryUtils.sol`

```solidity
for (uint256 i; i < nameLength; i++) {
```

Line of code: - 16

### [G-07] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop. The project has many instances of this issue. Cache the array outside the loop. Couple of examples:

File: `DAO.sol`

```solidity
for (uint256 i = 0; i < _actions.length; )
```

Lines of code:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L184

File: `Addresslist.sol`

```solidity
for (uint256 i; i < _exitingAddresses.length; ) {
```

Lines of code: 

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/utils/Addresslist.sol#L78

### [G-08] Some public functions can be changed to external

Since a function is not called from within a contract, using external instead of public will save gas as all the function arguments will be in calldata instead of copied to memory.

File: `MajorityVotingBase.sol`

`canExecute`, `canVote`, `getVoteOption`, `execute`, `vote` methods can be made external 