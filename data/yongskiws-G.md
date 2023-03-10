## Summary
### Gas Optimizations Issues List
| Number |Issues Details|Instance|
|:--:|:-------|:--:|
|[G-01]| Using unchecked blocks to save gas | 3 |
|[G-02]| Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration) | 2 |
|[G-03]| Using storage instead of memory for structs/arrays saves gas | 12 |
|[G-04]| Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate | 9 |
|[G-05]| Using private rather than public for constants, saves gas | ALL CONTRACT |
|[G-06]| Using immutable for uint & address for save gas | 3 |
|[G-07]| internal functions not called by the contract should be removed to save deployment gas | 2 |
|[G-08]| x = x + y is cheaper than x += y; | 1 |
|[G-09]| Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | ALL CONTRACT |
|[G-10]| Using > 0 costs more gas than != 0 when used on a uint in a require() statement | 6 |
|[G-11]| Using calldata instead of memory for read-only arguments in external functions saves gas | 3 |
|[G-12]| keccak256() should only need to be called on a specific string literal once | 18 |

## [G-01] Using unchecked blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
https://github.com/ethereum/solidity/issues/10695

There are 3 instances of this issue:

The above should be modified to:


``` solidity
plugins\counter-example\MultiplyHelper.sol
16:     function multiply(
17:         uint256 _a,
18:         uint256 _b
19:     ) external view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {
20:         return _a * _b;
21:     }


plugins\governance\majority-voting\MajorityVotingBase.sol
313:     function isSupportThresholdReached(uint256 _proposalId) public view virtual returns (bool) {
314:         Proposal storage proposal_ = proposals[_proposalId];
315: 
316:         // The code below implements the formula of the support criterion explained in the top of this file.
317:         // `(1 - supportThreshold) * N_yes > supportThreshold *  N_no`
318:         return
319:             (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >
320:             proposal_.parameters.supportThreshold * proposal_.tally.no;
321:     }
322: 


plugins\governance\majority-voting\MajorityVotingBase.sol
324:     function isSupportThresholdReachedEarly(
325:         uint256 _proposalId
326:     ) public view virtual returns (bool) {
327:         Proposal storage proposal_ = proposals[_proposalId];
328: 
329:         uint256 noVotesWorstCase = totalVotingPower(proposal_.parameters.snapshotBlock) -
330:             proposal_.tally.yes -
331:             proposal_.tally.abstain;
332: 
333:         // The code below implements the formula of the early execution support criterion explained in the top of this file.
334:         // `(1 - supportThreshold) * N_yes > supportThreshold *  N_no,worst-case`
335:         return
336:             (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >
337:             proposal_.parameters.supportThreshold * noVotesWorstCase;
338:     }
339: 


plugins\governance\majority-voting\MajorityVotingBase.sol
341:     function isMinParticipationReached(uint256 _proposalId) public view virtual returns (bool) {
342:         Proposal storage proposal_ = proposals[_proposalId];
343: 
344:         // The code below implements the formula of the participation criterion explained in the top of this file.
345:         // `N_yes + N_no + N_abstain >= minVotingPower = minParticipation * N_total`
346:         return
347:             proposal_.tally.yes + proposal_.tally.no + proposal_.tally.abstain >=
348:             proposal_.parameters.minVotingPower;
349:     }
350: 


plugins\utils\Ratio.sol
17: function _applyRatioCeiled(uint256 _value, uint256 _ratio) pure returns (uint256 result) {
18:     if (_ratio > RATIO_BASE) {
19:         revert RatioOutOfBounds({limit: RATIO_BASE, actual: _ratio});
20:     }
21: 
22:     _value = _value * _ratio;
23:     uint256 remainder = _value % RATIO_BASE;
24:     result = _value / RATIO_BASE;
25: 
26:     // Check if ceiling is needed
27:     if (remainder != 0) {
28:         ++result;
29:     }
30: }
31: 

```




## [G-02] Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration)

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.
``` solidity
for(uint256 i; i < 10; i++){
//doSomething
}
```
can be written as shown below.
``` solidity
for(uint256 i; i < 10;) {
  // loop logic
+  unchecked { i++; }
}
```
We can also write it as an inlined function like below.
``` solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

The above should be modified to:

There are 2 instances of this issue:

``` solidity
framework\dao\DAOFactory.sol
95:         for (uint256 i; i < _pluginSettings.length; ++i) {
96:             // Prepare plugin.
97:             (
98:                 address plugin,
99:                 IPluginSetup.PreparedSetupData memory preparedSetupData
100:             ) = pluginSetupProcessor.prepareInstallation(
101:                     address(createdDao),
102:                     PluginSetupProcessor.PrepareInstallationParams(
103:                         _pluginSettings[i].pluginSetupRef,
104:                         _pluginSettings[i].data
105:                     )
106:                 );
107: 
108:             // Apply plugin.
109:             pluginSetupProcessor.applyInstallation(
110:                 address(createdDao),
111:                 PluginSetupProcessor.ApplyInstallationParams(
112:                     _pluginSettings[i].pluginSetupRef,
113:                     plugin,
114:                     preparedSetupData.permissions,
115:                     hashHelpers(preparedSetupData.helpers)
116:                 )
117:             );
118:         }


