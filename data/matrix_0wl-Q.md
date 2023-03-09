# Summary

## Low Risk and Non-Critical Issues

## Non-Critical Issues

|       | Issue                                                                                    |
| ----- | :--------------------------------------------------------------------------------------- |
| NC-1  | ADD A TIMELOCK TO CRITICAL FUNCTIONS                                                     |
| NC-2  | USE OF `BYTES.CONCAT()` INSTEAD OF `ABI.ENCODEPACKED(,)`                                 |
| NC-3  | GENERATE PERFECT CODE HEADERS EVERY TIME                                                 |
| NC-4  | USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS                                          |
| NC-5  | FUNCTIONS, PARAMETERS AND VARIABLES IN SNAKE CASE                                        |
| NC-6  | FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES                                  |
| NC-7  | MARK VISIBILITY OF INITIALIZE(…) FUNCTIONS AS EXTERNAL                                   |
| NC-8  | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT |
| NC-9  | LACK OF EVENT EMISSION AFTER CRITICAL INITIALIZE() FUNCTIONS                             |
| NC-10 | MISSING CHECKS FOR `ADDRESS(0)`                                                          |
| NC-11 | MISSING EVENT FOR CRITICAL PARAMETER CHANGE                                              |
| NC-12 | NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS                                        |
| NC-13 | NO SAME VALUE INPUT CONTROL                                                              |
| NC-14 | OMISSIONS IN EVENTS                                                                      |
| NC-15 | SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC                                       |
| NC-16 | ORDER OF FUNCTIONS                                                                       |
| NC-17 | USE A MORE RECENT VERSION OF SOLIDITY                                                    |
| NC-18 | FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS                  |
| NC-19 | LONG LINES ARE NOT SUITABLE FOR THE SOLIDITY STYLE GUIDE                                 |
| NC-20 | USE OZ MERKLETREE IMPLEMENTATION INSTEAD OF CREATING A NEW ONE                           |
| NC-21 | FUNCTIONS NOT USED INTERNALLY COULD BE MARKED EXTERNAL                                   |

### [NC-1] ADD A TIMELOCK TO CRITICAL FUNCTIONS

#### Description:

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

