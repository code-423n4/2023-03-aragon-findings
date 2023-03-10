# QA REPORT

### Summary of low-risk issues


| Number       | Issue details                                           | Instances |
| -------------- | --------------------------------------------------------- | :---------: |
| [L-1](#L1) | ROOT should have full `auth` permissions.                     |    2    |
| [L-2](#L2) | Granting lower level permissions to an address with higher ones should not emit the `Granted` event                      |    1    |

*Total: 2 issues.*

### Summary of non-critical issues


| Number       | Issue details                                           | Instances |
| -------------- | --------------------------------------------------------- | :---------: |
| [NC-1](#NC1) | Lines are too long.                                     |    76    |
| [NC-2](#NC2) | Use of`bytes.concat()` instead of `abi.encodePacked()`. |     3     |

*Total: 2 issues.*

---

### Summary of suggestions


| Number       | Issue details                                           | Instances |
| -------------- | --------------------------------------------------------- | :---------: |
| [S-1](#S1) | When all actions are failable the transaction should revert.             |    1    |

*Total: 1 suggestion.*

---

## Low risk issues

<a id=L1>[L-1]</a> ROOT should have full `auth` permissions.

##### Description

`grant`ing ROOT permission to an address (at all conditions) should mean full auth permissions to that address.
The tests below fail and show that the `ALLOW_FLAG` is not set, despite having `ROOT` access for both `grant` and `grantWithCondition`. 
[Link](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L105-L127) to aforementioned code

#### Proof of concept

Add these two in `permission-manager.ts` file.

```solidity
it('ROOT should have full auth permissions', async () => {
  await pm.grant(pm.address, otherSigner.address, ROOT_PERMISSION_ID); // granting ROOT access
  const permission = await pm.getAuthPermission(
    pm.address,
    otherSigner.address,
    ADMIN_PERMISSION_ID // checking ADMIN permission
  );
  expect(permission).to.be.equal(ALLOW_FLAG);
});

it('ROOT should have full auth permissions at all conditions', async () => {
  await pm.grantWithCondition(
    pm.address,
    otherSigner.address,
    ROOT_PERMISSION_ID, // granting ROOT access
    ALLOW_FLAG
  );
  const permission = await pm.getAuthPermission(
    pm.address,
    otherSigner.address,
    ADMIN_PERMISSION_ID // checking ADMIN permission
  );
  expect(permission).to.be.equal(ALLOW_FLAG);
});
```

<a id=L2>[L-2]</a> Granting lower level permissions to an address with higher ones should not emit the `Granted` event.

##### Description

This issue is closely related to the previous one. Although an address has e.g. ROOT level access, `grant`ing lower level access emits the `Granted` event. 

#### Proof of concept

Add this failing test below in `permission-manager.ts` file.

```solidity
it('should not emit Granted when `who` already has higher level permissions', async () => {
  await pm.grant(pm.address, otherSigner.address, ROOT_PERMISSION_ID);
  await expect(
    pm.grantWithCondition(
      pm.address,
      otherSigner.address,
      ADMIN_PERMISSION_ID,
      ALLOW_FLAG
    )
  ).to.not.emit(pm, 'Granted');
});
```



## Non critical issues

### <a id=NC1>[NC-1]</a> Lines are too long.

##### Description

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

##### Recommendation

Reduce number of characters per line to improve readability.

##### *Instances (76):*

File: [2023-03-aragon/packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L134 )

```solidity
134: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
268: /// @param _input An alias being equivalent to `msg.data`. This feature of the fallback function was introduced with the [solidity compiler version 0.7.6](https://github.com/ethereum/solidity/releases/tag/v0.7.6)
338: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/core/dao/IDAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L40 )

```solidity
40: /// @notice Executes a list of actions. If no failure map is provided, one failing action results in the entire excution to be reverted. If a non-zero failure map is provided, allowed actions can fail without the remaining actions being reverted.
43: /// @param _allowFailureMap A bitmap allowing execution to succeed, even if individual actions might revert. If the bit at index `i` is 1, the execution succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
58: /// @dev The value of `callId` is defined by the component/contract calling the execute function. A `Plugin` implementation can use it, for example, as a nonce.
96: /// @dev This event is intended to be emitted in the `receive` function and is therefore bound by the gas limitations for `send`/`transfer` calls introduced by [ERC-2929](https://eips.ethereum.org/EIPS/eip-2929).
121: /// @notice Checks whether a signature is valid for the provided hash by forwarding the call to the set [ERC-1271](https://eips.ethereum.org/EIPS/eip-1271) signature validator contract.
127: /// @notice Registers an ERC standard having a callback by registering its [ERC-165](https://eips.ethereum.org/EIPS/eip-165) interface ID and callback function signature.
```

File: [2023-03-aragon/packages/contracts/src/core/dao/IEIP4824.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IEIP4824.sol#L9 )

```solidity
9: /// @notice A distinct Uniform Resource Identifier (URI) pointing to a JSON object following the "EIP-4824 DAO JSON-LD Schema". This JSON file splits into four URIs: membersURI, proposalsURI, activityLogURI, and governanceURI. The membersURI should point to a JSON file that conforms to the "EIP-4824 Members JSON-LD Schema". The proposalsURI should point to a JSON file that conforms to the "EIP-4824 Proposals JSON-LD Schema". The activityLogURI should point to a JSON file that conforms to the "EIP-4824 Activity Log JSON-LD Schema". The governanceURI should point to a flatfile, normatively a .md file. Each of the JSON files named above can be statically-hosted or dynamically-generated.
```

File: [2023-03-aragon/packages/contracts/src/core/permission/IPermissionCondition.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/IPermissionCondition.sol#L7 )

```solidity
7: /// @notice This interface can be implemented to support more customary permissions depending on on- or off-chain state, e.g., by querying token ownershop or a secondary condition, respectively.
```

File: [2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L26 )

```solidity
26: /// @notice A mapping storing permissions as hashes (i.e., `permissionHash(where, who, permissionId)`) and their status encoded by an address (unset, allowed, or redirecting to a `PermissionCondition`).
104: /// @dev Note, that granting permissions with `_who` or `_where` equal to `ANY_ADDR` does not replace other permissions with specific `_who` and `_where` addresses that exist in parallel.
113: /// @notice Grants permission to an address to call methods in a target contract guarded by an auth modifier with the specified permission identifier if the referenced condition permits it.
119: /// @dev Note, that granting permissions with `_who` or `_where` equal to `ANY_ADDR` does not replace other permissions with specific `_who` and `_where` addresses that exist in parallel.
134: /// @dev Note, that revoking permissions with `_who` or `_where` equal to `ANY_ADDR` does not revoke other permissions with specific `_who` and `_where` addresses that exist in parallel.
229: /// @dev Note, that granting permissions with `_who` or `_where` equal to `ANY_ADDR` does not replace other permissions with specific `_who` and `_where` addresses that exist in parallel.
281: /// @dev Note, that revoking permissions with `_who` or `_where` equal to `ANY_ADDR` does not revoke other permissions with specific `_who` and `_where` addresses that might have been granted in parallel.
291: /// @notice Checks if a caller is granted permissions on a target contract via a permission identifier and redirects the approval to a `PermissionCondition` if this was specified in the setup.
337: /// @notice Generates the hash for the `permissionsHashed` mapping obtained from the word "PERMISSION", the contract address, the address owning the permission, and the permission identifier.
353: /// @dev By default, every permission is unrestricted and it is the derived contract's responsibility to override it. Note, that the `ROOT_PERMISSION_ID` is included not required to be set it again.
361: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/PluginCloneable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L13 )

```solidity
13: /// @notice An abstract, non-upgradeable contract to inherit from when creating a plugin being deployed via the minimal clones pattern (see [ERC-1167](https://eips.ethereum.org/EIPS/eip-1167)).
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L15 )

```solidity
15: /// @notice An abstract, upgradeable contract to inherit from when creating a plugin being deployed via the UUPS pattern (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
53: /// @notice Returns the address of the implementation contract in the [proxy storage slot](https://eips.ethereum.org/EIPS/eip-1967) slot the [UUPS proxy](https://eips.ethereum.org/EIPS/eip-1822) is pointing to.
59: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
65: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L12 )

```solidity
12: /// @notice An abstract contract providing a meta-transaction compatible modifier for non-upgradeable contracts instantiated via the `new` keyword to authorize function calls through an associated DAO.
29: /// @notice A modifier to make functions on inheriting contracts authorized. Permissions to call the function are checked through the associated DAO's permission manager.
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L12 )

```solidity
12: /// @notice An abstract contract providing a meta-transaction compatible modifier for upgradeable or cloneable contracts to authorize function calls through an associated DAO.
30: /// @notice A modifier to make functions on inheriting contracts authorized. Permissions to call the function are checked through the associated DAO's permission manager.
37: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/proposal/IProposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L18 )

```solidity
18: /// @param allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/proposal/Proposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L12 )

```solidity
12: /// @notice An abstract contract containing the traits and internal functionality to create and execute proposals that can be inherited by non-upgradeable DAO plugins.
42: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
69: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L42 )

```solidity
42: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
69: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
82: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/core/utils/CallbackHandler.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L7 )

```solidity
7: /// @notice This contract handles callbacks by registering a magic number together with the callback function's selector. It provides the `_handleCallback` function that inherting have to call inside their `fallback()` function  (`_handleCallback(msg.callbackSelector, msg.data)`).  This allows to adaptively register ERC standards (e.g., [ERC-721](https://eips.ethereum.org/EIPS/eip-721), [ERC-1115](https://eips.ethereum.org/EIPS/eip-1155), or future versions of [ERC-165](https://eips.ethereum.org/EIPS/eip-165)) and returning the required magic numbers for the associated callback functions for the inheriting contract so that it doesn't need to be upgraded.
52: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/core/utils/auth.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol#L14 )

```solidity
14: /// @notice A free function checking if a caller is granted permissions on a target contract via a permission identifier that redirects the approval to a `PermissionCondition` if this was specified in the setup.
```

File: [2023-03-aragon/packages/contracts/src/framework/dao/DAORegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L72 )

```solidity
72: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L14 )

```solidity
14: /// @notice Creates a new plugin version as the latest build for an existing release number or the first build for a new release number for the provided `PluginSetup` contract address and metadata.
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L255 )