framework\utils\RegistryUtils.sol
16:    for (uint256 i; i < nameLength; i++) {
17:         uint8 char = uint8(nameBytes[i]);
18: 
19:         // if char is between a-z
20:         if (char > 96 && char < 123) {
21:             continue;
22:         }
23: 
24:         // if char is between 0-9
25:         if (char > 47 && char < 58) {
26:             continue;
27:         }
28: 
29:         // if char is -
30:         if (char == 45) {
31:             continue;
32:         }
33: 
34:         // invalid if one char doesn't work with the rules above
35:         return false;
36:     }



```

## [G-03] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct


There are 12 instances of this issue:


``` solidity
core\permission\PermissionManager.sol
151:     PermissionLib.SingleTargetPermission memory item = items[i];
152: 
core\permission\PermissionManager.sol
171:             PermissionLib.MultiTargetPermission memory item = _items[i];
172: 
framework\dao\DAOFactory.sol
98:                 address plugin,
99:                 IPluginSetup.PreparedSetupData memory preparedSetupData
framework\dao\DAOFactory.sol
158:         PermissionLib.SingleTargetPermission[]
159:             memory items = new PermissionLib.SingleTargetPermission[](6);
160: 
framework\plugin\repo\PluginRepo.sol
167:      Tag memory tag = Tag(_release, build);
framework\plugin\repo\PluginRepoFactory.sol
67:         PermissionLib.SingleTargetPermission[]
68:             memory items = new PermissionLib.SingleTargetPermission[](6);
framework\plugin\setup\PluginSetupProcessor.sol
433:         PluginRepo.Version memory currentVersion = _params.pluginSetupRepo.getVersion(
434:             _params.currentVersionTag
435:         );
436: 
437:         PluginRepo.Version memory newVersion = _params.pluginSetupRepo.getVersion(
438:             _params.newVersionTag
439:         );
440: 
framework\plugin\setup\PluginSetupProcessor.sol
523:         PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(
524:             _params.pluginSetupRef.versionTag
525:         );
framework\plugin\setup\PluginSetupProcessor.sol
571: 
572:         PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(
573:             _params.pluginSetupRef.versionTag
574:         );
plugins\counter-example\v1\CounterV1PluginSetup.sol
54:         PermissionLib.MultiTargetPermission[]
55:             memory permissions = new PermissionLib.MultiTargetPermission[](
56:                 _multiplyHelper == address(0) ? 3 : 2
57:             );
58:         address[] memory helpers = new address[](1);
plugins\counter-example\v2\CounterV2PluginSetup.sol
55:         PermissionLib.MultiTargetPermission[]
56:             memory permissions = new PermissionLib.MultiTargetPermission[](
57:                 _multiplyHelper == address(0) ? 3 : 2
58:             );
plugins\counter-example\v2\CounterV2PluginSetup.sol
59:         address[] memory helpers = new address[](1);
plugins\counter-example\v2\CounterV2PluginSetup.sol
121:         PermissionLib.MultiTargetPermission[]
122:             memory permissions = new PermissionLib.MultiTargetPermission[](1);
plugins\counter-example\v2\CounterV2PluginSetup.sol
132:         address[] memory activeHelpers = new address[](1);
plugins\governance\admin\AdminSetup.sol
50:         PermissionLib.MultiTargetPermission[]
51:             memory permissions = new PermissionLib.MultiTargetPermission[](2);
plugins\governance\majority-voting\addresslist\AddresslistVotingSetup.sol
30:         (MajorityVotingBase.VotingSettings memory votingSettings, address[] memory members) = abi
31:             .decode(_data, (MajorityVotingBase.VotingSettings, address[]));
32: 
plugins\governance\majority-voting\addresslist\AddresslistVotingSetup.sol
45:         PermissionLib.MultiTargetPermission[]
46:             memory permissions = new PermissionLib.MultiTargetPermission[](4);
plugins\governance\majority-voting\token\TokenVotingSetup.sol
84:             MajorityVotingBase.VotingSettings memory votingSettings,
85:             TokenSettings memory tokenSettings,
86:             // only used for GovernanceERC20(token is not passed)
87:             GovernanceERC20.MintSettings memory mintSettings
plugins\governance\majority-voting\token\TokenVotingSetup.sol
149:         PermissionLib.MultiTargetPermission[]
150:             memory permissions = new PermissionLib.MultiTargetPermission[](
151:                 tokenSettings.addr != address(0) ? 3 : 4
152:             );
plugins\governance\multisig\MultisigSetup.sol
41:         PermissionLib.MultiTargetPermission[]
42:             memory permissions = new PermissionLib.MultiTargetPermission[](3);
43: 
```

## [G-04] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations 

There are 5 instances of this issue:

``` solidity
core\permission\PermissionManager.sol
27:     mapping(bytes32 => address) internal permissionsHashed;
core\utils\CallbackHandler.sol
11:     mapping(bytes4 => bytes4) internal callbackMagicNumbers;
framework\plugin\repo\PluginRepo.sol
53: 
54:     /// @notice The mapping between release and build numbers.
55:     mapping(uint8 => uint16) internal buildsPerRelease;
56: 
57:     /// @notice The mapping between the version hash and the corresponding version information.
58:     mapping(bytes32 => Version) internal versions;
59: 
60:     /// @notice The mapping between the plugin setup address and its corresponding version hash.
61:     mapping(address => bytes32) internal latestTagHashForPluginSetup;
framework\plugin\setup\PluginSetupProcessor.sol
59:     mapping(bytes32 => PluginState) public states;
framework\utils\InterfaceBasedRegistry.sol
25:     mapping(address => bool) public entries;
plugins\governance\majority-voting\MajorityVotingBase.sol
187:     mapping(uint256 => Proposal) internal proposals;
plugins\governance\multisig\Multisig.sol
75:     mapping(uint256 => Proposal) internal proposals;
plugins\token\MerkleDistributor.sol
29:     mapping(uint256 => uint256) private claimedBitMap;
plugins\utils\Addresslist.sol
17:     mapping(address => CheckpointsUpgradeable.History) private _addresslistCheckpoints;

