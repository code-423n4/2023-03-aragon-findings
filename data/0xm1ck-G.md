| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Use `storage` instead of `memory` for structs/arrays | 31 |
| [GAS-2](#GAS-2) | Increments can be `unchecked` in for-loops | 2 |
| [GAS-3](#GAS-3) | Pre-increments and pre-decrements are cheaper than post-increments and post-decrements | 1 |
| [GAS-4](#GAS-4) | Use shift Right/Left instead of division/multiplication if possible | 2 |
### <a name="GAS-1"></a>[GAS-1] Use `storage` instead of `memory` for structs/arrays
Using `memory` copies the struct or array in memory. Use `storage` to save the location in storage and have cheaper reads:

*Instances (31)*:
```solidity
File: src/core/dao/DAO.sol

187:                 _actions[i].data

```

```solidity
File: src/core/permission/PermissionManager.sol

152: 

172: 

```

```solidity
File: src/framework/dao/DAOFactory.sol

160: 

```

```solidity
File: src/framework/plugin/repo/PluginRepo.sol

168:         bytes32 _tagHash = tagHash(tag);

```

```solidity
File: src/framework/plugin/repo/PluginRepoFactory.sol

69: 

```

```solidity
File: src/framework/plugin/setup/PluginSetupProcessor.sol

299:             _params.pluginSetupRef.versionTag

434:             _params.currentVersionTag

438:             _params.newVersionTag

524:             _params.pluginSetupRef.versionTag

573:             _params.pluginSetupRef.versionTag

```

```solidity
File: src/framework/utils/TokenFactory.sol

89:                 abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))

```

```solidity
File: src/plugins/counter-v1/CounterV1PluginSetup.sol

48:             bytes4(keccak256("initialize(address,address,uint256)")),

56:                 _multiplyHelper == address(0) ? 3 : 2

59: 

```

```solidity
File: src/plugins/counter-v2/CounterV2PluginSetup.sol

49:             bytes4(keccak256("initialize(address,address,uint256)")),

57:                 _multiplyHelper == address(0) ? 3 : 2

60: 

123:         permissions[0] = PermissionLib.MultiTargetPermission(

133:         activeHelpers[0] = _payload.currentHelpers[0];

```

```solidity
File: src/plugins/governance/admin/AdminSetup.sol

52: 

```

```solidity
File: src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

31:             .decode(_data, (MajorityVotingBase.VotingSettings, address[]));

47: 

```

```solidity
File: src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

97: 

109: 

151:                 tokenSettings.addr != address(0) ? 3 : 4

213: 

267:         interfaceIds[0] = type(IERC20Upgradeable).interfaceId;

278:             abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))

```

```solidity
File: src/plugins/governance/multisig/MultisigSetup.sol

30:             _data,

43: 

```

### <a name="GAS-2"></a>[GAS-2] Increments can be `unchecked` in for-loops

*Instances (2)*:
```solidity
File: src/framework/dao/DAOFactory.sol

95:         for (uint256 i; i < _pluginSettings.length; ++i) {

```

```solidity
File: src/framework/utils/RegistryUtils.sol

16:     for (uint256 i; i < nameLength; i++) {

```

### <a name="GAS-3"></a>[GAS-3] Pre-increments and pre-decrements are cheaper than post-increments and post-decrements
*Saves 5 gas per iteration*

*Instances (1)*:
```solidity
File: src/framework/utils/RegistryUtils.sol

16:     for (uint256 i; i < nameLength; i++) {

```

### <a name="GAS-4"></a>[GAS-4] Use shift Right/Left instead of division/multiplication if possible
Shifting left by N is like multiplying by 2^N and shifting right by N is like dividing by 2^N

*Instances (2)*:
```solidity
File: src/plugins/token/MerkleDistributor.sol

112:         uint256 claimedBit_index = _index % 256;

122:         uint256 claimedBit_index = _index % 256;

```