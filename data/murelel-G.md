# Gas Optimizations

## \[GAS-01\] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### Proof Of Concept

```solidity
95: for (uint256 i; i < _pluginSettings.length; ++i) {
```

DAOFactory.sol

```solidity
16: for (uint256 i; i < nameLength; i++) {`
```

## \[G‑02\] Using `storage` instead of `memory` for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

*plugin/setup/PluginSetupProcessor.sol*

#### <ins>Proof Of Concept</ins>
*There are  instances of this issue:*

```solidity

298: PluginRepo.Version memory version = pluginSetupRepo.getVersion(
            _params.pluginSetupRef.versionTag
        );
		
433: PluginRepo.Version memory currentVersion = _params.pluginSetupRepo.getVersion(
            _params.currentVersionTag
        );

437: PluginRepo.Version memory newVersion = _params.pluginSetupRepo.getVersion(
            _params.newVersionTag
        );

523: PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(

572: PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(
            _params.pluginSetupRef.versionTag
        );
```

plugin/setup/PluginSetupProcessor.sol*

## \[G‑03\] Assigning keccak operations to constant variables results in extra gas costs
"constants" expressions are expressions. As such, keccak assigned to a constant variable are re-computed at each use of the variable, which will consume gas unnecessarily. To save gas, Changing the variable from constant to immutable will make the computation run only once and therefore save gas.

#### Proof Of Concept

```solidity
File: packages/contracts/src/core/dao/DAO.sol

40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");

43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");

46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

15:  bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

31:  bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");



```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)
```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol)
```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol)

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol)
```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

27:     bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)