```solidity
255: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L41 )

```solidity
41: /// @param _maintainer The maintainer of the plugin repo. This address has permission to update metadata, upgrade the repo logic, and manage the repo permissions.
61: /// @notice Set the final permissions for the published plugin repository maintainer. All permissions are revoked from the plugin factory and granted to the specified plugin maintainer.
111: /// @notice Internal method creating a `PluginRepo` via the [ERC-1967](https://eips.ethereum.org/EIPS/eip-1967) proxy pattern from the provided base contract and registering it in the Aragon plugin registry.
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L71 )

```solidity
71: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L23 )

```solidity
23: /// @param data The bytes-encoded data containing the input parameters for the preparation of update/uninstall as specified in the corresponding ABI on the version's metadata.
63: /// @dev The implementation can be instantiated via the `new` keyword, cloned via the minimal clones pattern (see [ERC-1167](https://eips.ethereum.org/EIPS/eip-1167)), or proxied via the UUPS pattern (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L29 )

```solidity
29: /// @notice A convenience function to create an [ERC-1967](https://eips.ethereum.org/EIPS/eip-1967) proxy contract pointing to an implementation and being associated to a DAO.
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L17 )

```solidity
17: import {PluginSetupRef, hashHelpers, hashPermissions, _getPreparedSetupId, _getAppliedSetupId, _getPluginInstallationId, PreparationType} from "./PluginSetupProcessorHelpers.sol";
49: /// @param currentAppliedSetupId The current setup id that plugin holds. Needed to confirm that `prepareUpdate` or `prepareUninstallation` happens for the plugin's current/valid dependencies.
50: /// @param preparedSetupIdToBlockNumber The mapping between prepared setup IDs and block numbers at which `prepareInstallation`, `prepareUpdate` or `prepareUninstallation` was executed.
63: /// @param data The bytes-encoded data containing the input parameters for the installation preparation as specified in the corresponding ABI on the version's metadata.
85: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
86: ///  This includes the bytes-encoded data containing the input parameters for the update preparation as specified in the corresponding ABI on the version's metadata.
110: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
111: ///  This includes the bytes-encoded data containing the input parameters for the uninstallation preparation as specified in the corresponding ABI on the version's metadata.
158: /// @notice Thrown if a prepared setup ID is not eligible to be applied. This can happen if another setup has been already applied or if the setup wasn't prepared in the first place.
213: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
245: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
395: /// @dev The list of `_params.setupPayload.currentHelpers` has to be specified in the same order as they were returned from previous setups preparation steps (the latest `prepareInstallation` or `prepareUpdate` step that has happend) on which the update is prepared for.
551: /// @dev The list of `_params.setupPayload.currentHelpers` has to be specified in the same order as they were returned from previous setups preparation steps (the latest `prepareInstallation` or `prepareUpdate` step that has happend) on which the uninstallation was prepared for.
611: /// @dev The list of `_params.setupPayload.currentHelpers` has to be specified in the same order as they were returned from previous setups preparation steps (the latest `prepareInstallation` or `prepareUpdate` step that has happend) on which the uninstallation was prepared for.
649: /// @dev If the block number stored in `states[pluginInstallationId].blockNumber` exceeds the one stored in `pluginState.preparedSetupIdToBlockNumber[preparedSetupId]`, the prepared setup with `preparedSetupId` is outdated and not applicable anymore.
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L41 )

