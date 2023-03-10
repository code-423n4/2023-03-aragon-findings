# Gas Report 

[Exclusive findings](#exclusive-findings) is below the [automated findings](#automated-findings).

---

## Automated Findings


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Use assembly to check for `address(0)` | 22 |
| [GAS-2](#GAS-2) | `array[index] += amount` is cheaper than `array[index] = array[index] + amount` (or related variants) | 1 |
| [GAS-3](#GAS-3) | Using bools for storage incurs overhead | 2 |
| [GAS-4](#GAS-4) | Cache array length outside of loop | 10 |
| [GAS-5](#GAS-5) | State variables should be cached in stack variables rather than re-reading them from storage | 1 |
| [GAS-6](#GAS-6) | Use calldata instead of memory for function arguments that do not get mutated | 20 |
| [GAS-7](#GAS-7) | Don't initialize variables with default value | 1 |
| [GAS-8](#GAS-8) | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 |
| [GAS-9](#GAS-9) | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 |
| [GAS-10](#GAS-10) | Using `private` rather than `public` for constants, saves gas | 29 |
| [GAS-11](#GAS-11) | Use shift Right/Left instead of division/multiplication if possible | 2 |
| [GAS-12](#GAS-12) | Use != 0 instead of > 0 for unsigned integer comparison | 9 |
### <a name="GAS-1"></a>[GAS-1] Use assembly to check for `address(0)`
*Saves 6 gas per instance*

*Instances (22)*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

225:         if (_token == address(0)) {

252:         if (address(signatureValidator) == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

321:         if (pluginState.currentAppliedSetupId != bytes32(0)) {

366:         if (pluginState.currentAppliedSetupId != bytes32(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

86:         if (token != address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol)

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

65:         if (nodeResolver == address(0)) {

89:         if (currentOwner != address(0)) {

103:         if (_resolver == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

43:         if (_multiplyHelper == address(0)) {

56:                 _multiplyHelper == address(0) ? 3 : 2

80:         if (_multiplyHelper == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol)

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

44:         if (_multiplyHelper == address(0)) {

57:                 _multiplyHelper == address(0) ? 3 : 2

81:         if (_multiplyHelper == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol)

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

39:         if (admin == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

98:         if (token != address(0)) {

151:                 tokenSettings.addr != address(0) ? 3 : 4

181:         if (tokenSettings.addr == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

116:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

116:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

106:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

106:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)

### <a name="GAS-2"></a>[GAS-2] `array[index] += amount` is cheaper than `array[index] = array[index] + amount` (or related variants)
When updating a value in an array with arithmetic, using `array[index] += amount` is cheaper than `array[index] = array[index] + amount`.
This is because you avoid an additonal `mload` when the array is stored in memory, and an `sload` when the array is stored in storage.
This can be applied for any arithmetic operation including `+=`, `-=`,`/=`,`*=`,`^=`,`&=`, `%=`, `<<=`,`>>=`, and `>>>=`.
This optimization can be particularly significant if the pattern occurs during a loop.

*Saves 28 gas for a storage array, 38 for a memory array*

*Instances (1)*:
```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

123:         claimedBitMap[claimedWord_index] =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)

### <a name="GAS-3"></a>[GAS-3] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (2)*:
```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

25:     mapping(address => bool) public entries;

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol)

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

38:         mapping(address => bool) approvers;

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)

### <a name="GAS-4"></a>[GAS-4] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (10)*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

184:         for (uint256 i = 0; i < _actions.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

150:         for (uint256 i; i < items.length; ) {

170:         for (uint256 i; i < _items.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

95:         for (uint256 i; i < _pluginSettings.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

130:         for (uint256 i; i < _actions.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

124:         for (uint256 i; i < _actions.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

252:         for (uint256 i; i < _actions.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

60:         for (uint256 i; i < _newAddresses.length; ) {

78:         for (uint256 i; i < _exitingAddresses.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

81:         for (uint256 i; i < _mintSettings.receivers.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)

### <a name="GAS-5"></a>[GAS-5] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*Saves 100 gas per instance*

*Instances (1)*:
```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

128:         emit TokenCreated(IERC20Upgradeable(token), MerkleMinter(merkleMinter), distributorBase);

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol)

### <a name="GAS-6"></a>[GAS-6] Use calldata instead of memory for function arguments that do not get mutated
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

*Instances (20)*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

155:         bytes memory _data

250:         bytes memory _signature

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/core/dao/IDAO.sol

29:         bytes memory _data

48:         Action[] memory _actions,

125:     function isValidSignature(bytes32 _hash, bytes memory _signature) external returns (bytes4);

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol)

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

202:         bytes memory _data

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

49:         bytes memory _releaseMetadata,

50:         bytes memory _buildMetadata

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol)

```solidity
File: packages/contracts/src/plugins/token/IMerkleDistributor.sol

45:         bytes32[] memory _proof

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol)

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

87:         bytes32[] memory _proof

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

51:         string memory _name,

52:         string memory _symbol,

53:         MintSettings memory _mintSettings

65:         string memory _name,

66:         string memory _symbol,

67:         MintSettings memory _mintSettings

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

38:     constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

38:     constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

48:         string memory _name,

49:         string memory _symbol

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)

### <a name="GAS-7"></a>[GAS-7] Don't initialize variables with default value

*Instances (1)*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

184:         for (uint256 i = 0; i < _actions.length; ) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)

### <a name="GAS-8"></a>[GAS-8] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (4)*:
```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

95:     function __PermissionManager_init(address _initialOwner) internal onlyInitializing {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

22:     function __PluginCloneable_init(IDAO _dao) internal virtual onlyInitializing {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol)

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

39:     function __PluginUUPSUpgradeable_init(IDAO _dao) internal virtual onlyInitializing {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

20:     function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol)

### <a name="GAS-9"></a>[GAS-9] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
*Saves 5 gas per loop*

*Instances (1)*:
```solidity
File: packages/contracts/src/framework/utils/RegistryUtils.sol

16:     for (uint256 i; i < nameLength; i++) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol)

### <a name="GAS-10"></a>[GAS-10] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (29)*:
```solidity
File: packages/contracts/src/core/dao/DAO.sol

40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");

43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");

46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");

49:     bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =

53:     bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =

57:     bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)

```solidity
File: packages/contracts/src/core/permission/PermissionLib.sol

10:     address public constant NO_CONDITION = address(0);

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol)

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

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
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

16:     bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

27:     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =

31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

34:     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

18:     bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol)

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

18:     bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =

22:     bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)

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
File: packages/contracts/src/plugins/governance/admin/Admin.sol

23:     bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

183:     bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

27:     bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

71:     bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)

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

### <a name="GAS-11"></a>[GAS-11] Use shift Right/Left instead of division/multiplication if possible

*Instances (2)*:
```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

111:         uint256 claimedWord_index = _index / 256;

121:         uint256 claimedWord_index = _index / 256;

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)

### <a name="GAS-12"></a>[GAS-12] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (9)*:
```solidity
File: packages/contracts/src/core/utils/BitMap.sol

10:     return bitValue > 0;

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol)

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

59:         if ((bytes(subdomain).length > 0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

181:         if (_releaseMetadata.length > 0) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

55:         if (!(bytes(subdomain).length > 0)) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

378:         if (_params.permissions.length > 0) {

535:         if (_params.permissions.length > 0) {

635:         if (_params.permissions.length > 0) {

669:         if (_initData.length > 0) {

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

139:         return votingToken.getVotes(_account) > 0;

```
[Link to code](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)


---
## Exclusive Findings
---
### Gas Optimizations Report
| No. | Issue |
| --- | --- |
| 1 | [Using `uint256` instead of `bool` in mappings is more gas efficient [146 gas per instance]](#G-01-using-uint256-instead-of-bool-in-mappings-is-more-gas-efficient-146-gas-per-instance)|
| 2 | [Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#G-02-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead)|
| 3 | [++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#G-03-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops)|
| 4 | [Optimize names to save gas [22 gas per instance]](#G-04-optimize-names-to-save-gas-22-gas-per-instance)|
| 5 | [Setting the `constructor` to `payable` [~13 gas per instance]](#G-05-setting-the-constructor-to-payable-13-gas-per-instance)|
| 6 | [Comparison operators](#G-06-comparison-operators)|
| 7 | [`<array>.length` should not be looked up in every loop of a for-loop](#G-07-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop)|
---
### [G-01] Using `uint256` instead of `bool` in mappings is more gas efficient [146 gas per instance]

---
**Description:**

OpenZeppelin uint256 and bool comparison: 

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. The values being non-zero value makes deployment a bit more expensive.

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

**Recommendation:**

Use uint256(1) and uint256(2) instead of bool.

**Lines of Code:** 

- [InterfaceBasedRegistry.sol#L25](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L25)
- [Multisig.sol#L38](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L38)

---
### [G-02] Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

---
**Description:**

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so.

**Recommendation:**

Use a larger size then downcast where needed

**Lines of Code:** 

- [IProposal.sol#L22](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L22)
- [IProposal.sol#L23](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L23)
- [Proposal.sol#L48](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L48)
- [Proposal.sol#L49](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L49)
- [ProposalUpgradeable.sol#L48](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L48)
- [ProposalUpgradeable.sol#L49](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L49)
- [BitMap.sol#L8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol#L8)
- [BitMap.sol#L16](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol#L16)
- [IPluginRepo.sol#L12](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L12)
- [IPluginRepo.sol#L20](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L20)
- [PluginRepo.sol#L34](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L34)
- [PluginRepo.sol#L35](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L35)
- [PluginRepo.sol#L55](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55)
- [PluginRepo.sol#L65](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L65)
- [PluginRepo.sol#L80](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L80)
- [PluginRepo.sol#L80](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L80)
- [PluginRepo.sol#L86](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L86)
- [PluginRepo.sol#L86](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L86)
- [PluginRepo.sol#L100](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L100)
- [PluginRepo.sol#L101](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L101)
- [PluginRepo.sol#L109](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L109)
- [PluginRepo.sol#L129](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L129)
- [PluginRepo.sol#L165](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L165)
- [PluginRepo.sol#L188](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L188)
- [PluginRepo.sol#L209](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L209)
- [PluginRepo.sol#L210](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L210)
- [PluginRepo.sol#L244](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L244)
- [IPluginSetup.sol#L48](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L48)
- [PluginSetup.sol#L20](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L20)
- [RegistryUtils.sol#L17](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L17)
- [CounterV2PluginSetup.sol#L103](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L103)
- [Admin.sol#L67](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L67)
- [MajorityVotingBase.sol#L124](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L124)
- [MajorityVotingBase.sol#L125](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L125)
- [MajorityVotingBase.sol#L126](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L126)
- [MajorityVotingBase.sol#L155](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L155)
- [MajorityVotingBase.sol#L156](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L156)
- [MajorityVotingBase.sol#L157](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L157)
- [MajorityVotingBase.sol#L158](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L158)
- [MajorityVotingBase.sol#L195](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L195)
- [MajorityVotingBase.sol#L195](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L195)
- [MajorityVotingBase.sol#L200](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L200)
- [MajorityVotingBase.sol#L200](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L200)
- [MajorityVotingBase.sol#L228](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L228)
- [MajorityVotingBase.sol#L229](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L229)
- [MajorityVotingBase.sol#L230](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L230)
- [MajorityVotingBase.sol#L438](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L438)
- [MajorityVotingBase.sol#L439](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L439)
- [MajorityVotingBase.sol#L516](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L516)
- [MajorityVotingBase.sol#L565](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L565)
- [MajorityVotingBase.sol#L566](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L566)
- [MajorityVotingBase.sol#L567](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L567)
- [MajorityVotingBase.sol#L567](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L567)
- [MajorityVotingBase.sol#L568](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L568)
- [MajorityVotingBase.sol#L580](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580)
- [AddresslistVoting.sol#L87](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L87)
- [AddresslistVoting.sol#L88](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L88)
- [AddresslistVoting.sol#L92](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L92)
- [TokenVoting.sol#L75](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L75)
- [TokenVoting.sol#L76](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L76)
- [Multisig.sol#L36](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L36)
- [Multisig.sol#L49](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L49)
- [Multisig.sol#L50](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L50)
- [Multisig.sol#L51](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L51)
- [Multisig.sol#L52](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L52)
- [Multisig.sol#L60](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L60)
- [Multisig.sol#L99](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L99)
- [Multisig.sol#L99](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L99)
- [Multisig.sol#L104](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L104)
- [Multisig.sol#L109](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L109)
- [Multisig.sol#L109](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L109)
- [Multisig.sol#L119](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L119)
- [Multisig.sol#L175](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L175)
- [Multisig.sol#L211](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L211)
- [Multisig.sol#L212](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L212)
- [Multisig.sol#L214](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L214)
- [Multisig.sol#L312](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L312)
- [Multisig.sol#L404](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L404)
- [Multisig.sol#L414](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L414)
- [MajorityVotingMock.sol#L16](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/MajorityVotingMock.sol#L16)
- [MajorityVotingMock.sol#L17](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/MajorityVotingMock.sol#L17)
- [PluginMockData.sol#L11](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol#L11)
- [PluginMockData.sol#L12](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol#L12)
- [PluginMockData.sol#L18](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol#L18)
- [PluginMockData.sol#L29](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol#L29)
- [PluginMockData.sol#L32](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol#L32)
- [PluginUUPSUpgradeableSetupMock.sol#L73](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol#L73)
- [PluginUUPSUpgradeableSetupMock.sol#L111](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol#L111)
- [PluginUUPSUpgradeableSetupMock.sol#L163](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol#L163)

---
### [G-03] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

---
**Description:**

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

**Recommendation:**

Using Solidity's unchecked block saves the overflow checks.

**Proof Of Concept:**

<https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g011---unnecessary-checked-arithmetic-in-for-loop>

**Lines of Code:** 

- [DAOFactory.sol#L95](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95)
- [RegistryUtils.sol#L16](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16)
- [PluginMockData.sol#L18](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol#L18)
- [PluginMockData.sol#L32](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol#L32)

---
### [G-04] Optimize names to save gas [22 gas per instance]

---
**Description:**

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

**Recommendation:**

Find a lower `method ID` name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas. For example, the function IDs in the L1GraphTokenGateway.sol contract will be the most used; A lower method ID may be given.

**Proof Of Concept:**

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

**Lines of Code:** 

- [DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)
- [IDAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol)
- [IEIP4824.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IEIP4824.sol)
- [IPermissionCondition.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/IPermissionCondition.sol)
- [PermissionLib.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol)
- [PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)
- [IPlugin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/IPlugin.sol)
- [Plugin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol)
- [PluginCloneable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol)
- [PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)
- [DaoAuthorizable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol)
- [DaoAuthorizableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol)
- [IMembership.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/membership/IMembership.sol)
- [IProposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol)
- [Proposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol)
- [ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol)
- [BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol)
- [CallbackHandler.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol)
- [auth.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol)
- [DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol)
- [DAORegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol)
- [IPluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol)
- [PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)
- [PluginRepoFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol)
- [PluginRepoRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)
- [IPluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol)
- [PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol)
- [PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)
- [PluginSetupProcessorHelpers.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol)
- [InterfaceBasedRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol)
- [RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol)
- [TokenFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol)
- [ENSMigration.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol)
- [ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)
- [MultiplyHelper.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol)
- [CounterV1.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol)
- [CounterV1PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol)
- [CounterV2.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol)
- [CounterV2PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol)
- [Admin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol)
- [AdminSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol)
- [IMajorityVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol)
- [MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)
- [AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)
- [AddresslistVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol)
- [TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)
- [TokenVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol)
- [IMultisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol)
- [Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)
- [MultisigSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol)
- [IMerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol)
- [IMerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleMinter.sol)
- [MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)
- [MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol)
- [Addresslist.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol)
- [Ratio.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol)
- [ActionExecute.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/dao/ActionExecute.sol)
- [CallbackHandlerHelperMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/dao/CallbackHandlerHelperMock.sol)
- [DAOMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/dao/DAOMock.sol)
- [ERC1271Mock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/dao/ERC1271Mock.sol)
- [ParameterScopingPermissionConditionTest.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/permission/ParameterScopingPermissionConditionTest.sol)
- [PermissionConditionMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/permission/PermissionConditionMock.sol)
- [PermissionManagerTest.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/permission/PermissionManagerTest.sol)
- [AddresslistMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/AddresslistMock.sol)
- [AdminCloneFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/AdminCloneFactory.sol)
- [PluginCloneableMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/Clonable/PluginCloneableMock.sol)
- [PluginCloneableSetupMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/Clonable/PluginCloneableSetupMock.sol)
- [MajorityVotingMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/MajorityVotingMock.sol)
- [PluginMockData.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginMockData.sol)
- [PluginTest.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/PluginTest.sol)
- [SharedPluginTest.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/SharedPluginTest.sol)
- [PluginUUPSUpgradeableMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableMock.sol)
- [PluginUUPSUpgradeableSetupMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol)
- [GovernanceERC20Mock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/GovernanceERC20Mock.sol)
- [TestERC1155.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/TestERC1155.sol)
- [TestERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/TestERC20.sol)
- [TestERC721.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/TestERC721.sol)
- [InterfaceBasedRegistryMock.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/utils/InterfaceBasedRegistryMock.sol)
- [RatioTest.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/utils/RatioTest.sol)
- [RegistryUtilsTest.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/utils/RegistryUtilsTest.sol)
- [IERC20MintableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/IERC20MintableUpgradeable.sol)
- [GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)
- [GovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)
- [IGovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol)
- [Proxy.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol)
- [UncheckedMath.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/UncheckedMath.sol)

---
### [G-05] Setting the `constructor` to `payable` [~13 gas per instance]

---
**Lines of Code:** 

- [DAO.sol#L92](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L92)
- [Plugin.sol#L17](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L17)
- [PluginCloneable.sol#L16](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L16)
- [PluginUUPSUpgradeable.sol#L25](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25)
- [DaoAuthorizable.sol#L19](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19)
- [DAOFactory.sol#L53](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L53)
- [DAORegistry.sol#L30](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L30)
- [PluginRepo.sol#L112](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L112)
- [PluginRepoFactory.sol#L22](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22)
- [PluginRepoRegistry.sol#L34](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L34)
- [PluginSetupProcessor.sol#L277](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277)
- [TokenFactory.sol#L68](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L68)
- [ENSSubdomainRegistrar.sol#L45](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L45)
- [CounterV1PluginSetup.sol#L23](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L23)
- [CounterV2PluginSetup.sol#L24](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L24)
- [AdminSetup.sol#L27](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L27)
- [AddresslistVotingSetup.sol#L20](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L20)
- [TokenVotingSetup.sol#L61](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61)
- [MultisigSetup.sol#L19](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L19)
- [DAOMock.sol#L14](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/dao/DAOMock.sol#L14)
- [AdminCloneFactory.sol#L14](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/AdminCloneFactory.sol#L14)
- [PluginCloneableSetupMock.sol#L15](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/Clonable/PluginCloneableSetupMock.sol#L15)
- [PluginCloneableSetupMock.sol#L45](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/Clonable/PluginCloneableSetupMock.sol#L45)
- [PluginCloneableSetupMock.sol#L51](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/Clonable/PluginCloneableSetupMock.sol#L51)
- [SharedPluginTest.sol#L65](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/SharedPluginTest.sol#L65)
- [PluginUUPSUpgradeableSetupMock.sol#L15](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol#L15)
- [PluginUUPSUpgradeableSetupMock.sol#L57](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol#L57)
- [PluginUUPSUpgradeableSetupMock.sol#L95](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol#L95)
- [PluginUUPSUpgradeableSetupMock.sol#L147](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol#L147)
- [GovernanceERC20Mock.sol#L9](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/GovernanceERC20Mock.sol#L9)
- [TestERC1155.sol#L8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/TestERC1155.sol#L8)
- [TestERC20.sol#L8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/TestERC20.sol#L8)
- [TestERC721.sol#L8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/test/token/TestERC721.sol#L8)
- [GovernanceERC20.sol#L49](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L49)
- [GovernanceWrappedERC20.sol#L38](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L38)

---
### [G-06] Comparison operators

---
**Description:**

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas.

**Recommendation:**

 Replace `<=` with `<`, and `>=` with `>`. Do not forget to increment/decrement the compared variable.

**Lines of Code:** 

- [PluginSetupProcessor.sol#L405](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L405)
- [PluginSetupProcessor.sol#L655](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L655)
- [MajorityVotingBase.sol#L347-L348](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L347-L348)
- [MajorityVotingBase.sol#L519](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L519)
- [Multisig.sol#L397](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L397)
- [Multisig.sol#L407](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L407)
- [Multisig.sol#L408](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L408)

---
### [G-07] `<array>.length` should not be looked up in every loop of a for-loop

---
**Description:**

The overheads outlined below are *PER LOOP*, excluding the first loop 

- storage arrays incur a Gwarmaccess (100 gas)

- memory arrays use `MLOAD` (3 gas)

- calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset



**Lines of Code:** 

- [DAO.sol#L184](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L184)
- [PermissionManager.sol#L150](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L150)
- [PermissionManager.sol#L170](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L170)
- [DAOFactory.sol#L95](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95)
- [AddresslistVoting.sol#L130](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L130)
- [TokenVoting.sol#L124](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L124)
- [Multisig.sol#L252](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L252)
- [Addresslist.sol#L60](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L60)
- [Addresslist.sol#L78](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L78)
- [GovernanceERC20.sol#L81](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L81)
