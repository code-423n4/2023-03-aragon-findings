
## Non Critical Issues

### [N-1] Use of block.timestamp
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
#### Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers — i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

Instances where block.timestamp is used:

*Find (7) instance(s) in contracts*:
```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

67:         uint64 currentTimestamp64 = block.timestamp.toUint64();

```
[packages/contracts/src/plugins/governance/admin/Admin.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/admin/Admin.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

515:         uint64 currentTime = block.timestamp.toUint64();

567:         uint64 currentTimestamp = block.timestamp.toUint64();

```
[packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

221:             _startDate = block.timestamp.toUint64();

222:         } else if (_startDate < block.timestamp.toUint64()) {

223:             revert DateOutOfBounds({limit: block.timestamp.toUint64(), actual: _startDate});

404:         uint64 currentTimestamp64 = block.timestamp.toUint64();

```
[packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol)


### [N-2] Unused imports

*Find (12) instance(s) in contracts*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

11: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";

18: import {hasBit, flipBit} from "../utils/BitMap.sol";

```
[packages/contracts/src/core/dao/DAO.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

6: import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol";

```
[packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

10: import {hashHelpers, PluginSetupRef} from "../plugin/setup/PluginSetupProcessorHelpers.sol";

```
[packages/contracts/src/framework/dao/DAOFactory.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/dao/DAOFactory.sol)

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

8: import {isSubdomainValid} from "../utils/RegistryUtils.sol";

```
[packages/contracts/src/framework/dao/DAORegistry.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/dao/DAORegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

8: import {isSubdomainValid} from "../../utils/RegistryUtils.sol";

```
[packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

17: import {PluginSetupRef, hashHelpers, hashPermissions, _getPreparedSetupId, _getAppliedSetupId, _getPluginInstallationId, PreparationType} from "./PluginSetupProcessorHelpers.sol";

```
[packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)

```solidity
File: packages/contracts/src/framework/utils/ens/ENSMigration.sol

7: import "@ensdomains/ens-contracts/contracts/registry/ENSRegistry.sol";

8: import "@ensdomains/ens-contracts/contracts/resolvers/PublicResolver.sol";

```
[packages/contracts/src/framework/utils/ens/ENSMigration.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/utils/ens/ENSMigration.sol)

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

7: import {_uncheckedAdd, _uncheckedSub} from "../../utils/UncheckedMath.sol";

```
[packages/contracts/src/plugins/utils/Addresslist.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/utils/Addresslist.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

5: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```
[packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

8: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```
[packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)

### [N-3] Unused parameters

*Find (3) instance(s) in contracts*:
```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

19:         address _dao,

20:         uint16 _currentBuild,

21:         SetupPayload calldata _payload

```
[packages/contracts/src/framework/plugin/setup/PluginSetup.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/plugin/setup/PluginSetup.sol)


### [N-4] Constant values such as a call to keccak256(), should used to immutable rather than constant
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.
  
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

*Find (39) instance(s) in contracts*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");

43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");

46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");

49:     bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =

53:     bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =

57:     bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =

```
[packages/contracts/src/core/dao/DAO.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/core/permission/PermissionLib.sol

10:     address public constant NO_CONDITION = address(0);

```
[packages/contracts/src/core/permission/PermissionLib.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/permission/PermissionLib.sol)

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

18:     address internal constant ANY_ADDR = address(type(uint160).max);

21:     address internal constant UNSET_FLAG = address(0);

24:     address internal constant ALLOW_FLAG = address(2);

```
[packages/contracts/src/core/permission/PermissionManager.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/permission/PermissionManager.sol)

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

```
[packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)

```solidity
File: packages/contracts/src/core/utils/CallbackHandler.sol

14:     bytes4 internal constant UNREGISTERED_CALLBACK = bytes4(0);

```
[packages/contracts/src/core/utils/CallbackHandler.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/utils/CallbackHandler.sol)

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

```
[packages/contracts/src/framework/dao/DAORegistry.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/dao/DAORegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

```
[packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

16:     bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =

```
[packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

27:     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =

31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

34:     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =

```
[packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

18:     bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =

```
[packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol)

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

18:     bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =

22:     bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =

```
[packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
[packages/contracts/src/plugins/counter-example/MultiplyHelper.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol)

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
[packages/contracts/src/plugins/counter-example/v1/CounterV1.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol)

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
[packages/contracts/src/plugins/counter-example/v2/CounterV2.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol)

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

19:     bytes4 internal constant ADMIN_INTERFACE_ID =

23:     bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =

```
[packages/contracts/src/plugins/governance/admin/Admin.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/admin/Admin.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

172:     bytes4 internal constant MAJORITY_VOTING_BASE_INTERFACE_ID =

182:     bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =

```
[packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

23:     bytes4 internal constant ADDRESSLIST_VOTING_INTERFACE_ID =

27:     bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =

```
[packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

22:     bytes4 internal constant TOKEN_VOTING_INTERFACE_ID =

```
[packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

64:     bytes4 internal constant MULTISIG_INTERFACE_ID =

71:     bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =

```
[packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol)

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

27:     bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =

```
[packages/contracts/src/plugins/token/MerkleMinter.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/token/MerkleMinter.sol)

```solidity
File: packages/contracts/src/plugins/utils/Ratio.sol

6: uint256 constant RATIO_BASE = 10 ** 6;

```
[packages/contracts/src/plugins/utils/Ratio.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/plugins/utils/Ratio.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```
[packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)


## Low Issues



### [L-1] Draft Openzeppelin Dependencies
The contracts adopted OpenZeppelin draft contracts and are not considered ready for mainnet use.
OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

*Find (3) instance(s) in contracts*:
```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

6: import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol";

```
[packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

5: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```
[packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

8: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```
[packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol](https://github.com/aragon/osx/tree/develop/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)

### [L-2] Usage of draft proposal EIP-1822
Multiple methods across contracts are required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822), but it is just a draft proposal. 
The use of it in a smart contract may have potential security issues, may encounter significant changes in the future, or may be abandoned, which can bring a lot of costs to development and maintenance.
#### Recommended Mitigation Steps
Using EIPs that have reached the Final stage is recommended.

*Find (18) instance(s) in contracts*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

100:          /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
                                                                     
134:          /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).                                   
```
[packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

15:        /// @notice An abstract, upgradeable contract to inherit from when creating a plugin being deployed via the UUPS pattern (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).

53:      /// @notice Returns the address of the implementation contract in the [proxy storage slot](https://eips.ethereum.org/EIPS/eip-1967) slot the [UUPS proxy](https://eips.ethereum.org/EIPS/eip-1822) is pointing to.

59:       /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).                                                        
```
[packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

119:        /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).

255:     /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).                                                
```
[packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

63:          /// @dev The implementation can be instantiated via the `new` keyword, cloned via the minimal clones pattern (see [ERC-1167](https://eips.ethereum.org/EIPS/eip-1167)), or proxied via the UUPS pattern (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).

```
[packages/contracts/src/framework/plugin/setup/IPluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

141:         /// @notice Thrown if the upgrade of an `UUPSUpgradeable` proxy contract (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)) failed.

660:    /// @notice Upgrades a UUPS upgradeable proxy contract (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```
[packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

52:         /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```
[packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol)

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

72:    /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).

```
[packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

235:        /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).

```
[packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

31:          /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).

```
[packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

32:         /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
```
[packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

122:         /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
```
[packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

42:         /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
```
[packages/contracts/src/plugins/token/MerkleDistributor.sol
](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

37:         /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
```
[packages/contracts/src/plugins/token/MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol)

### [L-3] Usage of draft proposal EIP-4824
Multiple methods across contracts are required to support [ERC-4824](https://eips.ethereum.org/EIPS/eip-4824), but it is just a draft proposal, which the project cannot assume to be supported by any other contract.
The use of it in a smart contract may have potential security issues, may encounter significant changes in the future, or may be abandoned, which can bring a lot of costs to development and maintenance.
#### Recommended Mitigation Steps
Using EIPs that have reached the Final stage is recommended.

*Find (3) instance(s) in contracts*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

69:         /// @notice The [EIP-4824](https://eips.ethereum.org/EIPS/eip-4824) DAO uri.

```
[packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol)
```solidity
File: packages/contracts/src/core/dao/IEIP4824.sol

5:        /// @title EIP-4824 Common Interfaces for DAOs
```
[packages/contracts/src/core/dao/IEIP4824.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IEIP4824.sol)
```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

29:             /// @param daoURI The DAO uri used with [EIP-4824](https://eips.ethereum.org/EIPS/eip-4824).

```
[packages/contracts/src/framework/dao/DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol)