```solidity
41: /// @param _preparationType The type of preparation the plugin is currently undergoing. Without this, it is possible to call `applyUpdate` even after `applyInstallation` is called.
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52 )

```solidity
52: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
74: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L9 )

```solidity
9: /// @dev This function allows empty (zero-length) subdomains. If this should not be allowed, make sure to add a respective check when using this function in your code.
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/TokenFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L72 )

```solidity
72: /// @notice Creates a new `GovernanceERC20` token or a `GovernanceWrappedERC20` from an existing [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token depending on the address used in the `TokenConfig` provided.
74: /// @param _tokenConfig The token configuration struct containing the name, and symbol of the token to be create, but also an address. For `address(0)`, a new governance token is created. For any other address pointing to an [ERC-20](https://eips.ethereum.org/EIPS/eip-20)-compatible contract, a wrapped governance token is created.
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L15 )

```solidity
15: /// @notice This contract registers ENS subdomains under a parent domain specified in the initialization process and maintains ownership of the subdomain since only the resolver address is set. This contract must either be the domain node owner or an approved operator of the node owner. The default resolver being used is the one specified in the parent domain.
72: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
110: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

### <a id=NC2>[NC-2]</a> Use of `bytes.concat()` instead of `abi.encodePacked()`.

##### Description

Rather than using `abi.encodePacked` for appending bytes, since version `0.8.4`, `bytes.concat()` is enabled.

##### Recommendation

Since version `0.8.4` for appending bytes, `bytes.concat()` can be used instead of `abi.encodePacked()`.

##### *Instances (3):*

File: [2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L347 )

```solidity
347: return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L252 )