144:     function setTrustedForwarder(

172:     function setMetadata(

260:     function setSignatureValidator(

298:     function _setMetadata(bytes calldata _metadata) internal {

304:     function _setTrustedForwarder(address _trustedForwarder) internal {

351:     function setDaoURI(

359:     function _setDaoURI(string calldata daoURI_) internal {

```

```solidity
File: packages/contracts/src/core/dao/IDAO.sol

34:     function setMetadata(bytes calldata _metadata) external;

103:     function setTrustedForwarder(address _trustedForwarder) external;

115:     function setSignatureValidator(address _signatureValidator) external;

```

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

156:     function _setDAOPermissions(DAO _dao) internal {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

65:     function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal {

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

144:     function setupBases() private {

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

100:     function setDefaultResolver(

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

52:     function setNewVariable(uint256 _newVariable) external reinitializer(2) {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

76:         _setClaimed(_index);

120:     function _setClaimed(uint256 _index) private {

```

### [NC-2] USE OF `BYTES.CONCAT()` INSTEAD OF `ABI.ENCODEPACKED(,)`

#### Description:

Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, `bytes.concat()` is enabled.

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,).

Reference: https://swcregistry.io/docs/SWC-133

Reference: https://docs.soliditylang.org/en/v0.5.3/abi-spec.html#non-standard-packed-mode

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
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

86:         bytes32 subnode = keccak256(abi.encodePacked(node, _label));

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

105:         bytes32 node = keccak256(abi.encodePacked(_index, _to, _amount));

```

### [NC-3] GENERATE PERFECT CODE HEADERS EVERY TIME

#### Description:

I recommend using header for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [NC-4] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS

#### Description:

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes.

Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

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

### [NC-5] FUNCTIONS, PARAMETERS AND VARIABLES IN SNAKE CASE

#### Description:

Use camel case for all functions, parameters and variables and snake case for constants

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

111:         uint256 claimedWord_index = _index / 256;

112:         uint256 claimedBit_index = _index % 256;

113:         uint256 claimedWord = claimedBitMap[claimedWord_index];

121:         uint256 claimedWord_index = _index / 256;

122:         uint256 claimedBit_index = _index % 256;

```

### [NC-6] FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES

#### Description:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.

This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

`import {contract1 , contract2} from "filename.sol";` OR Use specific imports syntax per solidity docs recommendation.

```solidity
File: packages/contracts/src/core/dao/DAO.sol

5: import "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165StorageUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

7: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

8: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

9: import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

10: import "@openzeppelin/contracts-upgradeable/token/ERC721/IERC721ReceiverUpgradeable.sol";

11: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";

12: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155ReceiverUpgradeable.sol";

13: import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";

14: import "@openzeppelin/contracts/interfaces/IERC1271.sol";

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

7: import "./IPermissionCondition.sol";

8: import "./PermissionLib.sol";

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

8: import "./IProposal.sol";

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

8: import "./IProposal.sol";

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSMigration.sol

7: import "@ensdomains/ens-contracts/contracts/registry/ENSRegistry.sol";

8: import "@ensdomains/ens-contracts/contracts/resolvers/PublicResolver.sol";

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

5: import "@ensdomains/ens-contracts/contracts/registry/ENS.sol";

6: import "@ensdomains/ens-contracts/contracts/resolvers/Resolver.sol";

```

```solidity
File: packages/contracts/src/utils/Proxy.sol

5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

```

### [NC-7] MARK VISIBILITY OF INITIALIZE(…) FUNCTIONS AS EXTERNAL

#### Description:

If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}\_init function, not the parent public initialize(...) function.

External instead of public would give more sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally).

From a security point of view, it might be safer so that it cannot be called internally by accident in the child contract.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

63:     function initialize(
64:            IDAO _dao,
65:        string memory _name,
66:        string memory _symbol,
67:        MintSettings memory _mintSettings
68:    ) public initializer {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

46:     function initialize(
47:            IERC20Upgradeable _token,
48:        string memory _name,
49:        string memory _symbol
50:    ) public initializer {

```

### [NC-8] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### Description:

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

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

### [NC-9] LACK OF EVENT EMISSION AFTER CRITICAL INITIALIZE() FUNCTIONS

#### Description:

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the initialize() functions:

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

107:     function initialize(

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

37:     function initialize(

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

120:     function initialize(address initialOwner) external initializer {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

41:     function initialize(IDAO _dao, ENSSubdomainRegistrar _subdomainRegistrar) external initializer {

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

57:     function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

26:     function initialize(

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

32:     function initialize(

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

29:     function initialize(IDAO _dao) external initializer {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

34:     function initialize(

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

36:     function initialize(

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

125:     function initialize(

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

46:     function initialize(

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

41:     function initialize(

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

63:     function initialize(

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

46:     function initialize(

```

### [NC-10] MISSING CHECKS FOR `ADDRESS(0)`

#### Description:

0 address control should be done in these parts;

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

305:         trustedForwarder = _trustedForwarder;

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

107:         resolver = _resolver;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

41:         address multiplyHelper = _multiplyHelper;

```

#### Recommended Mitigation Steps:

Add code like this: `if (oracle == address(0)) revert ADDRESS_ZERO();` or `require(address(\_VARIABLE) != address(0), "Address cannot be zero");

### [NC-11] MISSING EVENT FOR CRITICAL PARAMETER CHANGE

#### Description:

Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

https://github.com/crytic/slither/wiki/Detector-Documentation#missing-events-access-control

### [NC-12] NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS

#### Description:

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

### [NC-13] NO SAME VALUE INPUT CONTROL

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

305:         trustedForwarder = _trustedForwarder;

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

20:         dao_ = _dao;

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

21:         dao_ = _dao;

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

42:         subdomainRegistrar = _subdomainRegistrar;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

148:             latestRelease = _release;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

23:         pluginRepoRegistry = _pluginRepoRegistry;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

45:         subdomainRegistrar = _subdomainRegistrar;

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

278:         repoRegistry = _repoRegistry;

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

49:         targetInterfaceId = _targetInterfaceId;

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

60:         ens = _ens;

61:         node = _node;

107:         resolver = _resolver;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

33:         count = _count;

34:         multiplyHelper = _multiplyHelper;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

41:         address multiplyHelper = _multiplyHelper;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

40:         count = _count;

44:         newVariable = _newVariable;

46:         multiplyHelper = _multiplyHelper;

53:         newVariable = _newVariable;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

25:         multiplyHelperBase = _helper;

42:         address multiplyHelper = _multiplyHelper;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

573:             startDate = _start;

585:             endDate = _end;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

43:         votingToken = _token;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

427:         multisigSettings = _multisigSettings;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

52:         token = _token;

53:         merkleRoot = _merkleRoot;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

48:         token = _token;

49:         distributorBase = _distributorBase;

56:         distributorBase = _distributorBase;

```

### [NC-14] OMISSIONS IN EVENTS

#### Description:

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

The events should include the new value and old value where possible.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

299:         emit MetadataSet(_metadata);

307:         emit TrustedForwarderSet(_trustedForwarder);

337:         emit StandardCallbackRegistered(

362:         emit NewURI(daoURI_);

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

79:         emit ProposalExecuted({proposalId: _proposalId});

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

79:         emit ProposalExecuted({proposalId: _proposalId});

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

106:             emit WrappedToken(GovernanceWrappedERC20(token));

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

32:         emit MembershipContractAnnounced({definingContract: address(_dao)});

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

42:         emit MembersAdded({members: _members});

64:         emit MembersAdded({members: _members});

74:         emit MembersRemoved({members: _members});

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

45:         emit MembershipContractAnnounced({definingContract: address(_token)});

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

133:         emit MembersAdded({members: _members});

168:         emit MembersAdded({members: _members});

187:         emit MembersRemoved({members: _members});

```

### [NC-15] SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC

#### Description:

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

#### **Proof Of Concept**

```solidity
File: hardhat.config.ts:

solidity: {
48:    version: '0.8.17',
49:    settings: {
50:      optimizer: {
51:        enabled: true,
52:        runs: 2000,
53:      },
```

#### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.

Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [NC-16] ORDER OF FUNCTIONS

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private, within a grouping, place the view and pure functions last.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

### [NC-17] USE A MORE RECENT VERSION OF SOLIDITY

#### Description:

For security, it is best practice to use the latest Solidity version (0.8.19). For the security fix list in the versions;

https://github.com/ethereum/solidity/blob/develop/Changelog.md

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/dao/IDAO.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/dao/IEIP4824.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/permission/IPermissionCondition.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/permission/PermissionLib.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/IPlugin.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/Plugin.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/membership/IMembership.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/proposal/IProposal.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/utils/BitMap.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/utils/CallbackHandler.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/core/utils/auth.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/IPluginRepo.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/utils/RegistryUtils.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSMigration.sol

5: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/IMultisig.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleDistributor.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleMinter.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

5: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/plugins/utils/Ratio.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/token/ERC20/IERC20MintableUpgradeable.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/utils/Proxy.sol

3: pragma solidity 0.8.17;

```

```solidity
File: packages/contracts/src/utils/UncheckedMath.sol

3: pragma solidity 0.8.17;

```

### [NC-18] FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

#### Description:

Solidity’s standard naming convention for internal and private functions and variables (apart from constants): the mixedCase format starting with an underscore (\_mixedCase starting with an underscore)

Solidity’s standard naming convention for internal and private constants variables: the snake_case format starting with an underscore (\_mixedCase starting with an underscore) and use ALL_CAPS for naming them.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

64:     uint256 internal constant MAX_ACTIONS = 256;

70:     address private trustedForwarder;

366:     uint256[47] private __gap;

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

27:     mapping(bytes32 => address) internal permissionsHashed;

95:     function __PermissionManager_init(address _initialOwner) internal onlyInitializing {

362:     uint256[49] private __gap;

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

22:     function __PluginCloneable_init(IDAO _dao) internal virtual onlyInitializing {

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

39:     function __PluginUUPSUpgradeable_init(IDAO _dao) internal virtual onlyInitializing {

66:     uint256[50] private __gap;

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

15:     IDAO private immutable dao_;

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

16:     IDAO private dao_;

20:     function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {

38:     uint256[49] private __gap;

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

17:     Counters.Counter private proposalCounter;

33:     function _createProposalId() internal returns (uint256 proposalId) {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

17:     CountersUpgradeable.Counter private proposalCounter;

83:     uint256[49] private __gap;

```

```solidity
File: packages/contracts/src/core/utils/CallbackHandler.sol

11:     mapping(bytes4 => bytes4) internal callbackMagicNumbers;

53:     uint256[49] private __gap;

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

73:     uint256[49] private __gap;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

55:     mapping(uint8 => uint16) internal buildsPerRelease;

58:     mapping(bytes32 => Version) internal versions;

61:     mapping(address => bytes32) internal latestTagHashForPluginSetup;

251:     function tagHash(Tag memory _tag) internal pure returns (bytes32) {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

72:     uint256[49] private __gap;

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

75:     uint256[48] private __gap;

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

144:     function setupBases() private {

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

111:     uint256[47] private __gap;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

50:     uint256[48] private __gap;

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

69:     uint256[47] private __gap;

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

20:     address private immutable implementation_;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

173:     bytes4 internal constant MAJORITY_VOTING_BASE_INTERFACE_ID =

187:     mapping(uint256 => Proposal) internal proposals;

594:     uint256[47] private __gap;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

227:     uint256[50] private __gap;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

17:     AddresslistVoting private immutable addresslistVotingBase;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

26:     IVotesUpgradeable private votingToken;

224:     uint256[49] private __gap;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

30:     TokenVoting private immutable tokenVotingBase;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

75:     mapping(uint256 => Proposal) internal proposals;

438:     uint256[48] private __gap;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

16:     Multisig private immutable multisigBase;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

29:     mapping(uint256 => uint256) private claimedBitMap;

129:     uint256[47] private __gap;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

98:     uint256[48] private __gap;

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

96:     uint256[48] private __gap;

```

### [NC-19] LONG LINES ARE NOT SUITABLE FOR THE SOLIDITY STYLE GUIDE

#### Description:

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

5: import "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165StorageUpgradeable.sol";

8: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

10: import "@openzeppelin/contracts-upgradeable/token/ERC721/IERC721ReceiverUpgradeable.sol";

11: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";

12: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155ReceiverUpgradeable.sol";

```

```solidity
File: packages/contracts/src/core/dao/IDAO.sol

81:     function deposit(address _token, uint256 _amount, string calldata _reference) external payable;

125:     function isValidSignature(bytes32 _hash, bytes memory _signature) external returns (bytes4);

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

95:     function __PermissionManager_init(address _initialOwner) internal onlyInitializing {

177:             } else if (item.operation == PermissionLib.Operation.GrantWithCondition) {

205:             _isGranted(_where, _who, _permissionId, _data) || // check if `_who` has permission for `_permissionId` on `_where`

206:             _isGranted(_where, ANY_ADDR, _permissionId, _data) || // check if anyone has permission for `_permissionId` on `_where`

207:             _isGranted(ANY_ADDR, _who, _permissionId, _data); // check if `_who` has permission for `_permissionI` on any contract

220:     function _grant(address _where, address _who, bytes32 _permissionId) internal virtual {

221:         _grantWithCondition(_where, _who, _permissionId, IPermissionCondition(ALLOW_FLAG));

241:             bool isRestricted = isPermissionRestrictedForAnyAddr(_permissionId);

282:     function _revoke(address _where, address _who, bytes32 _permissionId) internal virtual {

347:         return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));

```

```solidity
File: packages/contracts/src/core/plugin/Plugin.sol

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

28:         return _interfaceId == type(IPlugin).interfaceId || super.supportsInterface(_interfaceId);

```

```solidity
File: packages/contracts/src/core/plugin/PluginCloneable.sol

5: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

7: import {DaoAuthorizableUpgradeable} from "./dao-authorizable/DaoAuthorizableUpgradeable.sol";

14: abstract contract PluginCloneable is IPlugin, ERC165Upgradeable, DaoAuthorizableUpgradeable {

22:     function __PluginCloneable_init(IDAO _dao) internal virtual onlyInitializing {

34:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

35:         return _interfaceId == type(IPlugin).interfaceId || super.supportsInterface(_interfaceId);

```

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

5: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol";

7: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

10: import {DaoAuthorizableUpgradeable} from "./dao-authorizable/DaoAuthorizableUpgradeable.sol";

35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

39:     function __PluginUUPSUpgradeable_init(IDAO _dao) internal virtual onlyInitializing {

46:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

5: import {ContextUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";

20:     function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/Proposal.sol

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

28:         return _interfaceId == type(IProposal).interfaceId || super.supportsInterface(_interfaceId);

77:     ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {

78:         (execResults, failureMap) = _dao.execute(bytes32(_proposalId), _actions, _allowFailureMap);

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

5: import {CountersUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";

6: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

28:         return _interfaceId == type(IProposal).interfaceId || super.supportsInterface(_interfaceId);

77:     ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {

78:         (execResults, failureMap) = _dao.execute(bytes32(_proposalId), _actions, _allowFailureMap);

```

```solidity
File: packages/contracts/src/core/utils/CallbackHandler.sol

37:             revert UnkownCallback({callbackSelector: _callbackSelector, magicNumber: magicNumber});

40:         emit CallbackReceived({sender: msg.sender, sig: _callbackSelector, data: _data});

48:     function _registerCallback(bytes4 _callbackSelector, bytes4 _magicNumber) internal virtual {

```

```solidity
File: packages/contracts/src/core/utils/auth.sol

12: error DaoUnauthorized(address dao, address where, address who, bytes32 permissionId);

```

```solidity
File: packages/contracts/src/framework/dao/DAOFactory.sol

10: import {hashHelpers, PluginSetupRef} from "../plugin/setup/PluginSetupProcessorHelpers.sol";

53:     constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {

85:         createdDao.grant(address(createdDao), address(pluginSetupProcessor), rootPermissionID);

125:         createdDao.revoke(address(createdDao), address(pluginSetupProcessor), rootPermissionID);

136:         createdDao.revoke(address(createdDao), address(this), rootPermissionID);

141:     function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

27:     event DAORegistered(address indexed dao, address indexed creator, string subdomain);

```

```solidity
File: packages/contracts/src/framework/plugin/repo/IPluginRepo.sol

12:     function updateReleaseMetadata(uint8 _release, bytes calldata _releaseMetadata) external;

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

5: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

6: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

7: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

8: import {AddressUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";

9: import {ERC165CheckerUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165CheckerUpgradeable.sol";

11: import {PermissionManager} from "../../../core/permission/PermissionManager.sol";

49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

86:     error PluginSetupAlreadyInPreviousRelease(uint8 release, uint16 build, address pluginSetup);

144:             revert InvalidReleaseIncrement({latestRelease: latestRelease, newRelease: _release});

156:         Version storage version = versions[latestTagHashForPluginSetup[_pluginSetup]];

209:     function getLatestVersion(uint8 _release) public view returns (Version memory) {

217:     function getLatestVersion(address _pluginSetup) public view returns (Version memory) {

224:     function getVersion(Tag calldata _tag) public view returns (Version memory) {

231:     function getVersion(bytes32 _tagHash) public view returns (Version memory) {

264:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

55:         pluginRepo.createVersion(1, _pluginSetup, _buildMetadata, _releaseMetadata);

65:     function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal {

122:                 abi.encodeWithSelector(PluginRepo.initialize.selector, _initialOwner)

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

6: import {ENSSubdomainRegistrar} from "../../utils/ens/ENSSubdomainRegistrar.sol";

41:     function initialize(IDAO _dao, ENSSubdomainRegistrar _subdomainRegistrar) external initializer {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

38:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData);

50:     ) external returns (bytes memory initData, PreparedSetupData memory preparedSetupData);

59:     ) external returns (PermissionLib.MultiTargetPermission[] memory permissions);

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

6: import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

26:         returns (bytes memory initData, PreparedSetupData memory preparedSetupData)

43:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

45:             _interfaceId == type(IPluginSetup).interfaceId || super.supportsInterface(_interfaceId);

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

5: import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

9: import {PluginUUPSUpgradeable} from "../../../core/plugin/PluginUUPSUpgradeable.sol";

17: import {PluginSetupRef, hashHelpers, hashPermissions, _getPreparedSetupId, _getAppliedSetupId, _getPluginInstallationId, PreparationType} from "./PluginSetupProcessorHelpers.sol";

31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

135:     error SetupApplicationUnauthorized(address dao, address caller, bytes32 permissionId);

145:     error PluginProxyUpgradeFailed(address proxy, address implementation, bytes initData);

165:     error InvalidUpdateVersion(PluginRepo.Tag currentVersionTag, PluginRepo.Tag newVersionTag);

173:     error InvalidAppliedSetupId(bytes32 currentAppliedSetupId, bytes32 appliedSetupId);

289:     ) external returns (address plugin, IPluginSetup.PreparedSetupData memory preparedSetupData) {

303:         (plugin, preparedSetupData) = PluginSetup(version.pluginSetup).prepareInstallation(

326:         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {

330:         pluginState.preparedSetupIdToBlockNumber[preparedSetupId] = block.number;

353:         bytes32 pluginInstallationId = _getPluginInstallationId(_dao, _params.plugin);

372:         bytes32 appliedSetupId = _getAppliedSetupId(_params.pluginSetupRef, _params.helpersHash);

379:             DAO(payable(_dao)).applyMultiTargetPermissions(_params.permissions);

401:         returns (bytes memory initData, IPluginSetup.PreparedSetupData memory preparedSetupData)

404:             _params.currentVersionTag.release != _params.newVersionTag.release ||

413:         bytes32 pluginInstallationId = _getPluginInstallationId(_dao, _params.setupPayload.plugin);

417:         bytes32 currentHelpersHash = hashHelpers(_params.setupPayload.currentHelpers);

433:         PluginRepo.Version memory currentVersion = _params.pluginSetupRepo.getVersion(

437:         PluginRepo.Version memory newVersion = _params.pluginSetupRepo.getVersion(

461:             if (!_params.setupPayload.plugin.supportsInterface(type(IPlugin).interfaceId)) {

462:                 revert IPluginNotSupported({plugin: _params.setupPayload.plugin});

464:             if (IPlugin(_params.setupPayload.plugin).pluginType() != IPlugin.PluginType.UUPS) {

465:                 revert PluginNonupgradeable({plugin: _params.setupPayload.plugin});

469:             (initData, preparedSetupData) = PluginSetup(newVersion.pluginSetup).prepareUpdate(

485:         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {

489:         pluginState.preparedSetupIdToBlockNumber[preparedSetupId] = block.number;

492:         emitPrepareUpdateEvent(_dao, preparedSetupId, _params, preparedSetupData, initData);

504:         bytes32 pluginInstallationId = _getPluginInstallationId(_dao, _params.plugin);

518:         bytes32 appliedSetupId = _getAppliedSetupId(_params.pluginSetupRef, _params.helpersHash);

523:         PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(

527:         address currentImplementation = PluginUUPSUpgradeable(_params.plugin).implementation();

528:         address newImplementation = PluginSetup(version.pluginSetup).implementation();

536:             DAO(payable(_dao)).applyMultiTargetPermissions(_params.permissions);

555:     ) external returns (PermissionLib.MultiTargetPermission[] memory permissions) {

556:         bytes32 pluginInstallationId = _getPluginInstallationId(_dao, _params.setupPayload.plugin);

572:         PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(

590:         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {

594:         pluginState.preparedSetupIdToBlockNumber[preparedSetupId] = block.number;

616:         bytes32 pluginInstallationId = _getPluginInstallationId(_dao, _params.plugin);

636:             DAO(payable(_dao)).applyMultiTargetPermissions(_params.permissions);

655:         if (pluginState.blockNumber >= pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {

671:                 PluginUUPSUpgradeable(_proxy).upgradeToAndCall(_implementation, _initData)

682:             try PluginUUPSUpgradeable(_proxy).upgradeTo(_implementation) {} catch Error(

702:             !DAO(payable(_dao)).hasPermission(address(this), msg.sender, _permissionId, bytes(""))

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

32: function _getPluginInstallationId(address _dao, address _plugin) pure returns (bytes32) {

73:             abi.encode(_pluginSetupRef.versionTag, _pluginSetupRef.pluginSetupRepo, _helpersHash)

```

```solidity
File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

5: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import {ERC165CheckerUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165CheckerUpgradeable.sol";

8: import {DaoAuthorizableUpgradeable} from "../../core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol";

14: abstract contract InterfaceBasedRegistry is UUPSUpgradeable, DaoAuthorizableUpgradeable {

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

7: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

8: import {ERC20VotesUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20VotesUpgradeable.sol";

13: import {GovernanceERC20} from "../../token/ERC20/governance/GovernanceERC20.sol";

14: import {GovernanceWrappedERC20} from "../../token/ERC20/governance/GovernanceWrappedERC20.sol";

15: import {IERC20MintableUpgradeable} from "../../token/ERC20/IERC20MintableUpgradeable.sol";

89:                 abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))

128:         emit TokenCreated(IERC20Upgradeable(token), MerkleMinter(merkleMinter), distributorBase);

130:         bytes32 tokenMintPermission = GovernanceERC20(token).MINT_PERMISSION_ID();

131:         bytes32 merkleMintPermission = MerkleMinter(merkleMinter).MERKLE_MINT_PERMISSION_ID();

138:         _managingDao.grant(merkleMinter, address(_managingDao), merkleMintPermission);

151:                 GovernanceERC20.MintSettings(new address[](0), new uint256[](0))

155:             new GovernanceWrappedERC20(IERC20Upgradeable(address(0)), "baseName", "baseSymbol")

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

8: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

10: import {DaoAuthorizableUpgradeable} from "../../../core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol";

57:     function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {

```

```solidity
File: packages/contracts/src/plugins/counter-example/MultiplyHelper.sol

5: import {PluginUUPSUpgradeable} from "../../core/plugin/PluginUUPSUpgradeable.sol";

12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

5: import {PluginUUPSUpgradeable} from "../../../core/plugin/PluginUUPSUpgradeable.sol";

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

39:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

39:         (address _multiplyHelper, uint256 _num) = abi.decode(_data, (address, uint256));

44:             multiplyHelper = createERC1967Proxy(address(multiplyHelperBase), bytes(""));

103:     ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {

137:     function implementation() external view virtual override returns (address) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

5: import {PluginUUPSUpgradeable} from "../../../core/plugin/PluginUUPSUpgradeable.sol";

14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

58:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

40:         (address _multiplyHelper, uint256 _num) = abi.decode(_data, (address, uint256));

45:             multiplyHelper = createERC1967Proxy(address(multiplyHelperBase), bytes(""));

109:         returns (bytes memory initData, PreparedSetupData memory preparedSetupData)

143:     ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {

177:     function implementation() external view virtual override returns (address) {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

5: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

7: import {ProposalUpgradeable} from "../../../core/plugin/proposal/ProposalUpgradeable.sol";

40:     ) public view override(PluginCloneable, ProposalUpgradeable) returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

7: import {PluginSetup, IPluginSetup} from "../../../framework/plugin/setup/PluginSetup.sol";

35:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

79:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol

47:     function isSupportThresholdReached(uint256 _proposalId) external view returns (bool);

52:     function isSupportThresholdReachedEarly(uint256 _proposalId) external view returns (bool);

57:     function isMinParticipationReached(uint256 _proposalId) external view returns (bool);

85:     function vote(uint256 _proposalId, VoteOption _voteOption, bool _tryEarlyExecution) external;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

5: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

6: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

7: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

10: import {ProposalUpgradeable} from "../../../core/plugin/proposal/ProposalUpgradeable.sol";

11: import {PluginUUPSUpgradeable} from "../../../core/plugin/PluginUUPSUpgradeable.sol";

214:     error VoteCastForbidden(uint256 proposalId, address account, VoteOption voteOption);

308:     function canExecute(uint256 _proposalId) public view virtual returns (bool) {

313:     function isSupportThresholdReached(uint256 _proposalId) public view virtual returns (bool) {

319:             (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >

329:         uint256 noVotesWorstCase = totalVotingPower(proposal_.parameters.snapshotBlock) -

336:             (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >

341:     function isMinParticipationReached(uint256 _proposalId) public view virtual returns (bool) {

347:             proposal_.tally.yes + proposal_.tally.no + proposal_.tally.abstain >=

382:     function totalVotingPower(uint256 _blockNumber) public view virtual returns (uint256);

483:     function _canExecute(uint256 _proposalId) internal view virtual returns (bool) {

515:     function _isProposalOpen(Proposal storage proposal_) internal view virtual returns (bool) {

526:     function _updateVotingSettings(VotingSettings calldata _votingSettings) internal virtual {

537:             revert RatioOutOfBounds({limit: RATIO_BASE, actual: _votingSettings.minParticipation});

541:             revert MinDurationOutOfBounds({limit: 60 minutes, actual: _votingSettings.minDuration});

545:             revert MinDurationOutOfBounds({limit: 365 days, actual: _votingSettings.minDuration});

576:                 revert DateOutOfBounds({limit: currentTimestamp, actual: startDate});

580:         uint64 earliestEndDate = startDate + votingSettings.minDuration; // Since `minDuration` is limited to 1 year, `startDate + minDuration` can only overflow if the `startDate` is after `type(uint64).max - minDuration`. In this case, the proposal creation will revert and another date can be picked.

588:                 revert DateOutOfBounds({limit: earliestEndDate, actual: endDate});

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

5: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

24:         this.initialize.selector ^ this.addAddresses.selector ^ this.removeAddresses.selector;

48:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

78:     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {

97:         if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {

113:         (proposal_.parameters.startDate, proposal_.parameters.endDate) = _validateProposalDates({

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

8: import {PluginSetup, IPluginSetup} from "../../../../framework/plugin/setup/PluginSetup.sol";

28:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

30:         (MajorityVotingBase.VotingSettings memory votingSettings, address[] memory members) = abi

90:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

5: import {IVotesUpgradeable} from "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";

6: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

51:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

66:     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {

91:         if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {

107:         (proposal_.parameters.startDate, proposal_.parameters.endDate) = _validateProposalDates(

152:         uint256 votingPower = votingToken.getPastVotes(_voter, proposal_.parameters.snapshotBlock);

206:         if (votingToken.getPastVotes(_account, proposal_.parameters.snapshotBlock) == 0) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

7: import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

8: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

9: import {IVotesUpgradeable} from "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";

14: import {PluginSetup, IPluginSetup} from "../../../../framework/plugin/setup/PluginSetup.sol";

15: import {GovernanceERC20} from "../../../../token/ERC20/governance/GovernanceERC20.sol";

16: import {GovernanceWrappedERC20} from "../../../../token/ERC20/governance/GovernanceWrappedERC20.sol";

17: import {IGovernanceWrappedERC20} from "../../../../token/ERC20/governance/IGovernanceWrappedERC20.sol";

67:                 GovernanceERC20.MintSettings(new address[](0), new uint256[](0))

80:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

90:                 (MajorityVotingBase.VotingSettings, TokenSettings, GovernanceERC20.MintSettings)

145:             abi.encodeWithSelector(TokenVoting.initialize.selector, _dao, votingSettings, token)

182:             bytes32 tokenMintPermission = GovernanceERC20(token).MINT_PERMISSION_ID();

201:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

214:         bool isGovernanceERC20 = supportedIds[0] && supportedIds[1] && !supportedIds[2];

216:         permissions = new PermissionLib.MultiTargetPermission[](isGovernanceERC20 ? 4 : 3);

258:     function implementation() external view virtual override returns (address) {

265:     function _getTokenInterfaceIds(address token) private view returns (bool[] memory) {

278:             abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/IMultisig.sol

31:     function canApprove(uint256 _proposalId, address _account) external view returns (bool);

42:     function hasApproved(uint256 _proposalId, address _account) external view returns (bool);

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

5: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

9: import {PluginUUPSUpgradeable} from "../../../core/plugin/PluginUUPSUpgradeable.sol";

11: import {ProposalUpgradeable} from "../../../core/plugin/proposal/ProposalUpgradeable.sol";

119:     event MultisigSettingsUpdated(bool onlyListed, uint16 indexed minApprovals);

143:     ) public view virtual override(PluginUUPSUpgradeable, ProposalUpgradeable) returns (bool) {

175:         uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

216:         if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {

223:             revert DateOutOfBounds({limit: block.timestamp.toUint64(), actual: _startDate});

289:     function canApprove(uint256 _proposalId, address _account) external view returns (bool) {

328:     function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {

365:     function _canApprove(uint256 _proposalId, address _account) internal view returns (bool) {

403:     function _isProposalOpen(Proposal storage proposal_) internal view returns (bool) {

413:     function _updateMultisigSettings(MultisigSettings calldata _multisigSettings) internal {

424:             revert MinApprovalsOutOfBounds({limit: 1, actual: _multisigSettings.minApprovals});

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

8: import {PluginSetup, IPluginSetup} from "../../../framework/plugin/setup/PluginSetup.sol";

27:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

29:         (address[] memory members, Multisig.MultisigSettings memory multisigSettings) = abi.decode(

37:             abi.encodeWithSelector(Multisig.initialize.selector, _dao, members, multisigSettings)

78:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleDistributor.sol

5: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

6: import {ERC20WrapperUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20WrapperUpgradeable.sol";

```

```solidity
File: packages/contracts/src/plugins/token/IMerkleMinter.sol

5: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

7: import {IERC20MintableUpgradeable} from "../../token/ERC20/IERC20MintableUpgradeable.sol";

34:     function changeDistributorBase(IMerkleDistributor _distributorBase) external;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

7: import {SafeERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

8: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

9: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

10: import {MerkleProof} from "@openzeppelin/contracts/utils/cryptography/MerkleProof.sol";

13: import {PluginUUPSUpgradeable} from "../../core/plugin/PluginUUPSUpgradeable.sol";

59:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

74:             revert TokenClaimInvalid({index: _index, to: _to, amount: _amount});

90:         return _verifyBalanceOnTree(_index, _to, _amount, _proof) ? _amount : 0;

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

6: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

7: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

10: import {PluginUUPSUpgradeable} from "../../core/plugin/PluginUUPSUpgradeable.sol";

11: import {IERC20MintableUpgradeable} from "../../token/ERC20/IERC20MintableUpgradeable.sol";

24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

62:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

79:     ) external override auth(MERKLE_MINT_PERMISSION_ID) returns (IMerkleDistributor distributor) {

92:         emit MerkleMinted(distributorAddr, _merkleRoot, _totalAmount, _tree, _context);

```

```solidity
File: packages/contracts/src/plugins/utils/Addresslist.sol

5: import {CheckpointsUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/CheckpointsUpgradeable.sol";

17:     mapping(address => CheckpointsUpgradeable.History) private _addresslistCheckpoints;

47:     function addresslistLengthAtBlock(uint256 _blockNumber) public view virtual returns (uint256) {

72:         _addresslistLengthCheckpoints.push(_uncheckedAdd, _newAddresses.length);

77:     function _removeAddresses(address[] calldata _exitingAddresses) internal virtual {

90:         _addresslistLengthCheckpoints.push(_uncheckedSub, _exitingAddresses.length);

```

```solidity
File: packages/contracts/src/plugins/utils/Ratio.sol

17: function _applyRatioCeiled(uint256 _value, uint256 _ratio) pure returns (uint256 result) {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

5: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

6: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

7: import {IERC20MetadataUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";

8: import {ERC20VotesUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20VotesUpgradeable.sol";

9: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

10: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

11: import {IVotesUpgradeable} from "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";

13: import {DaoAuthorizableUpgradeable} from "../../../core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol";

42:     error MintSettingsArrayLengthMismatch(uint256 receiversArrayLength, uint256 amountsArrayLength);

93:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

106:     function mint(address to, uint256 amount) external override auth(MINT_PERMISSION_ID) {

112:     function _afterTokenTransfer(address from, address to, uint256 amount) internal override {

116:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

5: import {ERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";

6: import {ERC20WrapperUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20WrapperUpgradeable.sol";

7: import {IVotesUpgradeable} from "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";

8: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

9: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

10: import {IERC20MetadataUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";

11: import {ERC20VotesUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20VotesUpgradeable.sol";

12: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

13: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";

15: import {DaoAuthorizableUpgradeable} from "../../../core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol";

38:     constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

59:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

84:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {

92:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {

106:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol

5: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";

6: import {ERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";

7: import {ERC20WrapperUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20WrapperUpgradeable.sol";

13:     function depositFor(address account, uint256 amount) external returns (bool);

18:     function withdrawTo(address account, uint256 amount) external returns (bool);

```

```solidity
File: packages/contracts/src/utils/Proxy.sol

12: function createERC1967Proxy(address _logic, bytes memory _data) returns (address) {

```

#### Recommended Mitigation Steps

Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

### [NC-20] USE OZ MERKLETREE IMPLEMENTATION INSTEAD OF CREATING A NEW ONE

#### Description:

Instead of your own merkle tree lib:

- packages/contracts/src/plugins/token/IMerkleDistributor.sol
- packages/contracts/src/plugins/token/IMerkleMinter.sol
- packages/contracts/src/plugins/token/MerkleDistributor.sol
- packages/contracts/src/plugins/token/MerkleMinter.sol

Use openzeppelin implementation;
https://docs.openzeppelin.com/contracts/4.x/api/utils#MerkleProof

### [NC-21] FUNCTIONS NOT USED INTERNALLY COULD BE MARKED EXTERNAL

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

39:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

44:     function execute() public {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

58:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

63:     function execute() public {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

38:     function supportsInterface(

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

61:     function getVotingToken() public view returns (IVotesUpgradeable) {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

305:     function getProposal(

328:     function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {

333:     function execute(uint256 _proposalId) public {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

83:     function unclaimedBalance(

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

71:     function decimals()

81:     function depositFor(

89:     function withdrawTo(

```

## Low Issues

|      | Issue                                                   |
| ---- | :------------------------------------------------------ |
| L-1  | AVOID INITIAL VALUES IN FIELD DECLARATIONS              |
| L-2  | DOS WITH BLOCK GAS LIMIT                                |
| L-3  | CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE          |
| L-4  | DOS WITH FAILED CALL                                    |
| L-5  | DRAFT OPENZEPPELIN DEPENDENCIES                         |
| L-6  | `INITIALIZE()` FUNCTION CAN BE CALLED BY ANYBODY        |
| L-7  | LACK OF INPUT VALIDATION                                |
| L-8  | UNIFY RETURN CRITERIA                                   |
| L-9  | BLOCK VALUES AS A PROXY FOR TIME                        |
| L-10 | ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS             |
| L-11 | UNSAFE CAST                                             |
| L-12 | UNUSED `RECEIVE()` FUNCTION WILL LOCK ETHER IN CONTRACT |
| L-13 | USE `_SAFEMINT` INSTEAD OF `_MINT`                      |
| L-14 | VOID CONSTRUCTOR                                        |

### [L-1] AVOID INITIAL VALUES IN FIELD DECLARATIONS

#### Description:

Make sure that all initial values are set in an initializer function; otherwise, any upgradeable instances will not have these fields set.

This should be done in initializer functions and not as part of the state variable declarations in which case they won’t be set.

https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#avoid-initial-values-in-field-declarations

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

    bytes32 public constant EXECUTE_PERMISSION_ID =
        keccak256("EXECUTE_PERMISSION");

    bytes32 public constant UPGRADE_DAO_PERMISSION_ID =
        keccak256("UPGRADE_DAO_PERMISSION");

    bytes32 public constant SET_METADATA_PERMISSION_ID =
        keccak256("SET_METADATA_PERMISSION");

    bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
        keccak256("SET_TRUSTED_FORWARDER_PERMISSION");

    bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
        keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");

    bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
        keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");

    uint256 internal constant MAX_ACTIONS = 256;

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

15:    bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

    bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

    bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

   bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
        keccak256("REGISTER_PLUGIN_REPO_PERMISSION");

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

    bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
        keccak256("UPGRADE_REGISTRAR_PERMISSION");

    bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
        keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

    bytes4 internal constant ADMIN_INTERFACE_ID =
        this.initialize.selector ^ this.executeProposal.selector;

    bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
        keccak256("EXECUTE_PROPOSAL_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

    bytes4 internal constant ADDRESSLIST_VOTING_INTERFACE_ID =
        this.initialize.selector ^ this.addAddresses.selector ^ this.removeAddresses.selector;

    bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
        keccak256("UPDATE_ADDRESSES_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

    bytes4 internal constant TOKEN_VOTING_INTERFACE_ID =
        this.initialize.selector ^ this.getVotingToken.selector;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

    bytes4 internal constant MULTISIG_INTERFACE_ID =
        this.initialize.selector ^
            this.updateMultisigSettings.selector ^
            this.createProposal.selector ^
            this.getProposal.selector;

    bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
        keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

    bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

    bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
        keccak256("CHANGE_DISTRIBUTOR_PERMISSION");

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```

### [L-2] DOS WITH BLOCK GAS LIMIT

#### Description:

Programming patterns such as looping over arrays of unknown size may lead to DoS when the gas cost of execution exceeds the block gas limit.

Reference: https://swcregistry.io/docs/SWC-128

This loops could drain all user gas and revert.

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

### [L-3] CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE

#### Description:

The critical procedures should be two step process.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

144:     function setTrustedForwarder(

172:     function setMetadata(

260:     function setSignatureValidator(

298:     function _setMetadata(bytes calldata _metadata) internal {

304:     function _setTrustedForwarder(address _trustedForwarder) internal {

351:     function setDaoURI(

359:     function _setDaoURI(string calldata daoURI_) internal {

```

```solidity
File: packages/contracts/src/framework/utils/TokenFactory.sol

144:     function setupBases() private {

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

100:     function setDefaultResolver(

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

52:     function setNewVariable(uint256 _newVariable) external reinitializer(2) {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

120:     function _setClaimed(uint256 _index) private {

```

#### Recommended Mitigation Steps:

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

### [L-4] DOS WITH FAILED CALL

#### Description:

External calls can fail accidentally or deliberately, which can cause a DoS condition in the contract. To minimize the damage caused by such failures, it is better to isolate each external call into its own transaction that can be initiated by the recipient of the call. This is especially relevant for payments, where it is better to let users withdraw funds rather than push funds to them automatically (this also reduces the chance of problems with the gas limit).

https://swcregistry.io/docs/SWC-113

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

195:        for (uint256 i = 0; i < _actions.length; ) {
            address to = _actions[i].to;
            (bool success, bytes memory response) = to.call{
                value: _actions[i].value
            }(_actions[i].data);

```

### [L-5] DRAFT OPENZEPPELIN DEPENDENCIES

#### Description:

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

6: import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol";

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

5: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

8: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```

### [L-6] `INITIALIZE()` FUNCTION CAN BE CALLED BY ANYBODY

#### Description:

`initialize()` function can be called anybody when the contract is not initialized.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

112:     ) external initializer {

```

```solidity
File: packages/contracts/src/framework/dao/DAORegistry.sol

40:     ) external initializer {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

120:     function initialize(address initialOwner) external initializer {

```

```solidity
File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

41:     function initialize(IDAO _dao, ENSSubdomainRegistrar _subdomainRegistrar) external initializer {

```

```solidity
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

57:     function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

30:     ) external initializer {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

29:     function initialize(IDAO _dao) external initializer {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

38:     ) external initializer {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

40:     ) external initializer {

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

129:     ) external initializer {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

50:     ) external initializer {

```

```solidity
File: packages/contracts/src/plugins/token/MerkleMinter.sol

45:     ) external initializer {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

68:     ) public initializer {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

50:     ) public initializer {

```

### [L-7] LACK OF INPUT VALIDATION

#### Description:

For defence-in-depth purpose, it is recommended to perform additional validation against the amount that the user is attempting to deposit, mint, withdraw and redeem to ensure that the submitted amount is valid.

https://code4rena.com/reports/2022-11-redactedcartel#l-11-lack-of-input-validation

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

106:     function mint(address to, uint256 amount) external override auth(MINT_PERMISSION_ID) {

112:     function _afterTokenTransfer(address from, address to, uint256 amount) internal override {

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

83:         uint256 amount

91:         uint256 amount

101:         uint256 amount

114:         uint256 amount

122:         uint256 amount

```

### [L-8] UNIFY RETURN CRITERIA

#### Description:

In contracts, sometimes the name of the return variable is not defined and sometimes is, unifying the way of writing the code makes the code more uniform and readable.

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

add `{return x}` if you want to return the updated value or else remove `returns(uint)` from the `function(){}` if no value you wanted to return

```solidity
File: packages/contracts/src/core/dao/DAO.sol

127:     ) internal pure override returns (bool) {

156:         returns (address)

167:     ) external view override returns (bool) {

187:         returns (bytes[] memory execResults, uint256 failureMap)

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
File: packages/contracts/src/core/dao/IEIP4824.sol

10:     function daoURI() external view returns (string memory _daoURI);

```

```solidity
File: packages/contracts/src/core/permission/IPermissionCondition.sol

20:     ) external view returns (bool allowed);

```

```solidity
File: packages/contracts/src/core/permission/PermissionManager.sol

203:     ) public view virtual returns (bool) {

302:     ) internal view virtual returns (bool) {

318:         returns (bool allowed) {

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

33:     function _createProposalId() internal returns (uint256 proposalId) {

52:     ) internal virtual returns (uint256 proposalId) {

77:     ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {

```

```solidity
File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

20:     function proposalCount() public view override returns (uint256) {

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

33:     function _createProposalId() internal returns (uint256 proposalId) {

52:     ) internal virtual returns (uint256 proposalId) {

77:     ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {

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
File: packages/contracts/src/framework/dao/DAOFactory.sol

66:     ) external returns (DAO createdDao) {

141:     function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {

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

51:     ) external returns (PluginRepo pluginRepo) {

118:     ) internal returns (PluginRepo pluginRepo) {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

38:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData);

50:     ) external returns (bytes memory initData, PreparedSetupData memory preparedSetupData);

59:     ) external returns (PermissionLib.MultiTargetPermission[] memory permissions);

64:     function implementation() external view returns (address);

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

26:         returns (bytes memory initData, PreparedSetupData memory preparedSetupData)

36:     ) internal returns (address) {

43:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

```

```solidity
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

289:     ) external returns (address plugin, IPluginSetup.PreparedSetupData memory preparedSetupData) {

401:         returns (bytes memory initData, IPluginSetup.PreparedSetupData memory preparedSetupData)

555:     ) external returns (PermissionLib.MultiTargetPermission[] memory permissions) {

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

36:         returns (address plugin, PreparedSetupData memory preparedSetupData)

103:     ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {

137:     function implementation() external view virtual override returns (address) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

58:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

```

```solidity
File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

37:         returns (address plugin, PreparedSetupData memory preparedSetupData)

109:         returns (bytes memory initData, PreparedSetupData memory preparedSetupData)

143:     ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {

177:     function implementation() external view virtual override returns (address) {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

40:     ) public view override(PluginCloneable, ProposalUpgradeable) returns (bool) {

48:     function isMember(address _account) external view returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/admin/AdminSetup.sol

35:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

79:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

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

442:     ) external virtual returns (uint256 proposalId);

477:     ) internal view virtual returns (bool);

483:     function _canExecute(uint256 _proposalId) internal view virtual returns (bool) {

515:     function _isProposalOpen(Proposal storage proposal_) internal view virtual returns (bool) {

567:     ) internal view virtual returns (uint64 startDate, uint64 endDate) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

48:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

78:     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {

91:     ) external override returns (uint256 proposalId) {

143:     function isMember(address _account) external view returns (bool) {

195:     ) internal view override returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

28:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

90:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

129:     function implementation() external view returns (address) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

51:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

61:     function getVotingToken() public view returns (IVotesUpgradeable) {

66:     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {

79:     ) external override returns (uint256 proposalId) {

137:     function isMember(address _account) external view returns (bool) {

192:     ) internal view override returns (bool) {

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

80:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

201:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

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

213:     ) external returns (uint256 proposalId) {

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

27:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

78:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {

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

79:     ) external override auth(MERKLE_MINT_PERMISSION_ID) returns (IMerkleDistributor distributor) {

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

### [L-9] IN MERKLEDISTRIBUTOR.SOL THE SAFETRANSFER FUNCTION DOES NOT CHECK FOR POTENTIALLY SELF-DESTROYED TOKENS

#### Description:

If a pair gets created and after a while one of the tokens gets self-destroyed (maybe because of a bug) then `safeTransfer` would still succeed. It’s probably a good idea to check if the contract still exists by checking the bytecode length.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/plugins/token/MerkleDistributor.sol

77:         token.safeTransfer(_to, _amount);

```

### [L-9] BLOCK VALUES AS A PROXY FOR TIME

#### Description:

Contracts often need access to time values to perform certain types of functionality. Values such as block.timestamp, and block.number can give you a sense of the current time or a time delta, however, they are not safe to use for most purposes.

In the case of block.timestamp, developers often attempt to use it to trigger time-dependent events. As Ethereum is decentralized, nodes can synchronize time only to some degree. Moreover, malicious miners can alter the timestamp of their blocks, especially if they can gain advantages by doing so. However, miners cant set a timestamp smaller than the previous one (otherwise the block will be rejected), nor can they set the timestamp too far ahead in the future. Taking all of the above into consideration, developers cant rely on the preciseness of the provided timestamp.

As for block.number, considering the block time on Ethereum is generally about 14 seconds, it`s possible to predict the time delta between blocks. However, block times are not constant and are subject to change for a variety of reasons, e.g. fork reorganisations and the difficulty bomb. Due to variable block times, block.number should also not be relied on for precise calculations of time.

Reference: https://swcregistry.io/docs/SWC-116

Reference: (https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/timestamp-dependence.md)

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/plugins/governance/admin/Admin.sol

67:         uint64 currentTimestamp64 = block.timestamp.toUint64();

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

516:         uint64 currentTime = block.timestamp.toUint64();

568:         uint64 currentTimestamp = block.timestamp.toUint64();

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

94:             snapshotBlock = block.number.toUint64() - 1;

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

82:             snapshotBlock = block.number - 1;

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

214:         uint64 snapshotBlock = block.number.toUint64() - 1;

221:             _startDate = block.timestamp.toUint64();

222:         } else if (_startDate < block.timestamp.toUint64()) {

223:             revert DateOutOfBounds({limit: block.timestamp.toUint64(), actual: _startDate});

404:         uint64 currentTimestamp64 = block.timestamp.toUint64();

```

#### Recommended Mitigation Steps

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

### [L-10] ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS

#### Description:

Low-level calls `call`/`delegatecall`/`staticcall` return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.

https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-callsn

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

197:             (bool success, bytes memory response) = to.call{

```

```solidity
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

277:         (bool success, bytes memory data) = token.staticcall(

```

#### Recommended Mitigation Steps:

In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

### [L-11] UNSAFE CAST

#### Description:

Keep in mind that the version of solidity used, despite being greater than 0.8, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

203:                 if (!hasBit(_allowFailureMap, uint8(i))) {

209:                 failureMap = flipBit(failureMap, uint8(i));

```

```solidity
File: packages/contracts/src/framework/utils/RegistryUtils.sol

17:         uint8 char = uint8(nameBytes[i]);

```

```solidity
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

175:         uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

414:         uint16 addresslistLength_ = uint16(addresslistLength());

```

#### Recommended Mitigation Steps:

Use a safeCast from Open Zeppelin or increase the type length.

### [L-12] UNUSED `RECEIVE()` FUNCTION WILL LOCK ETHER IN CONTRACT

#### Description:

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/dao/DAO.sol

284:     receive() external payable {

```

#### Recommended Mitigation Steps:

The function should call another function, otherwise it should revert

### [L-13] USE `_SAFEMINT` INSTEAD OF `_MINT`

#### Description:

According to openzepplin’s ERC721, the use of `_mint` is discouraged, use `safeMint` whenever possible.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-mint-address-uint256-

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

82:             _mint(_mintSettings.receivers[i], _mintSettings.amounts[i]);

107:         _mint(to, amount);

```

```solidity
File: packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol

112:     function _mint(

116:         super._mint(to, amount);

```

#### Recommended Mitigation Steps:

Use `_safeMint` whenever possible instead of `_mint`

### [L-14] VOID CONSTRUCTOR

#### Description:

Calls to base contract constructors that are unimplemented leads to misplaced assumptions. Check if the constructor is implemented or remove call if not.

https://github.com/crytic/slither/wiki/Detector-Documentation#void-constructor

#### **Proof Of Concept**

```solidity
File: packages/contracts/src/core/plugin/Plugin.sol

17:     constructor(IDAO _dao) DaoAuthorizable(_dao) {}

```

#### Recommendation

Remove the constructor call.
