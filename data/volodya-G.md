| Number | Optimization Details                                                                                      | Context |
| :----: | :-------------------------------------------------------------------------------------------------------- | :-----: |
| [G-01] | ++i/i++ should be `unchecked{++i}` when it is not possible for them to overflow, in for- and while-loops` |    2    |
| [G-02] | Setting the constructor to payable payable                                                                |   21    |
| [G-03] | Redundant variables                                                                                       |   4    |
| [G-04] | Reorder the `if` statements for `revert` to have the less gas consuming before the expensive one          |   4   |
| [G-05] | use nested if instead of `&&`                                                 |   12    |
| [G-06] | Empty blocks should be removed or emit something         |   2   |
| [G-07] | Using unchecked blocks to save gas |   1   |
| [G-08] | Caching global variables is more expensive than using the actual variable(use msg.sender instead of caching it |   2   |
| [G-09] | Optimize names to save gas                                                           |    All files    |

Total 48 issues
in addition to https://gist.github.com/Picodes/16984274f6ad7b83b7a59f8b33cee6a6

### [G-01] `++i`/`i++` should be `unchecked{++i}` when it is not possible for them to overflow, in for- and while-loops`

2 results - 2 files:
Refactor to `unchecked{++i}` like its done in most part of the project.

```solidity
src/framework/utils/RegistryUtils.sol:16
    for (uint256 i; i < nameLength; i++) {
```

[src/framework/utils/RegistryUtils.sol:16](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L16)

```solidity
src/framework/dao/DAOFactory.sol:95
        for (uint256 i; i < _pluginSettings.length; ++i) {
```

[src/framework/dao/DAOFactory.sol:95](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L95)

### [G-02] Setting the constructor to payable

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.

Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

**Context:**
21 results - 21 files:

```solidity
src/token/ERC20/governance/GovernanceERC20.sol:49
    constructor(
```

[src/token/ERC20/governance/GovernanceERC20.sol:49](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L49)

```solidity
src/core/dao/DAO.sol:92
    constructor(
```

[src/core/dao/DAO.sol:92](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L92)

```solidity
src/core/plugin/Plugin.sol:17
    constructor(IDAO _dao) DaoAuthorizable(_dao) {}
```

