# QA Report for Aragon contest

## Overview

| ID  | Title | Instance Count |
| --- | --- | --- |
| L-1 | Lack of check for non-existing contract | 1   |
| L-2 | Unsafe ABI encoding | 2   |
| NC-1 | Unused imported contract | 3   |

# L-1 Lack of check for non existing contract

In the DAO.execute() only the returned value success is checked. This is problematic because if the 
target address is not a contract (0 address or EOA) the call will not revert and will return true, 
and the failure will be bypassed.

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

In addition to check the returned call value, consider checking if the  contract exists or not 
(by checking his size).

# L-2 Unsafe ABI encoding

It is not an uncommon practice to use abi.encodeWithSelector to generate calldata for a low-level call.
This function is not type-safe. because the returned values are error-prone and should be considered 
unsafe.

## Recommandation

Consider replacing all occurrences of unsafe ABI encodings with abi.encodeCall, which checks whether the 
supplied values actually match the types expected by the called function, and also avoids typographical
errors.

Occurence in :
[PluginRepoFactory.sol#L122](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L122)

```solidity
createERC1967Proxy(
    pluginRepoBase,
    abi.encodeWithSelector(PluginRepo.initialize.selector, _initialOwner)
)
);
```

[TokenFactory.sol#L89](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L89)

```solidity
if (token != address(0)) {
    // Validate if token is ERC20
    bytes memory data = token.functionStaticCall(
        abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))
    );
```

Réf : openzeppelin

# NC-1 Unused imported contract

Consider removing unecessary imported contracts, for exemple :

[PluginRepo](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L8) in DAOFactory.sol contract
```solidity
import {PluginRepo} from "../plugin/repo/PluginRepo.sol";
```
[IERC1155Upgradeable](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#LL11C59-L11C78) in DAO.sol contract
```solidity
import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";
```
[IDAO](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/membership/IMembership.sol#L5) in IMembership.sol
```solidity
import {IDAO} from "../../dao/IDAO.sol";
```