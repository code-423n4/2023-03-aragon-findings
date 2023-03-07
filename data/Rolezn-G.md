## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `abi.encode()` is less efficient than `abi.encodepacked()` | 6 | 600 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Setting the `constructor` to `payable` | 21 | 273 |
| [GAS&#x2011;3](#GAS&#x2011;3) | Do not calculate constants | 1 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | Don't use `_msgSender() `if not supporting EIP-2771 | 12 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | Empty Blocks Should Be Removed Or Emit Something | 9 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Use `assembly` to write address storage values | 1 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | `internal` functions only called once can be inlined to save gas | 4 | 88 |
| [GAS&#x2011;8](#GAS&#x2011;8) | Optimize names to save gas | 33 | 726 |
| [GAS&#x2011;9](#GAS&#x2011;9) | Public Functions To External | 64 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Save gas with the use of specific import statements | 4 | - |
| [GAS&#x2011;11](#GAS&#x2011;11) | Shorten the array rather than copying to a new one | 5 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It | 3 | - |
| [GAS&#x2011;13](#GAS&#x2011;13) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 35 | - |
| [GAS&#x2011;14](#GAS&#x2011;14) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 2 | 70 |
| [GAS&#x2011;15](#GAS&#x2011;15) | Use solidity version 0.8.19 to gain some gas boost | 62 | 5456 |
| [GAS&#x2011;16](#GAS&#x2011;16) | Using `storage` instead of `memory` saves gas | 2 | 700 |

Total: 255 contexts over 16 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
272: return abi.encode(magicNumber);
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L272

```solidity
33: return keccak256(abi.encode(_dao, _plugin));
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L33

```solidity
33: abi.encode(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L33

```solidity
73: abi.encode(_pluginSetupRef.versionTag, _pluginSetupRef.pluginSetupRepo, _helpersHash)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L73

```solidity
80: return keccak256(abi.encode(_helpers));
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L80

```solidity
89: return keccak256(abi.encode(_permissions));
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L89






### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
92: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L92

```solidity
17: constructor(IDAO _dao) DaoAuthorizable(_dao)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L17

```solidity
16: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L16

```solidity
25: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25

```solidity
19: constructor(IDAO _dao)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19

```solidity
53: constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L53

```solidity
30: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L30

```solidity
112: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L112

```solidity
22: constructor(PluginRepoRegistry _pluginRepoRegistry)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22

```solidity
34: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L34

```solidity
277: constructor(PluginRepoRegistry _repoRegistry)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277

```solidity
68: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L68

```solidity
45: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L45

```solidity
23: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L23

```solidity
24: constructor(MultiplyHelper _helper)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L24

```solidity
27: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L27

```solidity
20: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L20

```solidity
61: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61

```solidity
19: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L19

```solidity
49: constructor(
        IDAO _dao,
        string memory _name,
        string memory _symbol,
        MintSettings memory _mintSettings
    )
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L49

```solidity
38: constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L38





### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```solidity
6: uint256 constant RATIO_BASE = 10 ** 6;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol#L6





### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Don't use `_msgSender()` if not supporting EIP-2771

Use `msg.sender` if the code does not implement <a href="https://eips.ethereum.org/EIPS/eip-2771">EIP-2771 trusted forwarder</a> support

#### <ins>Proof Of Concept</ins>

```solidity
70: _creator: _msgSender(),

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L70

```solidity
270: address account = _msgSender();

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L270

```solidity
97: if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
98: revert ProposalCreationForbidden(_msgSender());
102: _creator: _msgSender(),

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L98

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L102



```solidity
91: if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {
92: revert ProposalCreationForbidden(_msgSender());
96: _creator: _msgSender(),

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L91

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L92

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L96



```solidity
216: if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {
217: revert ProposalCreationForbidden(_msgSender());
231: _creator: _msgSender(),

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L217

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L231



```solidity
266: address approver = _msgSender();

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L266











### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
136: function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L136

```solidity
320: } catch {}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L320

```solidity
63: ) internal virtual override auth(UPGRADE_PLUGIN_PERMISSION_ID) {}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L63

```solidity
259: ) internal virtual override auth(UPGRADE_REPO_PERMISSION_ID) {}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L259

```solidity
27: {}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L27

```solidity
672: {} catch Error(string memory reason) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L672

```solidity
682: try PluginUUPSUpgradeable(_proxy).upgradeTo(_implementation) {} catch Error(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L682

```solidity
56: ) internal virtual override auth(UPGRADE_REGISTRY_PERMISSION_ID) {}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L56

```solidity
76: ) internal virtual override auth(UPGRADE_REGISTRAR_PERMISSION_ID) {}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L76








### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Use `assembly` to write address storage values

#### <ins>Proof Of Concept</ins>

```solidity
333: _daoURI = daoURI_;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L333










### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
122: function isPermissionRestrictedForAnyAddr

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L122


```solidity
212: function _initializePermissionManager

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L212


```solidity
354: function isPermissionRestrictedForAnyAddr

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L354


```solidity
141: function _createDAO

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L141









### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function isGranted(
        address _where,
        address _who,
        bytes32 _permissionId,
        bytes memory _data
    ) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L198

```solidity
function pluginType() public pure override returns (PluginType) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L20

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L27

```solidity
function pluginType() public pure override returns (PluginType) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L27

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L34

```solidity
function pluginType() public pure override returns (PluginType) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L30

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L46

```solidity
function implementation() public view returns (address) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L55

```solidity
function dao() public view returns (IDAO) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L25

```solidity
function dao() public view returns (IDAO) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L26

```solidity
function proposalCount() public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L20

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L27

```solidity
function proposalCount() public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L20

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L27

```solidity
function getLatestVersion(uint8 _release) public view returns (Version memory) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L209

```solidity
function getLatestVersion(address _pluginSetup) public view returns (Version memory) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L209

```solidity
function getVersion(Tag calldata _tag) public view returns (Version memory) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L224

```solidity
function getVersion(bytes32 _tagHash) public view returns (Version memory) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L224

```solidity
function buildCount(uint8 _release) public view returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L244

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L264

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L43

```solidity
function validatePreparedSetupId(
        bytes32 pluginInstallationId,
        bytes32 preparedSetupId
    ) public view {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L650

```solidity
function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L39

```solidity
function execute() public {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L44

```solidity
function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L58

```solidity
function execute() public {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L63

```solidity
function supportsInterface(
        bytes4 _interfaceId
    ) public view override(PluginCloneable, ProposalUpgradeable) returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L38

```solidity
function vote(
        uint256 _proposalId,
        VoteOption _voteOption,
        bool _tryEarlyExecution
    ) public virtual {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L265

```solidity
function execute(uint256 _proposalId) public virtual {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L283

```solidity
function getVoteOption(
        uint256 _proposalId,
        address _voter
    ) public view virtual returns (VoteOption) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L291

```solidity
function canVote(
        uint256 _proposalId,
        address _voter,
        VoteOption _voteOption
    ) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L299

```solidity
function canExecute(uint256 _proposalId) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L308

```solidity
function isSupportThresholdReached(uint256 _proposalId) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L313

```solidity
function isSupportThresholdReachedEarly(
        uint256 _proposalId
    ) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L324

```solidity
function isMinParticipationReached(uint256 _proposalId) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L341

```solidity
function supportThreshold() public view virtual returns (uint32) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L352

```solidity
function minParticipation() public view virtual returns (uint32) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L357

```solidity
function minDuration() public view virtual returns (uint64) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L363

```solidity
function minProposerVotingPower() public view virtual returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L369

```solidity
function votingMode() public view virtual returns (VotingMode) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L375

```solidity
function totalVotingPower(uint256 _blockNumber) public view virtual returns (uint256);
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L382

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L48

```solidity
function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L78

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L51

```solidity
function getVotingToken() public view returns (IVotesUpgradeable) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L61

```solidity
function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L66

```solidity
function supportsInterface(
        bytes4 _interfaceId
    ) public view virtual override(PluginUUPSUpgradeable, ProposalUpgradeable) returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L141

```solidity
function approve(uint256 _proposalId, bool _tryExecution) public {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L265

```solidity
function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L328

```solidity
function execute(uint256 _proposalId) public {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L333

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L59

```solidity
function unclaimedBalance(
        uint256 _index,
        address _to,
        uint256 _amount,
        bytes32[] memory _proof
    ) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L83

```solidity
function isClaimed(uint256 _index) public view override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L110

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L62

```solidity
function isListedAtBlock(
        address _account,
        uint256 _blockNumber
    ) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L30

```solidity
function isListed(address _account) public view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L40

```solidity
function addresslistLengthAtBlock(uint256 _blockNumber) public view virtual returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L47

```solidity
function addresslistLength() public view virtual returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L53

```solidity
function initialize(
        IDAO _dao,
        string memory _name,
        string memory _symbol,
        MintSettings memory _mintSettings
    ) public initializer {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L63

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L93

```solidity
function initialize(
        IERC20Upgradeable _token,
        string memory _name,
        string memory _symbol
    ) public initializer {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L46

```solidity
function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L59

```solidity
function depositFor(
        address account,
        uint256 amount
    ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L81

```solidity
function withdrawTo(
        address account,
        uint256 amount
    ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L89






### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
7: import "./IPermissionCondition.sol";
8: import "./PermissionLib.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L7-L8

```solidity
8: import "./IProposal.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L8

```solidity
8: import "./IProposal.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L8




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Shorten the array rather than copying to a new one

Inline-assembly can be used to shorten the array by changing the length slot, so that the entries don't have to be copied to a new, shorter array

#### <ins>Proof Of Concept</ins>


```solidity
58: address[] memory helpers = new address[](1);

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L58

```solidity
59: address[] memory helpers = new address[](1);

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L59

```solidity
132: address[] memory activeHelpers = new address[](1);

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L132

```solidity
96: address[] memory helpers = new address[](1);

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L96

```solidity
266: bytes4[] memory interfaceIds = new bytes4[](3);

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L266





### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Help The Optimizer By Saving A Storage Variable's Reference Instead Of Repeatedly Fetching It

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

#### <ins>Proof Of Concept</ins>


```solidity
111: Proposal storage proposal_ = proposals[proposalId];
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L111

```solidity
105: Proposal storage proposal_ = proposals[proposalId];
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L105

```solidity
240: Proposal storage proposal_ = proposals[proposalId];
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L240







### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
34: uint8 release;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L34

```solidity
35: uint16 build;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L35

```solidity
65: uint8 public latestRelease;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L65

```solidity
165: uint16 build = ++buildsPerRelease[_release];
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L165

```solidity
210: uint16 latestBuild = uint16(buildsPerRelease[_release]);
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L210

```solidity
17: uint8 char = uint8(nameBytes[i]);
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L17

```solidity
67: uint64 currentTimestamp64 = block.timestamp.toUint64();
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L67

```solidity
155: uint32 supportThreshold;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L155

```solidity
125: uint32 minParticipation;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L125

```solidity
126: uint64 minDuration;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L126

```solidity
156: uint64 startDate;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L156

```solidity
157: uint64 endDate;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L157

```solidity
158: uint64 snapshotBlock;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L158

```solidity
516: uint64 currentTime = block.timestamp.toUint64();
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L516

```solidity
568: uint64 currentTimestamp = block.timestamp.toUint64();
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L568

```solidity
580: uint64 earliestEndDate = startDate + votingSettings.minDuration;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580

```solidity
92: uint64 snapshotBlock;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L92

```solidity
267: interfaceIds[0] = type(IERC20Upgradeable).interfaceId;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L267

```solidity
268: interfaceIds[1] = type(IVotesUpgradeable).interfaceId;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L268

```solidity
269: interfaceIds[2] = type(IGovernanceWrappedERC20).interfaceId;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L269

```solidity
36: uint16 approvals;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L36

```solidity
60: uint16 minApprovals;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L60

```solidity
50: uint64 snapshotBlock;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L50

```solidity
51: uint64 startDate;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L51

```solidity
52: uint64 endDate;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L52

```solidity
175: uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L175

```solidity
214: uint64 snapshotBlock = block.number.toUint64() - 1;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L214

```solidity
404: uint64 currentTimestamp64 = block.timestamp.toUint64();
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L404

```solidity
414: uint16 addresslistLength_ = uint16(addresslistLength());
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L414






### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
95: for (uint256 i; i < _pluginSettings.length; ++i) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95

```solidity
16: for (uint256 i; i < nameLength; i++) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16







### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> Use solidity version 0.8.19 to gain some gas boost

Upgrade to the latest solidity version 0.8.19 to get additional gas savings.
See latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IEIP4824.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/IPermissionCondition.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/IPlugin.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/membership/IMembership.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol#L5

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L5

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/IERC20MintableUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/UncheckedMath.sol#L3





### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Using `storage` instead of `memory` saves gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another `memory` array/struct

#### <ins>Proof Of Concept</ins>


```solidity
151: PermissionLib.SingleTargetPermission memory item = items[i];

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L151

```solidity
171: PermissionLib.MultiTargetPermission memory item = _items[i];

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L171



