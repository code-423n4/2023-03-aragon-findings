## Summary

|        | Issues                                                                                                     | Contexts |
| ------ | ---------------------------------------------------------------------------------------------------------- | -------- |
| [G-01] | Setting the constructor to `payable`                                                                       | 26       |
| [G-02] | Empty blocks should be removed or emit something                                                           | 6        |
| [G-03] | Remove the `initializer` modifier                                                                          | 13       |
| [G-04] | Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant | 27       |
| [G-05] | Use nested if and, avoid multiple check combinations                                                       | 14       |

## [G-01] Setting the constructor to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor `payable` eliminates the need for an initial check of `msg.value == 0` and saves 13 gas on deployment with no security risks.

**Context**

26 results - 21 files

    packages/contracts/src/core/dao/DAO.sol:
    92:     constructor() {

    packages/contracts/src/core/plugin/Plugin.sol:
    17:     constructor(IDAO _dao) DaoAuthorizable(_dao) {}

    packages/contracts/src/core/plugin/PluginCloneable.sol:
    16:     constructor() {

    packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol:
    25:     constructor() {

    packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol:
    19:     constructor(IDAO _dao) {

    packages/contracts/src/framework/dao/DAOFactory.sol:
    50:     /// @notice The constructor setting the registry and plugin setup processor and creating the base contracts for the factory.
    53:     constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {

    packages/contracts/src/framework/dao/DAORegistry.sol:
    30:     constructor() {

    packages/contracts/src/framework/plugin/repo/PluginRepo.sol:
    112:     constructor() {

    packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol:
    22:     constructor(PluginRepoRegistry _pluginRepoRegistry) {

    packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol:
    34:     constructor() {

    packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol:
    277:     constructor(PluginRepoRegistry _repoRegistry) {

    packages/contracts/src/framework/utils/TokenFactory.sol:
    68:     constructor() {

    packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol:
    45:     constructor() {

    packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol:
    23:     constructor() {

    packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol:
    24:     constructor(MultiplyHelper _helper) {

    packages/contracts/src/plugins/governance/admin/AdminSetup.sol:
    26:     /// @notice The constructor setting the `Admin` implementation contract to clone from.
    27:     constructor() {

    packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol:
    19:     /// @notice The contract constructor, that deployes the `AddresslistVoting` plugin logic contract.
    20:     constructor() {

    packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol:
    60:     /// @notice The contract constructor, that deployes the bases.
    61:     constructor() {

    packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol:
    18:     /// @notice The contract constructor, that deployes the `Multisig` plugin logic contract.
    19:     constructor() {

    packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol:
    49:     constructor(

    packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol:
    38:     constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

## [G-02] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation.

**Context**

    6 results - 6 files

    packages/contracts/src/core/dao/DAO.sol:
    136:     function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}

    packages/contracts/src/core/plugin/Plugin.sol:
    17:     constructor(IDAO _dao) DaoAuthorizable(_dao) {}

    packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol:
    63:     ) internal virtual override auth(UPGRADE_PLUGIN_PERMISSION_ID) {}

    packages/contracts/src/framework/plugin/repo/PluginRepo.sol:
    259:     ) internal virtual override auth(UPGRADE_REPO_PERMISSION_ID) {}

    packages/contracts/src/framework/plugin/setup/PluginSetup.sol:
    27:     {}

    packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol:
    56:     ) internal virtual override auth(UPGRADE_REGISTRY_PERMISSION_ID) {}

## [G-03] Remove the `initializer` modifier

If we can just ensure that the initialize() function could only be called from within the constructor, we shouldn’t need to worry about it getting called again. In the EVM, the constructor’s job is actually to return the bytecode that will live at the contract’s address. So, while inside a constructor, your address (address(this)) will be the deployment address, but there will be no bytecode at that address! So if we check address(this).code.length before the constructor has finished, even from within a delegatecall, we will get 0. So now let’s update our initialize() functions to only run if we are inside a constructor:

```diff
+ require(address(this).code.length == 0, 'not in constructor');
```

Now the Proxy contracts’ constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero.

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version.

**Context**

    13 results - 13 files

    packages/contracts/src/core/dao/DAO.sol:
    109:     ) external initializer {

    packages/contracts/src/framework/dao/DAORegistry.sol:
    40:     ) external initializer {

    packages/contracts/src/framework/plugin/repo/PluginRepo.sol:
    120:     function initialize(address initialOwner) external initializer {

    packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol:
    41:     function initialize(IDAO _dao, ENSSubdomainRegistrar _subdomainRegistrar) external initializer {

    packages/contracts/src/plugins/counter-example/v1/CounterV1.sol:
    30:     ) external initializer {

    packages/contracts/src/plugins/governance/admin/Admin.sol:
    29:     function initialize(IDAO _dao) external initializer {

    packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol:
    38:     ) external initializer {

    packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol:
    40:     ) external initializer {

    packages/contracts/src/plugins/governance/multisig/Multisig.sol:
    129:     ) external initializer {

    packages/contracts/src/plugins/token/MerkleDistributor.sol:
    50:     ) external initializer {

    packages/contracts/src/plugins/token/MerkleMinter.sol:
    45:     ) external initializer {

    packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol:
    68:     ) public initializer {

    packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol:
    50:     ) public initializer {

## [G-04] Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant

    27 results - 18 files

    packages/contracts/src/core/dao/DAO.sol:
    40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
    43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
    46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");
    50:         keccak256("SET_TRUSTED_FORWARDER_PERMISSION");
    54:         keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");
    58:         keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");

    packages/contracts/src/core/permission/PermissionManager.sol:
    15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

    packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol:
    35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

    packages/contracts/src/framework/dao/DAORegistry.sol:
    15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

    packages/contracts/src/framework/plugin/repo/PluginRepo.sol:
    49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
    52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

    packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol:
    17:         keccak256("REGISTER_PLUGIN_REPO_PERMISSION");

    packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol:
    28:         keccak256("APPLY_INSTALLATION_PERMISSION");
    35:         keccak256("APPLY_UNINSTALLATION_PERMISSION");

    packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol:
    19:         keccak256("UPGRADE_REGISTRY_PERMISSION");

    packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol:
    19:         keccak256("UPGRADE_REGISTRAR_PERMISSION");
    23:         keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");

    packages/contracts/src/plugins/counter-example/MultiplyHelper.sol:
    12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

    packages/contracts/src/plugins/counter-example/v1/CounterV1.sol:
    14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

    packages/contracts/src/plugins/counter-example/v2/CounterV2.sol:
    14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

    packages/contracts/src/plugins/governance/admin/Admin.sol:
    24:         keccak256("EXECUTE_PROPOSAL_PERMISSION");

    packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol:
    184:         keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");

    packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol:
    28:         keccak256("UPDATE_ADDRESSES_PERMISSION");

    packages/contracts/src/plugins/governance/multisig/Multisig.sol:
    72:         keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");

    packages/contracts/src/plugins/token/MerkleMinter.sol:
    24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");
    28:         keccak256("CHANGE_DISTRIBUTOR_PERMISSION");

    packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol:
    28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

## [G-05] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

14 results - 8 files

packages/contracts/src/core/permission/PermissionManager.sol:
236: if (\_where == ANY_ADDR && \_who == ANY_ADDR) {

packages/contracts/src/framework/plugin/repo/PluginRepo.sol:
157: if (version.tag.release != 0 && version.tag.release != \_release) {

packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol:
701: msg.sender != \_dao &&

packages/contracts/src/framework/utils/RegistryUtils.sol:
20: if (char > 96 && char < 123) {
25: if (char > 47 && char < 58) {

packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol:
519: proposal*.parameters.startDate <= currentTime &&
520: currentTime < proposal*.parameters.endDate &&

packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol:
97: if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
185: if (\_tryEarlyExecution && \_canExecute(\_proposalId)) {
215: proposal_.voters[_account] != VoteOption.None &&

packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol:
182: if (_tryEarlyExecution && \_canExecute(\_proposalId)) {
212: proposal_.voters[_account] != VoteOption.None &&

packages/contracts/src/plugins/governance/multisig/Multisig.sol:
216: if (multisigSettings.onlyListed && !isListedAtBlock(\_msgSender(), snapshotBlock)) {
283: if (\_tryExecution && \_canExecute(\_proposalId)) {
