# QA Report for Aragon contest 

## Overview
ID|Title|Instance Count
|-|:-|:-:|
L-1 | Lack of check for non-existing contract | 1
NC-1 | Unused imported contract | 3



# L-1 Lack of check for non existing contract

In the DAO.execute() function consider checking that the contract exist (by checking the contract size).

The check is important because a call to a non contract address will always returning true. 

```solidity
if (!success) {
	// If the call failed and wasn't allowed in allowFailureMap, revert.
	if (!hasBit(_allowFailureMap, uint8(i))) {
		revert ActionFailed(i);
	}

	// If the call failed, but was allowed in allowFailureMap, store that
	// this specific action has actually failed.
	failureMap = flipBit(failureMap, uint8(i));
}
```

## Recommandation 

In addition to check the returned call value, consider checking if the address contract exists or not.

# NC-1  Unused imported contract

Consider removing unecessary imported contracts, for exemple :

[PluginRepo](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L8) in DAOFactory.sol contract 
[IERC1155Upgradeable](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#LL11C59-L11C78) in .sol contract 
[IDAO](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/membership/IMembership.sol#L5) in .sol contract 

