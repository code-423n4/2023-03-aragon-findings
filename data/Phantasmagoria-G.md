## Gas Optimizations Summary
| |Issue|Total Gas Saved|
|-|:-|:-:|
| [G-01] | Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead |  - |
| [G-02] | ++i / i++ should be unchecked{++i}/unchecked{i++}  | 20 |
| [G-03] | Not using the named return variables when a function returns, wastes deployment gas |   - |
| [G-04] | Use constants instead of type(uintx).max | - |
| [G-05] | Setting the constructor to payable |  143 |
| [G-06] | Multiple accesses of a function should use a local variable cache  | - |
| [G-07] | Multiple accesses of a mapping/array should use a local variable cache | 126 |
| [G-08] | Use nested if and, avoid multiple check combinations |  24 |
| [G-09] | Internal/Private functions only called once can be inlined to save gas |  160 |
| [G-10] | Internal functions not called by the contract should be removed to save deployment gas |  - |
| [G-11] | Duplicated if checks should be refactored to a modifier or function |  - |
| [G-12] | Using storage instead of memory for structs/arrays saves gas  |  2100 |
| [G-13] | Using immutable on variables that are only set in the constructor and never after |  4194 |
| [G-14] | Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | - |
| [G-15] | abi.encode() is less efficient than abi.encodePacked()  |  - |
| [G-16] | Functions guaranteed to revert when called by normal users can be marked payable| 315 |
| [G-17] | Unused imports  |  - |
| [G-18] | Optimize names to save gas  | 220 |

## Gas Optimizations
### [G-01] Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Use a larger size then downcast where needed.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

```
/src/core/plugin/proposal/Proposal.sol

48: uint64 _startDate,
49: uint64 _endDate,
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L48-L49
```
/src/core/plugin/proposal/ProposalUpgradeable.sol

48: uint64 _startDate,
49: uint64 _endDate,
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L48-L49
```
/src/framework/plugin/setup/PluginSetup.sol

20: uint16 _currentBuild,
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L20

### [G-02] `++i / i++` should be `unchecked{++i}/unchecked{i++}` When It Is Not Possible For Them To Overflow
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP
```
/src/framework/dao/DAOFactory.sol

95: for (uint256 i; i < _pluginSettings.length; ++i) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95

### [G-03] Not using the named return variables when a function returns, wastes deployment gas
Do not use return at the end of the function
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

### [G-04] Use constants instead of type(uintx).max
type(uint160).max uses more gas in the distribution process and also for each transaction than constant usage.
```
/src/core/permission/PermissionManager.sol

18: address internal constant ANY_ADDR = address(type(uint160).max);
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L18

### [G-05] Setting the constructor to payable
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.

```
/src/core/dao/DAO.sol

