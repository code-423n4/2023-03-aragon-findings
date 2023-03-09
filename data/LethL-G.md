## Gas Optimizations
|  | Issue | Instances | Total Gas Saved|
|---|---|---|---|
| Gas-1 | Constructors can be marked payable | All Contracts | - |
| Gas-2 | Structs can be packed into fewer storage slot | 2 | 40000 |
| Gas-3 | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 3 | - |
| Gas-4 | Using `storage` instead of `memory` for structs/arrays | 9 | 18900 |
| Gas-5 | Using `++x` instead of `x += 1`| 1 | 2 |

Total: 15 instances over 5 issues with 68,902 gas saved

### [Gas-1] Constructors can be marked payable
Payable functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided. A constructor can safely be marked as payable, since only the deployer would be able to pass funds.

### [Gas-2] Structs can be packed into fewer storage slot
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

Instances(2):
```
File: packages/contracts/src/framework/utils/TokenFactory.sol

62:    struct MintConfig {
63:        address[] receivers;
64:        uint256[] amounts;
65:    }
```

```
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

166:    struct Tally {
167:        uint256 abstain;
168:        uint256 yes;
169:        uint256 no;
170:    }
```

### [Gas-3] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
Saves 30-40 gas per loop. [Source](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

Instances(3):

```
File: packages/contracts/src/framework/dao/DAOFactory.sol

95:        for (uint256 i; i < _pluginSettings.length; ++i) {
```

```
File: packages/contracts/src/framework/utils/RegistryUtils.sol

16:    for (uint256 i; i < nameLength; i++) {
```

```
File: packages/contracts/src/plugins/utils/Ratio.sol

28:        ++result;
```

### [Gas-4] Using `storage` instead of `memory` for structs/arrays
Use the `storage` instead of `memory` when declaring local variables to minimize gas consumption when fetching data from storage. Assigning the data to a memory variable reads all fields of the struct/array from storage, which incurs a 2100 gas for each field.
Instances(9):
```
File: packages/contracts/src/core/permission/PermissionManager.sol

151:            PermissionLib.SingleTargetPermission memory item = items[i];

171:            PermissionLib.MultiTargetPermission memory item = _items[i];
```

```
File: packages/contracts/src/framework/dao/DAOFactory.sol

99:                IPluginSetup.PreparedSetupData memory preparedSetupData
```

```
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

167:        Tag memory tag = Tag(_release, build);
```

```
File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

298:        PluginRepo.Version memory version = pluginSetupRepo.getVersion(

433:        PluginRepo.Version memory currentVersion = _params.pluginSetupRepo.getVersion(

437:        PluginRepo.Version memory newVersion = _params.pluginSetupRepo.getVersion(

523:        PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(

572:        PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(
```

### [Gas-5] Using `++x` instead of `x += 1`
Saves 2 gas per instance

Instances(1):
```
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

276:            proposal_.approvals += 1;
```