```solidity
252: return keccak256(abi.encodePacked(_tag.release, _tag.build));
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86 )

```solidity
86: bytes32 subnode = keccak256(abi.encodePacked(node, _label));
```

---

## Suggestions

### <a id=S1>[S-1]</a> When all actions are failable the transaction should revert.

##### Description

According to the [documentation](https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/docs/osx/01-how-it-works/01-core/01-dao/01-actions.md#allowing-for-failure), `actions` can fail as long as it is the intention of the DAO that implements them and they are included in `allowFailureMap`.

I believe in an edge case where all the `actions` fail, the transaction should revert, so that the end user is better informed on what occured.
I have included a modified test ([from `dao.ts`](https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/test/core/dao/dao.ts)), where it is shown that the transaction succeeds even though all 256 actions fail.


```solidity
it('transaction succeeds when all actions fail', async () => {
    let allowFailureMap = ethers.BigNumber.from(0);
    let actions = [];

    for (let i = 0; i < MAX_ACTIONS; i++) {
        actions[i] = data.failAction;
    }

    // add all actions in the allowFailureMap
    // to make sure tx succeeds.
    for (let i = 0; i < MAX_ACTIONS; i++) {
        allowFailureMap = flipBit(i, allowFailureMap);
    }

    // If the below call not fails, means allowFailureMap is correct.
    let tx = await dao.execute(ZERO_BYTES32, actions, allowFailureMap);
    let event = await findEvent(tx, EVENTS.Executed);

    expect(event.args.actor).to.equal(ownerAddress);
    expect(event.args.callId).to.equal(ZERO_BYTES32);

    // construct the failureMap which only has those
    // bits set at indexes where actions failed
    let failureMap = ethers.BigNumber.from(0);
    for (let i = 0; i < MAX_ACTIONS; i++) {
        failureMap = flipBit(i, failureMap);
    }
    // Check that dao crrectly generated failureMap
    expect(event.args.failureMap).to.equal(failureMap);

    // Check that execResult emitted correctly stores action results.
    for (let i = 0; i < MAX_ACTIONS; i++) {
        expect(event.args.execResults[i]).to.includes(data.failActionMessage);
        expect(event.args.execResults[i]).to.includes(errorSignature);
    }
    // for (let i = 3; i < 6; i++) {
    //   expect(event.args.execResults[i]).to.equal(data.successActionResult);
    // }

    // lets remove one of the action from allowFailureMap
    // to see tx will actually revert.
    allowFailureMap = flipBit(200, allowFailureMap);
    await expect(dao.execute(ZERO_BYTES32, actions, allowFailureMap))
        .to.be.revertedWithCustomError(dao, 'ActionFailed')
        .withArgs(200); // Since we unset the 200th action from failureMap, it should fail with that index.
});
```