### [L-01] `_safeMint()` should be used rather than `_mint()` wherever possible
_mint() is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function
```
/src/token/ERC20/governance/GovernanceWrappedERC20.sol

116: super._mint(to, amount);
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L116
```
/src/token/ERC20/governance/GovernanceERC20.sol

107: _mint(to, amount);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L107

### [L-02] Draft OpenZeppelin Dependencies
The PluginUUPSUpgradeable.sol contract utilised draft-IERC1822Upgradeable.sol, an OpenZeppelin contract. This contract are still a draft and are not considered ready for mainnet use.

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.
```
/src/core/plugin/PluginUUPSUpgradeable.sol:

6: import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol";
```

### [L-03] Gas griefing/theft is possible on unsafe external call
return data `(bool success, bytes memory response)` has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided
```
/src/core/dao/DAO.sol

186: (bool success, bytes memory response) = to.call{value: _actions[i].value}(
187:     _actions[i].data
188: );
```

### [L-04] `approve` should be replaced with `safeIncreaseAllowance()` / `safeDecreaseAllowance()`
`approve` should be replaced with `safeApprove` or `safeIncreaseAllowance()` / `safeDecreaseAllowance()`
```
/src/plugins/governance/multisig/Multisig.sol

260: approve(proposalId, _tryExecution);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L186-L188


### [NC-01] Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated.
A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.
plugins/counter-example/MultiplyHelper.sol:    bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
plugins/counter-example/v2/CounterV2.sol:    bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
plugins/counter-example/v1/CounterV1.sol:    bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

### [NC-02] Add a limit for the maximum number of characters per line
Add limit for the maximum number of characters to the following contracts:
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IEIP4824.sol
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol

### [NC-03] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
```
/src/framework/plugin/repo/PluginRepo.sol

19: contract PluginRepo is
20:     Initializable,
21:     ERC165Upgradeable,
22:     IPluginRepo,
23:     UUPSUpgradeable,
24:     PermissionManager
25: {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L19-L25

### [NC-04] Mismatch between the filename and the contract name
```
/framework/utils/ens/ENSMigration.sol

10: contract Dummy {}
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol#L10

### [NC-05] Adding a return statement when the function defines a named return variable, is redundant

```
/src/framework/plugin/setup/PluginSetupProcessor.sol

289: ) external returns (address plugin, IPluginSetup.PreparedSetupData memory preparedSetupData) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L289
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

401: returns (bytes memory initData, IPluginSetup.PreparedSetupData memory preparedSetupData)
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L401

### [NC-06] Proper use of _ as a function name prefix and a common pattern is to prefix internal and private function names with _
To improve code readability add _ to internal/private functions
```
/src/core/permission/PermissionManager.sol

342: function permissionHash(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L342

```
/src/core/permission/PermissionManager.sol

354: function isPermissionRestrictedForAnyAddr(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L354

### [NC-07] Method inherited from interface is missing the override keyword
Add override keyword
```
/src/core/permission/PermissionManager.sol

319: /// @inheritdoc IEIP4824
320: function daoURI() external view returns (string memory) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L319-L320
```
/src/framework/plugin/repo/PluginRepo.sol

127: /// @inheritdoc IPluginRepo
128:     function createVersion(
129:         uint8 _release,
130:         address _pluginSetup,
131:         bytes calldata _buildMetadata,
132:         bytes calldata _releaseMetadata
133:     ) external auth(MAINTAINER_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L127-L133
```
/src/framework/plugin/repo/PluginRepo.sol

187: function updateReleaseMetadata(
188:         uint8 _release,
189:         bytes calldata _releaseMetadata
190:     ) external auth(MAINTAINER_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L186-L190