[src/core/plugin/Plugin.sol:17](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/plugin/Plugin.sol#L17)

```solidity
src/core/plugin/PluginCloneable.sol:16
    constructor() {
```

[src/core/plugin/PluginCloneable.sol:16](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/plugin/PluginCloneable.sol#L16)

```solidity
src/core/plugin/PluginUUPSUpgradeable.sol:25
    constructor() {
```

[src/core/plugin/PluginUUPSUpgradeable.sol:25](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25)

```solidity
src/core/plugin/dao-authorizable/DaoAuthorizable.sol:19
    constructor(IDAO _dao) {
```

[src/core/plugin/dao-authorizable/DaoAuthorizable.sol:19](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19)

```solidity
src/framework/dao/DAOFactory.sol:53
    constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
```

[src/framework/dao/DAOFactory.sol:53](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L53)

```solidity
src/framework/dao/DAORegistry.sol:30
    constructor() {
```

[src/framework/dao/DAORegistry.sol:30](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAORegistry.sol#L30)

```solidity
src/framework/plugin/repo/PluginRepo.sol:112
    constructor() {
```

[src/framework/plugin/repo/PluginRepo.sol:112](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L112)

```solidity
src/framework/plugin/repo/PluginRepoFactory.sol:22
    constructor(PluginRepoRegistry _pluginRepoRegistry) {
```

[src/framework/plugin/repo/PluginRepoFactory.sol:22](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22)

```solidity
src/framework/plugin/repo/PluginRepoRegistry.sol:34
    constructor() {
```

[src/framework/plugin/repo/PluginRepoRegistry.sol:34](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L34)

```solidity
src/framework/utils/TokenFactory.sol:68
    constructor() {
```

[src/framework/utils/TokenFactory.sol:68](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/TokenFactory.sol#L68)

```solidity
src/framework/utils/ens/ENSSubdomainRegistrar.sol:45
    constructor() {
```

[src/framework/utils/ens/ENSSubdomainRegistrar.sol:45](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L45)

```solidity
src/plugins/counter-example/v1/CounterV1PluginSetup.sol:23
    constructor() {
```

[src/plugins/counter-example/v1/CounterV1PluginSetup.sol:23](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L23)

```solidity
src/plugins/counter-example/v2/CounterV2PluginSetup.sol:24
    constructor(MultiplyHelper _helper) {
```

[src/plugins/counter-example/v2/CounterV2PluginSetup.sol:24](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L24)

```solidity
src/plugins/governance/admin/AdminSetup.sol:27
    constructor() {
```

[src/plugins/governance/admin/AdminSetup.sol:27](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L27)

```solidity
src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol:20
    constructor() {
```

[src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol:20](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L20)

```solidity
src/plugins/governance/majority-voting/token/TokenVotingSetup.sol:61
    constructor() {
```

[src/plugins/governance/majority-voting/token/TokenVotingSetup.sol:61](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61)

```solidity
src/plugins/governance/multisig/MultisigSetup.sol:19
    constructor() {
```

[src/plugins/governance/multisig/MultisigSetup.sol:19](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L19)

```solidity
src/token/ERC20/governance/GovernanceWrappedERC20.sol:38
    constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {
```

[src/token/ERC20/governance/GovernanceWrappedERC20.sol:38](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L38)

### [G-03] Redundant variables

**Context:**
4 results - 3 files:

```diff
src/core/utils/BitMap.sol:8
function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {
-    uint256 bitValue = bitmap & (1 << index);
-    return bitValue > 0;
+    return (bitmap & (1 << index)) > 0;
}
```


[src/core/utils/BitMap.sol:8](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/utils/BitMap.sol#L8)

```diff
src/framework/dao/DAORegistry.sol:64
function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {
-       bytes32 labelhash = keccak256(bytes(subdomain));

-       subdomainRegistrar.registerSubnode(labelhash, daoAddr);
+       subdomainRegistrar.registerSubnode(keccak256(bytes(subdomain)), daoAddr);
}
````

[src/framework/dao/DAORegistry.sol:64](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAORegistry.sol#L64)

```diff
src/framework/plugin/setup/PluginSetupProcessor.sol:308
-308        bytes32 pluginInstallationId = _getPluginInstallationId(_dao, plugin);

+318        PluginState storage pluginState = states[_getPluginInstallationId(_dao, plugin)];
```

```diff
src/framework/plugin/setup/PluginSetupProcessor.sol:556
-556        bytes32 pluginInstallationId = _getPluginInstallationId(_dao, _params.setupPayload.plugin);

+558        PluginState storage pluginState = states[_getPluginInstallationId(_dao, _params.setupPayload.plugin)];
```

```diff
src/framework/plugin/setup/PluginSetupProcessor.sol:413
-413                bytes32 pluginInstallationId = _getPluginInstallationId(_dao, _params.setupPayload.plugin);

+415                PluginState storage pluginState = states[_getPluginInstallationId(_dao, _params.setupPayload.plugin)];

```

[src/framework/plugin/setup/PluginSetupProcessor.sol:308](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L308)

### [G-04] Reorder the `if` statements for `revert` to have the less gas consuming before the expensive one

Its cheaper to check `_release == 0`, `_release - latestRelease > 1` than `!_pluginSetup.supportsInterface(type(IPluginSetup).interfaceId)`
So on average it will take less gas if we will reorder. Move `latestRelease = _release` after `revert` for the same reason.

**Context:**
4 results - 2 files:

Suggestion code:

```solidity
src/framework/plugin/repo/PluginRepo.sol:128

    function createVersion(
        uint8 _release,
        address _pluginSetup,
        bytes calldata _buildMetadata,
        bytes calldata _releaseMetadata
    ) external auth(MAINTAINER_PERMISSION_ID) {

        if (_release == 0) {
            revert ReleaseZeroNotAllowed();
        }

        // Check that the release number is not incremented by more than one
        if (_release - latestRelease > 1) {
            revert InvalidReleaseIncrement({latestRelease: latestRelease, newRelease: _release});
        }


        if (_release > latestRelease) {

            if (_releaseMetadata.length == 0) {
                revert EmptyReleaseMetadata();
            }
            latestRelease = _release;
        }

        if (!_pluginSetup.supportsInterface(type(IPluginSetup).interfaceId)) {
            revert InvalidPluginSetupInterface();
        }

```

Same with `updateReleaseMetadata`

```solidity
src/framework/plugin/repo/PluginRepo.sol:188
    function updateReleaseMetadata(
        uint8 _release,
        bytes calldata _releaseMetadata
    ) external auth(MAINTAINER_PERMISSION_ID) {
        if (_release == 0) {
            revert ReleaseZeroNotAllowed();
        }
// Remix: execution cost	694 gas
        if (_releaseMetadata.length == 0) {
            revert EmptyReleaseMetadata();
        }

// Remix: execution cost	2854 gas
        if (_release > latestRelease) {
            revert ReleaseDoesNotExist();
        }


        emit ReleaseMetadataUpdated(_release, _releaseMetadata);
    }


```

[src/framework/plugin/repo/PluginRepo.sol:128](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L128)

Suggestion code:

```solidity
src/framework/plugin/setup/PluginSetupProcessor.sol:308
        bytes32 pluginInstallationId = _getPluginInstallationId(_dao, plugin);

        PluginState storage pluginState = states[pluginInstallationId];

        // Check if this plugin is already installed.
        if (pluginState.currentAppliedSetupId != bytes32(0)) {
            revert PluginAlreadyInstalled();
        }
        bytes32 preparedSetupId = _getPreparedSetupId(
            _params.pluginSetupRef,
            hashPermissions(preparedSetupData.permissions),
            hashHelpers(preparedSetupData.helpers),
            bytes(""),
            PreparationType.Installation
        );


```

Same, suggestion code:

```solidity
src/framework/plugin/setup/PluginSetupProcessor.sol:355
        PluginState storage pluginState = states[pluginInstallationId];

        // Check if this plugin is already installed.
        if (pluginState.currentAppliedSetupId != bytes32(0)) {
            revert PluginAlreadyInstalled();
        }
        bytes32 preparedSetupId = _getPreparedSetupId(
            _params.pluginSetupRef,
            hashPermissions(_params.permissions),
            _params.helpersHash,
            bytes(""),
            PreparationType.Installation
        );


```

[src/framework/plugin/setup/PluginSetupProcessor.sol:310](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L308)


### [G-05] use nested if instead of `&&`

~12 results - 9 files:
Search && in the whole project

E.x.:execution cost	9616 gas vs 9926 gas for this string `this-is-my-super-valid-name` on Remix
Suggestion
```solidity
src/framework/utils/RegistryUtils.sol:13
function isSubdomainValid(string calldata subDomain) pure returns (bool) {
    bytes calldata nameBytes = bytes(subDomain);
    uint256 nameLength = nameBytes.length;
    for (uint256 i; i < nameLength; i++) {
        uint8 char = uint8(nameBytes[i]);

        // if char is between a-z
        if (char > 96 ) {
            if (char < 123 ) {
                continue;
            }
        }

        // if char is between 0-9
        if (char > 47 ) {
            if ( char < 58 ) {
                continue;
            }
        }

        // if char is -
        if (char == 45) {
            continue;
        }

        // invalid if one char doesn't work with the rules above
        return false;
    }
    return true;
}
```

[src/framework/utils/RegistryUtils.sol:13](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L13)


### [G-06] Empty blocks should be removed or emit something


The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

2 results - 2 files:

```solidity
src/plugins/counter-example/v1/CounterV1.sol:44
    function execute() public {
        // In order to do this, Count needs permission on the dao (EXEC_ROLE)
        //dao.execute(...)
}
```

[src/plugins/counter-example/v1/CounterV1.sol:44](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L44)


```solidity
src/plugins/counter-example/v2/CounterV2.sol:63
    function execute() public {
        // In order to do this, Count needs permission on the dao (EXEC_ROLE)
        //dao.execute(...)
    }
```

[src/plugins/counter-example/v2/CounterV2.sol:63](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L63)


### [G-07] Using unchecked blocks to save gas

1 results - 1 files:


```solidity
src/plugins/governance/multisig/Multisig.sol:214
        uint64 snapshotBlock = block.number.toUint64() - 1;
```

[src/plugins/governance/multisig/Multisig.sol:214](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L214)


### [G-08] Caching global variables is more expensive than using the actual variable(use msg.sender instead of caching it
The reason is the following: The CALLER operation which is reading the msg.sender global variable costs 2.
While MLOAD/MSTORE operations which are memory operations (storage where local variables are stored) costs 3.

2 results - 2 files:


Suggestion code
```diff
src/plugins/governance/majority-voting/MajorityVotingBase.sol:265
    function vote(
        uint256 _proposalId,
        VoteOption _voteOption,
        bool _tryEarlyExecution
    ) public virtual {
-        address account = _msgSender();

+        if (!_canVote(_proposalId, msg.sender, _voteOption)) {
            revert VoteCastForbidden({
                proposalId: _proposalId,
+               account: msg.sender,
                voteOption: _voteOption
            });
        }
+       _vote(_proposalId, _voteOption, msg.sender, _tryEarlyExecution);
    }
```

[src/plugins/governance/majority-voting/MajorityVotingBase.sol:265](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L265)

Same here
```solidity
src/plugins/governance/multisig/Multisig.sol:266
        address approver = _msgSender();
```

[src/plugins/governance/multisig/Multisig.sol:266](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L266)

### [G-09] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