92: constructor() {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L92
```
/src/framework/dao/DAOFactory.sol

53: constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L53
```
/src/framework/utils/TokenFactory.sol

68: constructor() {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L68
```
/src/framework/plugin/repo/PluginRepoFactory.sol

22: constructor(PluginRepoRegistry _pluginRepoRegistry) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22
```
/src/framework/plugin/repo/PluginRepoRegistry.sol

34: constructor() {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L34
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

277: constructor(PluginRepoRegistry _repoRegistry) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277
```
/src/framework/utils/ens/ENSSubdomainRegistrar.sol

45: constructor() {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L45
```
/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

19: constructor(IDAO _dao) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19
```
/src/core/plugin/Plugin.sol

17: constructor(IDAO _dao) DaoAuthorizable(_dao) {}
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/Plugin.sol#L17
```
/src/core/plugin/PluginCloneable.sol

16: constructor() {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L16
```
/src/core/plugin/PluginUUPSUpgradeable.sol

25: constructor() {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25

### [G-06] Multiple accesses of a function should use a local variable cache
It is better to cache result of a function call rather than re-calling the function
 GovernanceWrappedERC20(token)
```
/src/framework/utils/TokenFactory.sol

100: GovernanceWrappedERC20(token).initialize(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L100
```
/src/framework/utils/TokenFactory.sol

106: emit WrappedToken(GovernanceWrappedERC20(token));
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L106
GovernanceERC20(token)
```
/src/framework/utils/TokenFactory.sol

112: GovernanceERC20(token).initialize(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L112
```
/src/framework/utils/TokenFactory.sol

130: bytes32 tokenMintPermission = GovernanceERC20(token).MINT_PERMISSION_ID();
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L130
MerkleMinter(merkleMinter)
```
/src/framework/utils/TokenFactory.sol

121: MerkleMinter(merkleMinter).initialize(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L121
```
/src/framework/utils/TokenFactory.sol

128: emit TokenCreated(IERC20Upgradeable(token), MerkleMinter(merkleMinter), distributorBase);
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L128
```
/src/framework/utils/TokenFactory.sol

131: bytes32 merkleMintPermission = MerkleMinter(merkleMinter).MERKLE_MINT_PERMISSION_ID();
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L131
```
/src/framework/utils/TokenFactory.sol

140: return (ERC20VotesUpgradeable(token), MerkleMinter(merkleMinter));
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L140

implementation()
```
527: address currentImplementation = PluginUUPSUpgradeable(_params.plugin).implementation();
528: address newImplementation = PluginSetup(version.pluginSetup).implementation();
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L527-L528

### [G-07] Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory/calldata.

permissionsHashed[permHash]
```
/src/core/permission/PermissionManager.sol

284: if (permissionsHashed[permHash] != UNSET_FLAG) {
285:     permissionsHashed[permHash] = UNSET_FLAG;
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L284-L285
```
/src/core/permission/PermissionManager.sol

253: address currentCondition = permissionsHashed[permHash];
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L253
```
/src/core/permission/PermissionManager.sol

258: permissionsHashed[permHash] = newCondition;
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L258

latestTagHashForPluginSetup[_pluginSetup]
```
/src/framework/plugin/repo/PluginRepo.sol

156: Version storage version = versions[latestTagHashForPluginSetup[_pluginSetup]];
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L156
```
/src/framework/plugin/repo/PluginRepo.sol

172: latestTagHashForPluginSetup[_pluginSetup] = _tagHash;
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L172

entries[_registrant]
```
/src/framework/utils/InterfaceBasedRegistry.sol

62: if (entries[_registrant]) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L62
```
/src/framework/utils/InterfaceBasedRegistry.sol

71: entries[_registrant] = true;
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L71

### [G-08]Use nested if and, avoid multiple check combinations
Instead of using the && operator in a single if statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

700: if (
701:     msg.sender != _dao &&
702:     !DAO(payable(_dao)).hasPermission(address(this), msg.sender, _permissionId, bytes(""))
703:    ) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L700-L703
```
/src/framework/plugin/repo/PluginRepo.sol

157: if (version.tag.release != 0 && version.tag.release != _release) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L157
```
/src/core/permission/PermissionManager.sol

236: if (_where == ANY_ADDR && _who == ANY_ADDR) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L236

### [G-09] Internal/Private functions only called once can be inlined to save gas
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.
When we define internal functions to perform computation:
- The contract’s code size gets bigger
- the function call consumes more gas than executing it as an inlined function (part of the code, without the function call)

When it does not affect readability, it is recommended to inline functions in order to save gas
```
/src/framework/utils/TokenFactory.sol

144: function setupBases() private {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L144
```
/src/core/dao/DAO.sol

290: function _registerTokenInterfaces() private {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L290
```
/src/framework/dao/DAOFactory.sol

141: function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L141
```
/src/framework/dao/DAOFactory.sol

156: function _setDAOPermissions(DAO _dao) internal {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L156
```
/src/framework/plugin/repo/PluginRepoFactory.sol

65: function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L65
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

664: function _upgradeProxy(
665:        address _proxy,
666:        address _implementation,
667:        bytes memory _initData
668: ) private {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L664-L668
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

669: function _canApply(address _dao, bytes32 _permissionId) private view {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L699
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

719: function emitPrepareUpdateEvent(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L719

### [G-10] Internal functions not called by the contract should be removed to save deployment gas
Consider to remove these functions
```
/src/core/permission/PermissionManager.sol

95: function __PermissionManager_init(address _initialOwner) internal onlyInitializing {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L95
```
/src/core/dao/DAO.sol

122: function isPermissionRestrictedForAnyAddr(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L122

### [G-11] Duplicated if checks should be refactored to a modifier or function
Saves deployment costs
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

321: if (pluginState.currentAppliedSetupId != bytes32(0)) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L321
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

326: if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L326
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

378: if (_params.permissions.length > 0) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L378
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

426: if (pluginState.currentAppliedSetupId != appliedSetupId) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L426
```
/src/framework/plugin/repo/PluginRepo.sol

150: if (_releaseMetadata.length == 0) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L150
```
/src/framework/plugin/repo/PluginRepo.sol

138: if (_release == 0) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L138

### [G-12] Using storage instead of memory for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct
```
/src/framework/plugin/repo/PluginRepo.sol

167: Tag memory tag = Tag(_release, build);
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L167

### [G-13] Using immutable on variables that are only set in the constructor and never after 
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)
```
/src/framework/plugin/repo/PluginRepoFactory.sol

25: pluginRepoBase = address(new PluginRepo());
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L25
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

278: repoRegistry = _repoRegistry;
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L278

### [G-14] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue
```
/src/framework/utils/InterfaceBasedRegistry.sol

18: bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18
```
/src/framework/utils/ens/ENSSubdomainRegistrar.sol

18: bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18
```
/src/framework/utils/ens/ENSSubdomainRegistrar.sol

22: bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

27: bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

31: bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31
```
/src/framework/plugin/setup/PluginSetupProcessor.sol

34: bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L34
```
/src/framework/plugin/repo/PluginRepoRegistry.sol

16: bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16
```
/src/framework/dao/DAORegistry.sol

15: bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15
```
/src/framework/plugin/repo/PluginRepo.sol

49: bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49
```
/src/framework/plugin/repo/PluginRepo.sol

52: bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52
```
/src/core/dao/DAO.sol

40: bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40
```
/src/core/dao/DAO.sol

43: bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L43
```
/src/core/dao/DAO.sol

46: bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L46
```
/src/core/dao/DAO.sol

50: bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L50
```
/src/core/dao/DAO.sol

53: bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L53
```
/src/core/dao/DAO.sol

57: bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L57
```
/src/core/permission/PermissionManager.sol

15: bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L15
```
/src/core/plugin/PluginUUPSUpgradeable.sol

35: bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35

### [G-15] abi.encode() is less efficient than abi.encodePacked()
Changing abi.encode function to abi.encodePacked can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient (see [Solidity-Encode-Gas-Comparison](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison))
Consider using abi.encodePacked() here:
```
/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

33: return keccak256(abi.encode(_dao, _plugin));
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L33
```
/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

52: abi.encode(
53:     _pluginSetupRef.versionTag,
54:     _pluginSetupRef.pluginSetupRepo,
55:     _permissionsHash,
56:     _helpersHash,
57:     keccak256(_data),
58:     _preparationType
59: )
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L52-L59
```
/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

73: abi.encode(_pluginSetupRef.versionTag, _pluginSetupRef.pluginSetupRepo, _helpersHash)
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L73
```
/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

80: return keccak256(abi.encode(_helpers));
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L80
```
/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

89: return keccak256(abi.encode(_permissions));
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L89

### [G-16] Functions guaranteed to revert when called by normal users can be marked payable
If a function modifier such as auth() is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.
```
/src/framework/utils/ens/ENSSubdomainRegistrar.sol

82: function registerSubnode(
83:         bytes32 _label,
84:         address _targetAddress
85: ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L82-L85
```
/src/framework/utils/ens/ENSSubdomainRegistrar.sol

100: function setDefaultResolver(
101:         address _resolver
102:    ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L100-L102
```
/src/framework/plugin/repo/PluginRepoRegistry.sol

51: function registerPluginRepo(
52:         string calldata subdomain,
53:         address pluginRepo
54:     ) external auth(REGISTER_PLUGIN_REPO_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L51-L54
```
/src/framework/plugin/repo/PluginRepo.sol

128: function createVersion(
129:         uint8 _release,
130:         address _pluginSetup,
131:         bytes calldata _buildMetadata,
132:         bytes calldata _releaseMetadata
133:     ) external auth(MAINTAINER_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L128-L133
```
/src/framework/plugin/repo/PluginRepo.sol

187: function updateReleaseMetadata(
188:         uint8 _release,
189:         bytes calldata _releaseMetadata
190:     ) external auth(MAINTAINER_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L187-L190
```
/src/framework/dao/DAORegistry.sol

50: function register(
51:         IDAO dao,
52:         address creator,
53:         string calldata subdomain
54:     ) external auth(REGISTER_DAO_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L50-L54
```
/src/core/dao/DAO.sol

139: function setTrustedForwarder(
140:         address _newTrustedForwarder
141:     ) external override auth(SET_TRUSTED_FORWARDER_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L139-L141
```
/src/core/dao/DAO.sol

161: function setMetadata(
162:         bytes calldata _metadata
163:    ) external override auth(SET_METADATA_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L161-L163
```
/src/core/dao/DAO.sol

168: function execute(
169:         bytes32 _callId,
170:         Action[] calldata _actions,
171:         uint256 _allowFailureMap
172:     )
173:         external
174:         override
175:         auth(EXECUTE_PERMISSION_ID)
176:         returns (bytes[] memory execResults, uint256 failureMap)
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L168-L176
```
/src/core/dao/DAO.sol

239: function setSignatureValidator(
240:         address _signatureValidator
241:     ) external override auth(SET_SIGNATURE_VALIDATOR_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L239-L241
```
/src/core/dao/DAO.sol

309: function registerStandardCallback(
310:        bytes4 _interfaceId,
311:        bytes4 _callbackSelector,
312:        bytes4 _magicNumber
313:     ) external override auth(REGISTER_STANDARD_CALLBACK_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L309-L313
```
/src/core/dao/DAO.sol

326: function setDaoURI(string calldata newDaoURI) external auth(SET_METADATA_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L326
```
/src/core/permission/PermissionManager.sol

105: function grant(
106:         address _where,
107:         address _who,
108:         bytes32 _permissionId
109:     ) external virtual auth(ROOT_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L105-L109
```
/src/core/permission/PermissionManager.sol

120: function grantWithCondition(
121:         address _where,
122:         address _who,
123:         bytes32 _permissionId,
124:         IPermissionCondition _condition
125:     ) external virtual auth(ROOT_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L120-L125
```
/src/core/permission/PermissionManager.sol

167: function applyMultiTargetPermissions(
168:         PermissionLib.MultiTargetPermission[] calldata _items
169:     ) external virtual auth(ROOT_PERMISSION_ID) {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L167-L169

### [G-17] Unused import
```
/src/core/dao/DAO.sol

11: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L11

### [G-18] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.
See [this](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92) for more details
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol
supportsInterface(), implementation()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol
prepareUpdate()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol
isGranted(), grant(), grantWithCondition(), revoke(), applySingleTargetPermissions(), applyMultiTargetPermissions()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol
setTrustedForwarder(), hasPermission(), setMetadata(), execute(), deposit(), setSignatureValidator(), isValidSignature()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol
createDao()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol
register()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol
createVersion(), updateReleaseMetadata(), getLatestVersion(), getVersion(), buildCount()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol
createPluginRepo(), createPluginRepoWithFirstVersion()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol
registerPluginRepo()

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol
validatePreparedSetupId(), prepareInstallation(), applyInstallation(), prepareUpdate(), applyUpdate(), prepareUninstallation(), applyUninstallation()