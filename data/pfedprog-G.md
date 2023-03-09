## Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::184 => for (uint256 i = 0; i < actions.length; ) {

## Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::178 => if (actions.length > MAX_ACTIONS) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::182 => execResults = new bytes[](_actions.length);
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::184 => for (uint256 i = 0; i < actions.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol::150 => for (uint256 i; i < items.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol::170 => for (uint256 i; i < items.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol::68 => if (pluginSettings.length == 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol::95 => for (uint256 i; i < pluginSettings.length; ++i) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/dao/DAORegistry.sol::59 => if ((bytes(subdomain).length > 0)) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol::150 => if (releaseMetadata.length == 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol::181 => if (releaseMetadata.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol::199 => if (releaseMetadata.length == 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol::55 => if (!(bytes(subdomain).length > 0)) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::378 => if (params.permissions.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::535 => if (params.permissions.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::635 => if (params.permissions.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::669 => if (initData.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol::15 => uint256 nameLength = nameBytes.length;
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/utils/TokenFactory.sol::92 => if (data.length != 0x20) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol::105 => payload.currentHelpers.length != 0 ? 3 : 2
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol::125 => if (payload.currentHelpers.length != 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol::145 => payload.currentHelpers.length != 0 ? 3 : 2
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol::165 => if (payload.currentHelpers.length != 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol::130 => for (uint256 i; i < actions.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol::124 => for (uint256 i; i < actions.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol::58 => error WrongHelpersArrayLength(uint256 length);
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol::203 => uint256 helperLength = payload.currentHelpers.length;
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol::205 => revert WrongHelpersArrayLength({length: helperLength});
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol::280 => return success && data.length == 0x20;
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol::156 => uint256 newAddresslistLength = addresslistLength() + members.length;
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol::175 => uint16 newAddresslistLength = uint16(addresslistLength() - members.length);
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol::252 => for (uint256 i; i < actions.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/utils/Addresslist.sol::60 => for (uint256 i; i < newAddresses.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/utils/Addresslist.sol::72 => addresslistLengthCheckpoints.push(uncheckedAdd, newAddresses.length);
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/utils/Addresslist.sol::78 => for (uint256 i; i < exitingAddresses.length; ) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/utils/Addresslist.sol::90 => addresslistLengthCheckpoints.push(uncheckedSub, exitingAddresses.length);
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol::70 => if (mintSettings.receivers.length != mintSettings.amounts.length) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol::72 => receiversArrayLength: mintSettings.receivers.length,
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol::73 => amountsArrayLength: mintSettings.amounts.length
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol::81 => for (uint256 i; i < mintSettings.receivers.length; ) {

## Use != 0 instead of > 0 for Unsigned Integer Comparison

When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/utils/BitMap.sol::10 => return bitValue > 0;
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/dao/DAORegistry.sol::59 => if ((bytes(subdomain).length > 0)) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol::181 => if (releaseMetadata.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol::55 => if (!(bytes(subdomain).length > 0)) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::378 => if (params.permissions.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::535 => if (params.permissions.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::635 => if (params.permissions.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::669 => if (initData.length > 0) {
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol::139 => return votingToken.getVotes(account) > 0;

## Use immutable for OpenZeppelin AccessControl's Roles Declarations

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::40 => bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::43 => bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::46 => bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::50 => keccak256("SET_TRUSTED_FORWARDER_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::54 => keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/dao/DAO.sol::58 => keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol::15 => bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol::347 => return keccak256(abi.encodePacked("PERMISSION", who, where, permissionId));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol::35 => bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/dao/DAORegistry.sol::15 => bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/dao/DAORegistry.sol::64 => bytes32 labelhash = keccak256(bytes(subdomain));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol::49 => bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol::52 => bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol::252 => return keccak256(abi.encodePacked(tag.release, tag.build));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol::17 => keccak256("REGISTER_PLUGIN_REPO_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol::63 => bytes32 labelhash = keccak256(bytes(subdomain));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::28 => keccak256("APPLY_INSTALLATION_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::31 => bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::35 => keccak256("APPLY_UNINSTALLATION_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::38 => /// @dev The hash is computed via `keccak256(abi.encode([]))`.
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol::43 => /// @dev The hash is computed via `keccak256(abi.encode(0))`.
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol::33 => return keccak256(abi.encode(dao, plugin));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol::51 => keccak256(
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol::57 => keccak256(data),
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol::72 => keccak256(
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol::80 => return keccak256(abi.encode(helpers));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol::89 => return keccak256(abi.encode(permissions));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol::19 => keccak256("UPGRADE_REGISTRY_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol::19 => keccak256("UPGRADE_REGISTRAR_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol::23 => keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol::86 => bytes32 subnode = keccak256(abi.encodePacked(node, label));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol::12 => bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol::14 => bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol::48 => bytes4(keccak256("initialize(address,address,uint256)")),
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol::69 => keccak256("EXECUTE_PERMISSION")
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol::114 => keccak256("EXECUTE_PERMISSION")
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol::14 => bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol::49 => bytes4(keccak256("initialize(address,address,uint256)")),
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol::70 => keccak256("EXECUTE_PERMISSION")
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol::116 => bytes4(keccak256("setNewVariable(uint256)")),
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol::154 => keccak256("EXECUTE_PERMISSION")
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/admin/Admin.sol::24 => keccak256("EXECUTE_PROPOSAL_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol::184 => keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol::28 => keccak256("UPDATE_ADDRESSES_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol::72 => keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/token/MerkleDistributor.sol::105 => bytes32 node = keccak256(abi.encodePacked(index, to, amount));
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/token/MerkleMinter.sol::24 => bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/token/MerkleMinter.sol::28 => keccak256("CHANGE_DISTRIBUTOR_PERMISSION");
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol::28 => bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

## Use Shift Right/Left instead of Division/Multiplication

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/token/MerkleDistributor.sol::111 => uint256 claimedWord_index = index / 256;
https://github.com/code-423n4/2023-03-aragon/packages/contracts/src/plugins/token/MerkleDistributor.sol::121 => uint256 claimedWord_index = index / 256;
