### Issues Template
| Letter | Name | Description |
|:--:|:-------:|:-------:|
| L  | Low risk | Potential risk |
| NC |  Non-critical | Non risky findings |
| R  | Refactor | Code changes |
| O | Ordinary | Commonly found issues |

| Total Found Issues | 6 |
|:--:|:--:|

### Non-Critical Template
| Count | Title | Instances |
|:--:|:-------|:--:|
| [N-01] | Lack of zero-address checks in the constructor | 6 |
| [N-02] | Remove functions not used | 1 |
| [N-03] | Omission of important parameters in events emitted | 3 |
| [N-04] | Implement checks for input validation for setters | 6 |

| Total Non-Critical Issues | 4 |
|:--:|:--:|

### Ordinary Issues Template
| Count | Explanation | Instances |
|:--:|:-------|:--:|
| [O-01] | Function naming suggestion of internal and private functions starts with _ | 7 |
| [O-02] | Solidity compiler optimizations can be problematic | - |

| Total Ordinary Issues | 2 |
|:--:|:--:|


### [N-01] Lack of zero-address checks in the constructor
Context:
```solidity
6 results - 4 files

/DAOFactory.sol
53:    constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
54:        daoRegistry = _registry;
55:        pluginSetupProcessor = _pluginSetupProcessor;

/PluginRepoFactory.sol
22:    constructor(PluginRepoRegistry _pluginRepoRegistry) {
23:        pluginRepoRegistry = _pluginRepoRegistry;


/PluginSetupProcessor.sol
277:    constructor(PluginRepoRegistry _repoRegistry) {
278:        repoRegistry = _repoRegistry;
279:    }

/CounterV2PluginSetup.sol
24:    constructor(MultiplyHelper _helper) {
25:        multiplyHelperBase = _helper;


/DaoAuthorizable.sol
19:    constructor(IDAO _dao) {
20:        dao_ = _dao;
21:    }
```

Description:
Zero-address check should be implemented in constructors to avoid the risk of setting `address(0)` at deployment time for immutable address variables.

Reccomendation:
Add a zero-address check for the immutable variables for the instances above using custom errors.

### [N-02] Remove functions not used
```solidity
1 result - 1 file
/TokenFactory.sol
8: function _uncheckedIncrement(uint256 i) pure returns (uint256)
```

Recommendation:
Consider removing functions not used to save gas on deployment

### [N-03] Omission of important parameters in events emitted
```solidity
3 results - 3 files

/MajorityVotingBase.sol
526:    function _updateVotingSettings(VotingSettings calldata _votingSettings) internal virtual
550:        emit VotingSettingsUpdated({
551:            votingMode: _votingSettings.votingMode,
552:            supportThreshold: _votingSettings.supportThreshold,
553:            minParticipation: _votingSettings.minParticipation,
554:            minDuration: _votingSettings.minDuration,
555:            minProposerVotingPower: _votingSettings.minProposerVotingPower
556:        });

/Multisig.sol
413:    function _updateMultisigSettings(MultisigSettings calldata _multisigSettings) internal
429:        emit MultisigSettingsUpdated({
430:            onlyListed: _multisigSettings.onlyListed,
431:            minApprovals: _multisigSettings.minApprovals
432:        });

/PluginRepo.sol
187:    function updateReleaseMetadata
203:        emit ReleaseMetadataUpdated(_release, _releaseMetadata);
```

Description:
Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

Recommendation:
The events should include the new value and old value where possible

## [N-04] Implement checks for input validation for setters
```solidity
6 results - 5 files

/DAOFactory.sol
156:    function _setDAOPermissions(DAO _dao) internal

/PluginRepoFactory.sol
65:     function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal

/CounterV2.sol
52:    function setNewVariable(uint256 _newVariable) external reinitializer(2) {
53:        newVariable = _newVariable;
54:    }

/DAO.sol
283:    function _setTrustedForwarder(address _trustedForwarder) internal {
284:        trustedForwarder = _trustedForwarder;
285:
286:        emit TrustedForwarderSet(_trustedForwarder);
287:    }

239:    function setSignatureValidator(
240:        address _signatureValidator
241:    ) external override auth(SET_SIGNATURE_VALIDATOR_PERMISSION_ID) {
242:        signatureValidator = IERC1271(_signatureValidator);
243:
244:        emit SignatureValidatorSet({signatureValidator: _signatureValidator});
245:    }

/DaoAuthorizableUpgradeable.sol
20:    function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {
21:        dao_ = _dao;
22:    }
```

Recommendation:
zero-address checks can be added in the functions with address inputs to ensure the addresses set aren't address(0).

In the `setNewVariable` function, a check can be made to ensure the `_newVariable` is set as non-zero.

### [O-01] Function naming suggestion of internal and private functions starts with _

```solidity
7 results - 6 files

/TokenFactory.sol
144: function setupBases() private

/PluginRepo.sol
251: function tagHash(Tag memory _tag) internal pure returns (bytes32)

/PluginSetupProcessor.sol
719: function emitPrepareUpdateEvent

/PluginSetup.sol
33: function createERC1967Proxy

/PermissionManager.sol
342: function permissionHash
354: function isPermissionRestrictedForAnyAddr

/DAO.sol
122: function isPermissionRestrictedForAnyAddr
```

Proper use of _ as a function name prefix and a common pattern is to prefix internal and private function names with _. 
internal and private functions: the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [O-02] Solidity compiler optimizations can be problematic
```typescript
/hardhat.config.ts
47: const config: HardhatUserConfig = {
48:   solidity: {
49:     version: '0.8.17',
50:     settings: {
51:       optimizer: {
52:         enabled: true,
53:         runs: 2000,
```

Description
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.
Exploit Scenario:
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.
* * *
Recommendation:
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug. 
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.
		