# Summary

## Gas Optimizations

|        | Issue                                                                                                                                               |
| ------ | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| GAS-1  | `<X> += <Y>`/`<X> -= <Y>` COSTS MORE GAS THAN `<X> = <X> + <Y>`/`<X> = <X> - <Y>` FOR STATE VARIABLES                                               |
| GAS-2  | `ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`                                                                                          |
| GAS-3  | SETTING THE CONSTRUCTOR TO PAYABLE                                                                                                                  |
| GAS-4  | USING CREATE2 IS CHEAPER THAN CLONES                                                                                                                |
| GAS-5  | DON’T USE \_MSGSENDER() IF NOT SUPPORTING EIP-2771                                                                                                  |
| GAS-6  | DUPLICATED REQUIRE()/REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION (INSTANCES)                                                     |
| GAS-7  | DOS WITH BLOCK GAS LIMIT                                                                                                                            |
| GAS-8  | USE FUNCTION INSTEAD OF MODIFIERS                                                                                                                   |
| GAS-9  | INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT |
| GAS-10 | INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS                                                              |
| GAS-11 | INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS                                                                                      |
| GAS-12 | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT                                                            |
| GAS-13 | KECCAK256() SHOULD ONLY NEED TO BE CALLED ON A SPECIFIC STRING LITERAL ONCE                                                                         |
| GAS-14 | MAKING CONSTANT VARIABLES PRIVATE WILL SAVE GAS DURING DEPLOYMENT                                                                                   |
| GAS-15 | MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL                                                                                        |
| GAS-16 | MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE                                  |
| GAS-17 | OPTIMIZE NAMES TO SAVE GAS                                                                                                                          |
| GAS-18 | THE INCREMENT IN FOR LOOP POSTCONDITION CAN BE MADE UNCHECKED                                                                                       |
| GAS-19 | PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD                                                                     |
| GAS-20 | TERNARY OPERATION IS CHEAPER THAN IF-ELSE STATEMENT                                                                                                 |
| GAS-21 | USAGE OF `UINT`/`INT` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD                                                                              |
| GAS-22 | UNNECESSARY LIBRARIES                                                                                                                               |
| GAS-23 | USE NAMED RETURNS WHERE APPROPRIATE                                                                                                                 |

### [GAS-1] `<X> += <Y>`/`<X> -= <Y>` COSTS MORE GAS THAN `<X> = <X> + <Y>`/`<X> = <X> - <Y>` FOR STATE VARIABLES

#### Description:

Using the addition operator instead of plus-equals saves gas

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

276:             proposal_.approvals += 1;

```

### [GAS-2] `ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`

#### Description:

Use `abi.encodePacked()` where possible to save gas

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

293:         return abi.encode(magicNumber);

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

33:     return keccak256(abi.encode(_dao, _plugin));

73:             abi.encode(_pluginSetupRef.versionTag, _pluginSetupRef.pluginSetupRepo, _helpersHash)

80:     return keccak256(abi.encode(_helpers));

89:     return keccak256(abi.encode(_permissions));

```

### [GAS-3] SETTING THE CONSTRUCTOR TO PAYABLE

#### Description:

Saves ~13 gas per instance

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