```

## [G-05] Using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

There are ALL CONTRACT instances of this issue eg. :

``` solidity
- uint256 public override example;
+ uint256 private override example;
```

## [G-06] Using immutable for uint & address for save gas
Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

``` solidity
- address public override example;
+ address public immutable override example;
```

There are 3 instances of this issue:
``` solidity
framework\plugin\repo\PluginRepoFactory.sol
18:     address public pluginRepoBase;
framework\utils\TokenFactory.sol
26:     /// @notice The address of the `GovernanceERC20` base contract to clone from.
27:     address public governanceERC20Base;
28: 
29:     /// @notice The address of the `GovernanceWrappedERC20` base contract to clone from.
30:     address public governanceWrappedERC20Base;
31: 
32:     /// @notice The address of the `MerkleMinter` base contract to clone from.
33:     address public merkleMinterBase;
34: 
framework\utils\ens\ENSSubdomainRegistrar.sol
32:     address public resolver;

```

## [G-07] internal functions not called by the contract should be removed to save deployment gas
If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

There are 2 instances of this issue:

``` solidity
plugins\governance\multisig\Multisig.sol
365:     function _canApprove(uint256 _proposalId, address _account) internal view returns (bool) {
plugins\governance\multisig\Multisig.sol
389:     function _canExecute(uint256 _proposalId) internal view returns (bool) {
plugins\governance\multisig\Multisig.sol
403:     function _isProposalOpen(Proposal storage proposal_) internal view returns (bool) {
plugins\token\MerkleDistributor.sol
104:     ) internal view returns (bool) {
```

## [G-08] x = x + y is cheaper than x += y;

There are 1 instances of this issue :

``` solidity
plugins\governance\multisig\Multisig.sol
276:      proposal_.approvals += 1;
```

## [G-09] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Use a larger size then downcast where needed


## [G-10] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

There are 6 instances of this issue:

``` solidity
core\dao\DAO.sol
225:         if (_amount == 0) revert ZeroAmount();
226: 
framework\dao\DAOFactory.sol
68:         if (_pluginSettings.length == 0) {
69:             revert NoPluginProvided();
70:         }
framework\plugin\repo\PluginRepo.sol
138:         if (_release == 0) {
191:         if (_release == 0) {
plugins\governance\majority-voting\MajorityVotingBase.sol
570:      if (_start == 0) {
plugins\governance\majority-voting\MajorityVotingBase.sol
582:         if (_end == 0) {
plugins\governance\majority-voting\token\TokenVoting.sol
87:         if (totalVotingPower_ == 0) {
plugins\governance\multisig\Multisig.sol
220:         if (_startDate == 0) {
```

## [G-11] Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it's still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is public but can be marked as external since it's not called by the contract, and cases where a constructor is involved

There are 3 instances of this issue:

``` solidity
core\dao\DAO.sol
153:  function hasPermission(
154:         address _where,
155:         address _who,
156:         bytes32 _permissionId,
157:         bytes memory _data
158:     ) external view override returns (bool) {
159:         return isGranted(_where, _who, _permissionId, _data);
160:     }

core\dao\DAO.sol
250:     function isValidSignature(
251:         bytes32 _hash,
252:         bytes memory _signature
253:     ) external view override(IDAO, IERC1271) returns (bytes4) {
254:         if (address(signatureValidator) == address(0)) {
255:             // Return the invalid magic number
256:             return bytes4(0);
257:         }
258:         // Forward the call to the set signature validator contract
259:         return signatureValidator.isValidSignature(_hash, _signature);
260:     }


core\dao\IDAO.sol
25:    function hasPermission(
26:         address _where,
27:         address _who,
28:         bytes32 _permissionId,
29:         bytes memory _data
30:     ) external view returns (bool);

framework\plugin\repo\PluginRepoFactory.sol
45:     function createPluginRepoWithFirstVersion(
46:         string calldata _subdomain,
47:         address _pluginSetup,
48:         address _maintainer,
49:         bytes memory _releaseMetadata,
50:         bytes memory _buildMetadata
51:     ) external returns (PluginRepo pluginRepo) {
```


## [G-12] keccak256() should only need to be called on a specific string literal once


There are 18 instances of this issue:

``` solidity
core\dao\DAO.sol
39:     /// @notice The ID of the permission required to call the `execute` function.
40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
41: 
42:     /// @notice The ID of the permission required to call the `_authorizeUpgrade` function.
43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
44: 
45:     /// @notice The ID of the permission required to call the `setMetadata` function.
46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");
47: 
48:     /// @notice The ID of the permission required to call the `setTrustedForwarder` function.
49:     bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
50:         keccak256("SET_TRUSTED_FORWARDER_PERMISSION");
51: 
52:     /// @notice The ID of the permission required to call the `setSignatureValidator` function.
53:     bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
54:         keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");
55: 
56:     /// @notice The ID of the permission required to call the `registerStandardCallback` function.
57:     bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
58:         keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");
core\plugin\PluginUUPSUpgradeable.sol
35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");
framework\plugin\repo\PluginRepo.sol
49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
50: 
51:     /// @notice The ID of the permission required to call the `createVersion` function.
52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");
framework\plugin\repo\PluginRepoRegistry.sol
16:     bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
17:         keccak256("REGISTER_PLUGIN_REPO_PERMISSION");
framework\plugin\setup\PluginSetupProcessor.sol
27:     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
28:         keccak256("APPLY_INSTALLATION_PERMISSION");
29: 
30:     /// @notice The ID of the permission required to call the `applyUpdate` function.
31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");
32: 
33:     /// @notice The ID of the permission required to call the `applyUninstallation` function.
34:     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
35:         keccak256("APPLY_UNINSTALLATION_PERMISSION");
framework\utils\InterfaceBasedRegistry.sol
18:     bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRY_PERMISSION");
framework\dao\DAORegistry.sol
15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");
16: 
core\permission\PermissionManager.sol
14:     /// @notice The ID of the permission required to call the `grant`, `grantWithCondition`, `revoke`, and `bulk` function.
15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");
framework\utils\ens\ENSSubdomainRegistrar.sol
17:     /// @notice The ID of the permission required to call the `_authorizeUpgrade` function.
18:     bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRAR_PERMISSION");
20: 
21:     /// @notice The ID of the permission required to call the `registerSubnode` and `setDefaultResolver` function.
22:     bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
23:         keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");
plugins\counter-example\MultiplyHelper.sol
12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
plugins\counter-example\v1\CounterV1.sol
14:  bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
token\ERC20\governance\GovernanceERC20.sol
28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");
plugins\token\MerkleMinter.sol
24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");
25: 
26:     /// @notice The ID of the permission required to call the `changeDistributor` function.
27:     bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
28:         keccak256("CHANGE_DISTRIBUTOR_PERMISSION");
plugins\governance\multisig\Multisig.sol
71:     bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
72:         keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");

plugins\governance\majority-voting\addresslist\AddresslistVoting.sol
27:     bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
28:         keccak256("UPDATE_ADDRESSES_PERMISSION");
29: 
plugins\governance\majority-voting\MajorityVotingBase.sol
183:     bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
184:         keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");
185: 
plugins\governance\admin\Admin.sol
23:     bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
24:         keccak256("EXECUTE_PROPOSAL_PERMISSION");
plugins\counter-example\v2\CounterV2.sol
14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
```
