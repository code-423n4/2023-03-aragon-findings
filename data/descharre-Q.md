# Summary
## Low Risk
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|L-01       |Missing 0 address checks|4|
|L-02       |Account existence check for low-level calls|1|
|L-03       |Signature validater does not always return `0x1626ba7e`|1|
|L-04       |Be consistent with checking the endDate | 1 |


## Non critical
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|N-01       | Constant values such as a call to keccak256(), should be immutable rather than constant | - |
|N-02       | Event is missing indexed fields | 5 |
|N-03       | Be consistent with setting events in interfaces | 7 |
|N-04       | Timestamps don't need to be bigger than uint48 | - |
|N-05       | Don't use access control on view functions | 1 |
|N-06       | Use scientific notation (1E6) rather than exponentation (10**6)  | 2 |
|N-07       | Forgotton params variables in natspec comments| 11 |

# Details
## Low Risk
## [L-01] Missing 0 address checks
The function [`_setTrustedForwarder()`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L139-L143) sets the trusted forwarder on the DAO. This is a single step process and has no checks. A 0 address check can be used to prevent that the trusted forwarder can be set to 0. Allowing zero-addresses will lead to contract reverts and force redeployments if there are no setters for such address variables.

More 0 address checks missing:
- [DAO.sol#L239-L245](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L239-L245)
- [DaoAuthorizable.sol#L20](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L20)
- [DAOFactory.sol#L54-L55](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L54-L55)
## [L-02] Account existence check for low-level calls
Low-level calls call/delegatecall/staticcall return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed. If the `to` address is 0 by accident, the return value of success will be true. 
- [DAO.sol#L186](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L186)
## [L-03] Signature validater does not always return `0x1626ba7e`
The function [`isValidSignature()`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L248-L258) forwards the call to the EIP 1271 signatureValidator. The [natspec](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L124) comment says the function returns the `bytes4` magic value `0x1626ba7e` if the signature is valid. 

However as stated in [EIP 1271](https://eips.ethereum.org/EIPS/eip-1271#:~:text=not%20EOA%20signatures.-,Reference,-Implementation), the function `isValidSignature` of signatureValidator can have two different return values. If the signature comes from the owner it will return `0x1626ba7e` as stated in the natspec comments. Else the function will return `0xffffffff`.

The return value should be checked and revert if value is not equal to `0x1626ba7e`;
## [L-04] Be consistent with checking the endDate
[`Multisig`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L403-L409) and [`MajorityVotingBase`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L515-L522) both have the function `_isProposalOpen`.

The function in `MajorityVotingBase` will return false if the `endDate` is equal to the current time. It can only be smaller. It's done better in `Multisig`. Here it will only return false if the `endDate` is past the current time. The same should be done in `MajorityVotingBase`. This way you can vote when the proposal is technically still open.

```solidity
Multisig:

    return
        !proposal_.executed &&
        proposal_.parameters.startDate <= currentTimestamp64 &&
        proposal_.parameters.endDate >= currentTimestamp64;

MajorityBase:

    return
        proposal_.parameters.startDate <= currentTime &&
        currentTime < proposal_.parameters.endDate &&
        !proposal_.executed;
```
## Non critical
## [N-01] Constant values such as a call to keccak256(), should be immutable rather than constant
Constants are supposed to be used for literal values written into the code, and immutable variables should be used for expressions or values calculated in the constructor. While it doesn't save any gas, they should be used in their appropriate context.
- [DAO.sol#L40](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40)
- [DAO.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L43)
- [DAO.sol#L46](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L46)
- [DAO.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L49)
- [DAO.sol#L53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L53)
- [DAO.sol#L57](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L57)
- [PermissionManager.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L15)
- [PluginUUPSUpgradeable.sol#L35](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35)
- [DAORegistry.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15) 
- [PluginRepo.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49)
- [PluginRepo.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52)
- [PluginRepoRegistry.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16)
- [PluginSetupProcessor.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27)
- [PluginSetupProcessor.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31)
- [PluginSetupProcessor.sol#L34](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L34)
- [InterfaceBasedRegistry.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18)
- [ENSSubdomainRegistrar.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18)
- [ENSSubdomainRegistrar.sol#L22](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22)
- [MultiplyHelper.sol#L12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12)
- [CounterV1.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14)
- [CounterV2.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14)
- [Admin.sol#L23](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L23)
- [MajorityVotingBase.sol#L183](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183)
- [AddresslistVoting.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27)
- [Multisig.sol#L71](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71)
## [N-02] Event is missing indexed fields
When an event is missing indexed fields it can be hard for off-chain scripts to efficiently filter those events.
- [DAO.sol#L77](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L77)
- [IDAO.sol#L99](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L99)
- [IDAO.sol#L71-L75](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L71-L75)
- [PluginRepo.sol#L109](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L109)
- [TokenFactory.sol#L42-L46](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L42-L46)
- [MajorityVotingBase.sol#L226-L232](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L226-L232)
## [N-03] Be consistent with setting events and errors in interfaces
If you specify events  in the interface, be consistent with it and do it for every event. In [IDAO](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol) interface, events  are defined. But the [DAO](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L89) contract that implements the interface also has one event. It's better to also put this event in the interface.
## [N-04] Timestamps don't need to be bigger than uint48
The maximum value of uint48 is year 8 million 921 thousand 556. Uint40, is probably okay too. It will be year 36,812 when it overflows. For block numbers, uint48 is big enough as well. Even with 10000 blocks per second, it would be enough until year 2861. In this project uint64 is mostly used for timestamps.
## [N-05] Don't use access control on view functions
They can't change the state. And all state is public anyway
- [MultiplyHelper.sol#L19](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L19)
- [CounterV1.sol#L39](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L39)
- [CounterV2.sol#L58](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L58)
## [N-06] Use scientific notation (1E6) rather than exponentation (10**6)
Scientific notation should be used for better code readability.
- [Ratio.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Ratio.sol#L6)
## [N-07] Forgotton params in natspec comments
- [Multisig.sol#L121-L136](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L121-L136): `_members` is not specified in natspec
- [Multisig.sol#L198-L262](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L198-L262): `_startDate` and `_endDate`