95:     constructor() {

```

```solidity
File: packages/contracts/src/core/plugin/Plugin.sol

17:     constructor(IDAO _dao) DaoAuthorizable(_dao) {}

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

16:     constructor() {

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

25:     constructor() {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

19:     constructor(IDAO _dao) {

```

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

53:     constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

30:     constructor() {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

112:     constructor() {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

22:     constructor(PluginRepoRegistry _pluginRepoRegistry) {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

34:     constructor() {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

277:     constructor(PluginRepoRegistry _repoRegistry) {

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

68:     constructor() {

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

45:     constructor() {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

23:     constructor() {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

24:     constructor(MultiplyHelper _helper) {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

27:     constructor() {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

20:     constructor() {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

61:     constructor() {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

19:     constructor() {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

49:     constructor(

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

38:     constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

```

### [GAS-4] USING CREATE2 IS CHEAPER THAN CLONES

#### Description:

Using clone contracts requires extra proxy call, increasing the cost of all pair functions. Using CREATE2, although will increase cost of pair creation, will make all pair interactions cheaper.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

7: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

24:     using Clones for address;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

17:     using Clones for address;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

17:     using Clones for address;

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

17:     using Clones for address;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

26:     using Clones for address;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

21:     using Clones for address;

```

### [GAS-5] DON’T USE \_MSGSENDER() IF NOT SUPPORTING EIP-2771

#### Description:

Use msg.sender if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support.

[Code4arena example](https://code4rena.com/reports/2022-09-vtvl/#g15--dont-use-_msgsender-if-not-supporting-eip-2771)

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

32:         _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

33:         _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

70:             _creator: _msgSender(),

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

270:         address account = _msgSender();

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

97:         if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {

98:             revert ProposalCreationForbidden(_msgSender());

102:             _creator: _msgSender(),

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

91:         if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {

92:             revert ProposalCreationForbidden(_msgSender());

96:             _creator: _msgSender(),

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

216:         if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {

217:             revert ProposalCreationForbidden(_msgSender());

231:             _creator: _msgSender(),

266:         address approver = _msgSender();

```

### [GAS-6] DUPLICATED REQUIRE()/REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION (INSTANCES)

#### Description:

The compiler will inline the function, which will avoid JUMP instructions usually associated with functions

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

673:                 revert(reason);

685:                 revert(reason);

```

### [GAS-7] DOS WITH BLOCK GAS LIMIT

#### Description:

When smart contracts are deployed or functions inside them are called, the execution of these actions always requires a certain amount of gas, based of how much computation is needed to complete them. The Ethereum network specifies a block gas limit and the sum of all transactions included in a block can not exceed the threshold.

Programming patterns that are harmless in centralized applications can lead to Denial of Service conditions in smart contracts when the cost of executing a function exceeds the block gas limit. Modifying an array of unknown size, that increases in size over time, can lead to such a Denial of Service condition.

[Code4arena example](https://swcregistry.io/docs/SWC-128)

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

195:        for (uint256 i = 0; i < _actions.length; ) {

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

150:        for (uint256 i; i < items.length; ) {

170:        for (uint256 i; i < _items.length; ) {

```

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

95:        for (uint256 i; i < _pluginSettings.length; ++i) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

130:        for (uint256 i; i < _actions.length; ) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

124:        for (uint256 i; i < _actions.length; ) {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

252:        for (uint256 i; i < _actions.length; ) {

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

60:        for (uint256 i; i < _newAddresses.length; ) {

78:        for (uint256 i; i < _exitingAddresses.length; ) {

```

#### Recommended Mitigation Steps:

Caution is advised when you expect to have large arrays that grow over time. Actions that require looping across the entire data structure should be avoided.

If you absolutely must loop over an array of unknown size, then you should plan for it to potentially take multiple blocks, and therefore require multiple transactions.

### [GAS-8] USE FUNCTION INSTEAD OF MODIFIERS

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

87:     modifier auth(bytes32 _permissionId) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

31:     modifier auth(bytes32 _permissionId) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

32:     modifier auth(bytes32 _permissionId) {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

270:     modifier canApply(address _dao, bytes32 _permissionId) {

```

### [GAS-9] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol
40:         bytes32 public constant EXECUTE_PERMISSION_ID =
41:         keccak256("EXECUTE_PERMISSION");

44:         bytes32 public constant UPGRADE_DAO_PERMISSION_ID =
45:         keccak256("UPGRADE_DAO_PERMISSION");

48:         bytes32 public constant SET_METADATA_PERMISSION_ID =
49:         keccak256("SET_METADATA_PERMISSION");

52:         bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
53:         keccak256("SET_TRUSTED_FORWARDER_PERMISSION");

56:         bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
57:         keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");

60:         bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
61:         keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol
16:         bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
17:         keccak256("REGISTER_PLUGIN_REPO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol
27:         bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
28:         keccak256("APPLY_INSTALLATION_PERMISSION");

31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

34:         bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
35:         keccak256("APPLY_UNINSTALLATION_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

18:         bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRY_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

18:         bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRAR_PERMISSION");

22:         bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
23:         keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

23:         bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
24:         keccak256("EXECUTE_PROPOSAL_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

183:         bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
184:         keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

27:         bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
28:         keccak256("UPDATE_ADDRESSES_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

71:         bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
72:         keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

27:         bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
28:         keccak256("CHANGE_DISTRIBUTOR_PERMISSION");

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```

### [GAS-10] INTERNAL FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE REMOVED TO SAVE DEPLOYMENT GAS

#### Description:

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

95:     function __PermissionManager_init(address _initialOwner) internal onlyInitializing {

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

22:     function __PluginCloneable_init(IDAO _dao) internal virtual onlyInitializing {

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

39:     function __PluginUUPSUpgradeable_init(IDAO _dao) internal virtual onlyInitializing {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

20:     function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {

```

```solidity
File: packages/contracts/src/core/utils/CallbackHandler.sol

48:     function _registerCallback(bytes4 _callbackSelector, bytes4 _magicNumber) internal virtual {

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

61:     function _register(address _registrant) internal {

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

59:     function _addAddresses(address[] calldata _newAddresses) internal virtual {

77:     function _removeAddresses(address[] calldata _exitingAddresses) internal virtual {

```

### [GAS-11] INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

#### Description:

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

212:     function _initializePermissionManager(address _initialOwner) internal {

327:     function _auth(bytes32 _permissionId) internal view virtual {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

33:     function _createProposalId() internal returns (uint256 proposalId) {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

33:     function _createProposalId() internal returns (uint256 proposalId) {

```

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

141:     function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {

156:     function _setDAOPermissions(DAO _dao) internal {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

65:     function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

457:     function _execute(uint256 _proposalId) internal virtual {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

112:     function _afterTokenTransfer(address from, address to, uint256 amount) internal override {

```

### [GAS-12] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol
40:         bytes32 public constant EXECUTE_PERMISSION_ID =
41:         keccak256("EXECUTE_PERMISSION");

44:         bytes32 public constant UPGRADE_DAO_PERMISSION_ID =
45:         keccak256("UPGRADE_DAO_PERMISSION");

48:         bytes32 public constant SET_METADATA_PERMISSION_ID =
49:         keccak256("SET_METADATA_PERMISSION");

52:         bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
53:         keccak256("SET_TRUSTED_FORWARDER_PERMISSION");

56:         bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
57:         keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");

60:         bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
61:         keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol
16:         bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
17:         keccak256("REGISTER_PLUGIN_REPO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol
27:         bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
28:         keccak256("APPLY_INSTALLATION_PERMISSION");

31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

34:         bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
35:         keccak256("APPLY_UNINSTALLATION_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

18:         bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRY_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

18:         bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRAR_PERMISSION");

22:         bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
23:         keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

23:         bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
24:         keccak256("EXECUTE_PROPOSAL_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

183:         bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
184:         keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

27:         bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
28:         keccak256("UPDATE_ADDRESSES_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

71:         bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
72:         keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

27:         bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
28:         keccak256("CHANGE_DISTRIBUTOR_PERMISSION");

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```

### [GAS-13] KECCAK256() SHOULD ONLY NEED TO BE CALLED ON A SPECIFIC STRING LITERAL ONCE

#### Description:

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

347:         return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

252:         return keccak256(abi.encodePacked(_tag.release, _tag.build));

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

33:     return keccak256(abi.encode(_dao, _plugin));

80:     return keccak256(abi.encode(_helpers));

89:     return keccak256(abi.encode(_permissions));

```

### [GAS-14] MAKING CONSTANT VARIABLES PRIVATE WILL SAVE GAS DURING DEPLOYMENT

#### Description:

When constants are marked public, extra getter functions are created, increasing the deployment cost. Marking these functions private will decrease gas cost. One can still read these variables through the source code. If they need to be accessed by an external contract, a separate single getter function can be used to return all constants as a tuple.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

40:     bytes32 public constant EXECUTE_PERMISSION_ID =

44:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID =

48:     bytes32 public constant SET_METADATA_PERMISSION_ID =

52:     bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =

56:     bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =

60:     bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/core/permission/PermissionLib.sol

10:     address public constant NO_CONDITION = address(0);

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

16:     bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

27:     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =

31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

34:     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

18:     bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

18:     bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =

22:     bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

23:     bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

183:     bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

27:     bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

71:     bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

27:     bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```

### [GAS-15] MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

31:     modifier auth(bytes32 _permissionId) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

32:     modifier auth(bytes32 _permissionId) {

```

### [GAS-16] MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

#### Description:

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

If both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

55:     mapping(uint8 => uint16) internal buildsPerRelease;

58:     mapping(bytes32 => Version) internal versions;

61:     mapping(address => bytes32) internal latestTagHashForPluginSetup;

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

54:         mapping(bytes32 => uint256) preparedSetupIdToBlockNumber;

59:     mapping(bytes32 => PluginState) public states;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

141:         mapping(address => IMajorityVoting.VoteOption) voters;

187:     mapping(uint256 => Proposal) internal proposals;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

38:         mapping(address => bool) approvers;

75:     mapping(uint256 => Proposal) internal proposals;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

29:     mapping(uint256 => uint256) private claimedBitMap;

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

17:     mapping(address => CheckpointsUpgradeable.History) private _addresslistCheckpoints;

```

#### Recommended Mitigation Steps:

Make mapping a struct instead

### [GAS-17] OPTIMIZE NAMES TO SAVE GAS

#### Description:

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. In this report are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).

[Solidity optimize name github](https://github.com/enzosv/solidity-optimize-name)

[Source](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

26: contract DAO is

```

```solidity
File: packages/contracts/src/core/dao/IDAO.sol

8: interface IDAO {

```

```solidity
File: packages/contracts/src/core/dao/IEIP4824.sol

8: interface IEIP4824 {

```

```solidity
File: packages/contracts/src/core/permission/IPermissionCondition.sol

8: interface IPermissionCondition {

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

13: abstract contract PermissionManager is Initializable {

207:             _isGranted(ANY_ADDR, _who, _permissionId, _data); // check if `_who` has permission for `_permissionI` on any contract

```

```solidity
File: packages/contracts/src/core/plugin/IPlugin.sol

8: interface IPlugin {

```

```solidity
File: packages/contracts/src/core/plugin/Plugin.sol

14: abstract contract Plugin is IPlugin, ERC165, DaoAuthorizable {

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

14: abstract contract PluginCloneable is IPlugin, ERC165Upgradeable, DaoAuthorizableUpgradeable {

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

16: abstract contract PluginUUPSUpgradeable is

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

13: abstract contract DaoAuthorizable is Context {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

14: abstract contract DaoAuthorizableUpgradeable is ContextUpgradeable {

```

```solidity
File: packages/contracts/src/core/plugin/membership/IMembership.sol

10: interface IMembership {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/IProposal.sol

10: interface IProposal {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

13: abstract contract Proposal is IProposal, ERC165 {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

13: abstract contract ProposalUpgradeable is IProposal, ERC165Upgradeable {

```

```solidity
File: packages/contracts/src/core/utils/CallbackHandler.sol

9: abstract contract CallbackHandler {

```

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

17: contract DAOFactory {

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

13: contract DAORegistry is InterfaceBasedRegistry {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/IPluginRepo.sol

8: interface IPluginRepo {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

19: contract PluginRepo is

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

13: contract PluginRepoFactory {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

14: contract PluginRepoRegistry is InterfaceBasedRegistry {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

11: interface IPluginSetup {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

16: abstract contract PluginSetup is ERC165, IPluginSetup {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

23: contract PluginSetupProcessor {

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

14: abstract contract InterfaceBasedRegistry is UUPSUpgradeable, DaoAuthorizableUpgradeable {

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

22: contract TokenFactory {

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSMigration.sol

10: contract Dummy {}

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

16: contract ENSSubdomainRegistrar is UUPSUpgradeable, DaoAuthorizableUpgradeable {

```

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

10: contract MultiplyHelper is PluginUUPSUpgradeable {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

12: contract CounterV1 is PluginUUPSUpgradeable {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

16: contract CounterV1PluginSetup is PluginSetup {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

12: contract CounterV2 is PluginUUPSUpgradeable {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

16: contract CounterV2PluginSetup is PluginSetup {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

15: contract Admin is IMembership, PluginCloneable, ProposalUpgradeable {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

16: contract AdminSetup is PluginSetup {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol

11: interface IMajorityVoting {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

97: abstract contract MajorityVotingBase is

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

19: contract AddresslistVoting is IMembership, Addresslist, MajorityVotingBase {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

15: contract AddresslistVotingSetup is PluginSetup {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

18: contract TokenVoting is IMembership, MajorityVotingBase {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

24: contract TokenVotingSetup is PluginSetup {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/IMultisig.sol

10: interface IMultisig {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

18: contract Multisig is

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

14: contract MultisigSetup is PluginSetup {

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleDistributor.sol

10: interface IMerkleDistributor {

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleMinter.sol

11: interface IMerkleMinter {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

19: contract MerkleDistributor is IMerkleDistributor, PluginUUPSUpgradeable {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

20: contract MerkleMinter is IMerkleMinter, PluginUUPSUpgradeable {

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

13: abstract contract Addresslist {

```

```solidity
File: packages/contracts/src/token/ERC20/IERC20MintableUpgradeable.sol

7: interface IERC20MintableUpgradeable {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

20: contract GovernanceERC20 is

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

27: contract GovernanceWrappedERC20 is

```

```solidity
File: packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol

9: interface IGovernanceWrappedERC20 {

```

### [GAS-18] THE INCREMENT IN FOR LOOP POSTCONDITION CAN BE MADE UNCHECKED

#### Description:

This is only relevant if you are using the default solidity checked arithmetic.

The for loop postcondition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks.One can manually do this.

[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/framework/utils/RegistryUtils.sol

16:     for (uint256 i; i < nameLength; i++) {

```

### [GAS-19] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

#### Description:

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/plugin/Plugin.sol

20:     function pluginType() public pure override returns (PluginType) {

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

27:     function pluginType() public pure override returns (PluginType) {

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

30:     function pluginType() public pure override returns (PluginType) {

55:     function implementation() public view returns (address) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

25:     function dao() public view returns (IDAO) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

26:     function dao() public view returns (IDAO) {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

209:     function getLatestVersion(uint8 _release) public view returns (Version memory) {

217:     function getLatestVersion(address _pluginSetup) public view returns (Version memory) {

244:     function buildCount(uint8 _release) public view returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

44:     function execute() public {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

63:     function execute() public {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

283:     function execute(uint256 _proposalId) public virtual {

308:     function canExecute(uint256 _proposalId) public view virtual returns (bool) {

352:     function supportThreshold() public view virtual returns (uint32) {

357:     function minParticipation() public view virtual returns (uint32) {

363:     function minDuration() public view virtual returns (uint64) {

369:     function minProposerVotingPower() public view virtual returns (uint256) {

375:     function votingMode() public view virtual returns (VotingMode) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

61:     function getVotingToken() public view returns (IVotesUpgradeable) {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

328:     function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

47:     function addresslistLengthAtBlock(uint256 _blockNumber) public view virtual returns (uint256) {

53:     function addresslistLength() public view virtual returns (uint256) {

```

### [GAS-20] TERNARY OPERATION IS CHEAPER THAN IF-ELSE STATEMENT

#### Description:

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

570:        if (_start == 0) {
571:            startDate = currentTimestamp;
572:        } else {
573:            startDate = _start;

```

### [GAS-21] USAGE OF `UINT`/`INT` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

#### Description:

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

203:                 if (!hasBit(_allowFailureMap, uint8(i))) {

209:                 failureMap = flipBit(failureMap, uint8(i));

```

```solidity
File: packages/contracts/src/core/plugin/proposal/IProposal.sol

22:         uint64 startDate,

23:         uint64 endDate,

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

48:         uint64 _startDate,

49:         uint64 _endDate,

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

48:         uint64 _startDate,

49:         uint64 _endDate,

```

```solidity
File: packages/contracts/src/core/utils/BitMap.sol

8: function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {

16: function flipBit(uint256 bitmap, uint8 index) pure returns (uint256) {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/IPluginRepo.sol

12:     function updateReleaseMetadata(uint8 _release, bytes calldata _releaseMetadata) external;

20:         uint8 _release,

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

34:         uint8 release;

35:         uint16 build;

55:     mapping(uint8 => uint16) internal buildsPerRelease;

55:     mapping(uint8 => uint16) internal buildsPerRelease;

65:     uint8 public latestRelease;

80:     error InvalidReleaseIncrement(uint8 latestRelease, uint8 newRelease);

86:     error PluginSetupAlreadyInPreviousRelease(uint8 release, uint16 build, address pluginSetup);

100:         uint8 release,

101:         uint16 build,

109:     event ReleaseMetadataUpdated(uint8 release, bytes releaseMetadata);

129:         uint8 _release,

165:         uint16 build = ++buildsPerRelease[_release];

188:         uint8 _release,

209:     function getLatestVersion(uint8 _release) public view returns (Version memory) {

210:         uint16 latestBuild = uint16(buildsPerRelease[_release]);

244:     function buildCount(uint8 _release) public view returns (uint256) {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

48:         uint16 _currentBuild,

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

20:         uint16 _currentBuild,

```

```solidity
File: packages/contracts/src/framework/utils/RegistryUtils.sol

17:         uint8 char = uint8(nameBytes[i]);

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

103:         uint16 _currentBuild,

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

67:         uint64 currentTimestamp64 = block.timestamp.toUint64();

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol

38:     function supportThreshold() external view returns (uint32);

42:     function minParticipation() external view returns (uint32);

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

124:         uint32 supportThreshold;

125:         uint32 minParticipation;

126:         uint64 minDuration;

155:         uint32 supportThreshold;

156:         uint64 startDate;

157:         uint64 endDate;

158:         uint64 snapshotBlock;

195:     error DateOutOfBounds(uint64 limit, uint64 actual);

200:     error MinDurationOutOfBounds(uint64 limit, uint64 actual);

228:         uint32 supportThreshold,

229:         uint32 minParticipation,

230:         uint64 minDuration,

352:     function supportThreshold() public view virtual returns (uint32) {

357:     function minParticipation() public view virtual returns (uint32) {

363:     function minDuration() public view virtual returns (uint64) {

438:         uint64 _startDate,

439:         uint64 _endDate,

516:         uint64 currentTime = block.timestamp.toUint64();

565:         uint64 _start,

566:         uint64 _end

567:     ) internal view virtual returns (uint64 startDate, uint64 endDate) {

568:         uint64 currentTimestamp = block.timestamp.toUint64();

580:         uint64 earliestEndDate = startDate + votingSettings.minDuration; // Since `minDuration` is limited to 1 year, `startDate + minDuration` can only overflow if the `startDate` is after `type(uint64).max - minDuration`. In this case, the proposal creation will revert and another date can be picked.

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

87:         uint64 _startDate,

88:         uint64 _endDate,

92:         uint64 snapshotBlock;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

75:         uint64 _startDate,

76:         uint64 _endDate,

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

36:         uint16 approvals;

49:         uint16 minApprovals;

50:         uint64 snapshotBlock;

51:         uint64 startDate;

52:         uint64 endDate;

60:         uint16 minApprovals;

99:     error MinApprovalsOutOfBounds(uint16 limit, uint16 actual);

104:     error AddresslistLengthOutOfBounds(uint16 limit, uint256 actual);

109:     error DateOutOfBounds(uint64 limit, uint64 actual);

119:     event MultisigSettingsUpdated(bool onlyListed, uint16 indexed minApprovals);

159:         if (newAddresslistLength > type(uint16).max) {

161:                 limit: type(uint16).max,

175:         uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

211:         uint64 _startDate,

212:         uint64 _endDate

214:         uint64 snapshotBlock = block.number.toUint64() - 1;

312:             uint16 approvals,

404:         uint64 currentTimestamp64 = block.timestamp.toUint64();

414:         uint16 addresslistLength_ = uint16(addresslistLength());

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

75:         returns (uint8)

```

### [GAS-22] UNNECESSARY LIBRARIES

#### Description:

Libraries are often only imported for a small number of uses, meaning that they can contain a significant amount of code that is redundant to your contract. If you can safely and effectively implement the functionality imported from a library within your contract, it is optimal to do so.

[Source](https://betterprogramming.pub/how-to-write-smart-contracts-that-optimize-gas-spent-on-ethereum-30b5e9c5db85)

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

6: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

9: import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

11: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";

13: import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSMigration.sol

7: import "@ensdomains/ens-contracts/contracts/registry/ENSRegistry.sol";

8: import "@ensdomains/ens-contracts/contracts/resolvers/PublicResolver.sol";

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

5: import "@ensdomains/ens-contracts/contracts/registry/ENS.sol";

11: import {IDAO} from "../../../core/dao/IDAO.sol";

```

```solidity
File: packages/contracts/src/utils/Proxy.sol

5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";
import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

### [GAS-23] USE NAMED RETURNS WHERE APPROPRIATE

#### Description:

Do not use return at the end of the function:

Chodzi o to, ze jesli w funkcji mam returns i w nawiasie daje typ danych i ich mazwe to na koncu funkcji nie musze juz uzywac slowa return, bo to tylko marnuje gaz

```solidity
File: packages/contracts/src/core/dao/DAO.sol

127:     ) internal pure override returns (bool) {

156:         returns (address)

167:     ) external view override returns (bool) {

272:     ) external view override(IDAO, IERC1271) returns (bytes4) {

291:     fallback(bytes calldata _input) external returns (bytes memory) {

345:     function daoURI() external view returns (string memory) {

```

```solidity
File: packages/contracts/src/core/dao/IDAO.sol

30:     ) external view returns (bool);

50:     ) external returns (bytes[] memory, uint256);

107:     function getTrustedForwarder() external view returns (address);

125:     function isValidSignature(bytes32 _hash, bytes memory _signature) external returns (bytes4);

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

203:     ) public view virtual returns (bool) {

302:     ) internal view virtual returns (bool) {

346:     ) internal pure virtual returns (bytes32) {

356:     ) internal view virtual returns (bool) {

```

```solidity
File: packages/contracts/src/core/plugin/IPlugin.sol

16:     function pluginType() external view returns (PluginType);

```

```solidity
File: packages/contracts/src/core/plugin/Plugin.sol

20:     function pluginType() public pure override returns (PluginType) {

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

27:     function pluginType() public pure override returns (PluginType) {

34:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

30:     function pluginType() public pure override returns (PluginType) {

46:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

55:     function implementation() public view returns (address) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

25:     function dao() public view returns (IDAO) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

26:     function dao() public view returns (IDAO) {

```

```solidity
File: packages/contracts/src/core/plugin/membership/IMembership.sol

27:     function isMember(address _account) external view returns (bool);

```

```solidity
File: packages/contracts/src/core/plugin/proposal/IProposal.sol

35:     function proposalCount() external view returns (uint256);

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

20:     function proposalCount() public view override returns (uint256) {

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

20:     function proposalCount() public view override returns (uint256) {

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/core/utils/BitMap.sol

8: function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {

16: function flipBit(uint256 bitmap, uint8 index) pure returns (uint256) {

```

```solidity
File: packages/contracts/src/core/utils/CallbackHandler.sol

34:     ) internal virtual returns (bytes4) {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

209:     function getLatestVersion(uint8 _release) public view returns (Version memory) {

217:     function getLatestVersion(address _pluginSetup) public view returns (Version memory) {

224:     function getVersion(Tag calldata _tag) public view returns (Version memory) {

231:     function getVersion(bytes32 _tagHash) public view returns (Version memory) {

244:     function buildCount(uint8 _release) public view returns (uint256) {

251:     function tagHash(Tag memory _tag) internal pure returns (bytes32) {

264:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

34:     ) external returns (PluginRepo) {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

64:     function implementation() external view returns (address);

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

36:     ) internal returns (address) {

43:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

32: function _getPluginInstallationId(address _dao, address _plugin) pure returns (bytes32) {

49: ) pure returns (bytes32) {

70: ) pure returns (bytes32) {

79: function hashHelpers(address[] memory _helpers) pure returns (bytes32) {

88: ) pure returns (bytes32) {

```

```solidity
File: packages/contracts/src/framework/utils/RegistryUtils.sol

13: function isSubdomainValid(string calldata subDomain) pure returns (bool) {

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

82:     ) external returns (ERC20VotesUpgradeable, MerkleMinter) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

19:     ) external view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

39:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

137:     function implementation() external view virtual override returns (address) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

58:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

177:     function implementation() external view virtual override returns (address) {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

40:     ) public view override(PluginCloneable, ProposalUpgradeable) returns (bool) {

48:     function isMember(address _account) external view returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

93:     function implementation() external view returns (address) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol

38:     function supportThreshold() external view returns (uint32);

42:     function minParticipation() external view returns (uint32);

47:     function isSupportThresholdReached(uint256 _proposalId) external view returns (bool);

52:     function isSupportThresholdReachedEarly(uint256 _proposalId) external view returns (bool);

57:     function isMinParticipationReached(uint256 _proposalId) external view returns (bool);

73:     ) external view returns (bool);

78:     function canExecute(uint256 _proposalId) external view returns (bool);

98:     ) external view returns (VoteOption);

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

256:         returns (bool)

294:     ) public view virtual returns (VoteOption) {

303:     ) public view virtual returns (bool) {

308:     function canExecute(uint256 _proposalId) public view virtual returns (bool) {

313:     function isSupportThresholdReached(uint256 _proposalId) public view virtual returns (bool) {

326:     ) public view virtual returns (bool) {

341:     function isMinParticipationReached(uint256 _proposalId) public view virtual returns (bool) {

352:     function supportThreshold() public view virtual returns (uint32) {

357:     function minParticipation() public view virtual returns (uint32) {

363:     function minDuration() public view virtual returns (uint64) {

369:     function minProposerVotingPower() public view virtual returns (uint256) {

375:     function votingMode() public view virtual returns (VotingMode) {

382:     function totalVotingPower(uint256 _blockNumber) public view virtual returns (uint256);

398:         returns (

477:     ) internal view virtual returns (bool);

483:     function _canExecute(uint256 _proposalId) internal view virtual returns (bool) {

515:     function _isProposalOpen(Proposal storage proposal_) internal view virtual returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

48:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

78:     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {

143:     function isMember(address _account) external view returns (bool) {

195:     ) internal view override returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

129:     function implementation() external view returns (address) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

51:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

61:     function getVotingToken() public view returns (IVotesUpgradeable) {

66:     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {

137:     function isMember(address _account) external view returns (bool) {

192:     ) internal view override returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

258:     function implementation() external view virtual override returns (address) {

265:     function _getTokenInterfaceIds(address token) private view returns (bool[] memory) {

276:     function _isERC20(address token) private view returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/IMultisig.sol

31:     function canApprove(uint256 _proposalId, address _account) external view returns (bool);

36:     function canExecute(uint256 _proposalId) external view returns (bool);

42:     function hasApproved(uint256 _proposalId, address _account) external view returns (bool);

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

143:     ) public view virtual override(PluginUUPSUpgradeable, ProposalUpgradeable) returns (bool) {

289:     function canApprove(uint256 _proposalId, address _account) external view returns (bool) {

294:     function canExecute(uint256 _proposalId) external view returns (bool) {

310:         returns (

328:     function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {

342:     function isMember(address _account) external view returns (bool) {

365:     function _canApprove(uint256 _proposalId, address _account) internal view returns (bool) {

389:     function _canExecute(uint256 _proposalId) internal view returns (bool) {

403:     function _isProposalOpen(Proposal storage proposal_) internal view returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

109:     function implementation() external view returns (address) {

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleDistributor.sol

18:     function token() external returns (IERC20Upgradeable);

21:     function merkleRoot() external returns (bytes32);

46:     ) external returns (uint256);

51:     function isClaimed(uint256 _index) external view returns (bool);

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleMinter.sol

27:     function token() external returns (IERC20MintableUpgradeable);

30:     function distributorBase() external returns (IMerkleDistributor);

47:     ) external returns (IMerkleDistributor distributor);

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

59:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

88:     ) public view override returns (uint256) {

104:     ) internal view returns (bool) {

110:     function isClaimed(uint256 _index) public view override returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

62:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

33:     ) public view virtual returns (bool) {

40:     function isListed(address _account) public view virtual returns (bool) {

47:     function addresslistLengthAtBlock(uint256 _blockNumber) public view virtual returns (uint256) {

53:     function addresslistLength() public view virtual returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/utils/Ratio.sol

17: function _applyRatioCeiled(uint256 _value, uint256 _ratio) pure returns (uint256 result) {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

93:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

59:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

75:         returns (uint8)

84:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {

92:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol

13:     function depositFor(address account, uint256 amount) external returns (bool);

18:     function withdrawTo(address account, uint256 amount) external returns (bool);

```

```solidity
File: packages/contracts/src/utils/Proxy.sol

12: function createERC1967Proxy(address _logic, bytes memory _data) returns (address) {

```

```solidity
File: packages/contracts/src/utils/UncheckedMath.sol

8: function _uncheckedIncrement(uint256 i) pure returns (uint256) {

19: function _uncheckedAdd(uint256 a, uint256 b) pure returns (uint256) {

29: function _uncheckedSub(uint256 a, uint256 b) pure returns (uint256) {

```
