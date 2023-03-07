# GAS OPTIMIZATION REPORT

### Summary of optimizations


| Number     | Issue details                                                                                                                                         | Instances |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------: |
| [G-1](#G1) | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when  it is not possible for them to overflow, for example when used in `for` and `while` loops |     2     |
| [G-2](#G2) | Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead.                                                                              |    30    |
| [G-3](#G3) | `abi.encode()` is less efficient than `abi.encodePacked()`                                                                                            |     6     |
| [G-4](#G4) | Multiple accesses of a mapping/array should use a local variable cache                                                                                |     5     |
| [G-5](#G5) | `>=` costs less gas than `>`.                                                                                                                         |    14    |

*Total: 5 issues.*

---

### Gas Optimizations

### <a id=G1>[G-1]</a> `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when  it is not possible for them to overflow, for example when used in `for` and `while` loops

##### Description

In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck [cannot be made inline](https://github.com/ethereum/solidity/issues/10695).
Example for loop:

```Solidity
for (uint i = 0; i < length; i++) {
// do something that doesn't change the value of i
}
```

In this example, the for loop post condition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body `is 2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. You should manually do this by:

```Solidity
for (uint i = 0; i < length; i = unchecked_inc(i)) {
	// do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
	unchecked {
		return i + 1;
	}
}
```

Or just:

```Solidity
for (uint i = 0; i < length;) {
	// do something that doesn't change the value of i
	unchecked { i++; 	}
}
```

Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

Gas savings: roughly speaking this can save 30-40 gas per loop iteration. For lengthy loops, this can be significant!
(This is only relevant if you are using the default solidity checked arithmetic.)

##### Recommendation

Use the `unchecked` keyword

##### *Instances (2):*

File: [2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95 )

```solidity
95: for (uint256 i; i < _pluginSettings.length; ++i) {
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16 )

```solidity
16: for (uint256 i; i < nameLength; i++) {
```

### <a id=G2>[G-2]</a> Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead.

##### Description

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

##### Recommendation

Use `uint256` instead.

##### *Instances (30):*

File: [2023-03-aragon/packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L192 )

```solidity
192: if (!hasBit(_allowFailureMap, uint8(i))) {
198: failureMap = flipBit(failureMap, uint8(i));
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/proposal/IProposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L22 )

```solidity
22: uint64 startDate,
23: uint64 endDate,
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/proposal/Proposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L48 )

```solidity
48: uint64 _startDate,
49: uint64 _endDate,
```

File: [2023-03-aragon/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L48 )

```solidity
48: uint64 _startDate,
49: uint64 _endDate,
```

File: [2023-03-aragon/packages/contracts/src/core/utils/BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol#L8 )

```solidity
8: function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {
16: function flipBit(uint256 bitmap, uint8 index) pure returns (uint256) {
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L12 )

```solidity
12: function updateReleaseMetadata(uint8 _release, bytes calldata _releaseMetadata) external;
20: uint8 _release,
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L34 )

```solidity
34: uint8 release;
35: uint16 build;
55: mapping(uint8 => uint16) internal buildsPerRelease;
65: uint8 public latestRelease;
80: error InvalidReleaseIncrement(uint8 latestRelease, uint8 newRelease);
86: error PluginSetupAlreadyInPreviousRelease(uint8 release, uint16 build, address pluginSetup);
100: uint8 release,
101: uint16 build,
109: event ReleaseMetadataUpdated(uint8 release, bytes releaseMetadata);
129: uint8 _release,
165: uint16 build = ++buildsPerRelease[_release];
188: uint8 _release,
209: function getLatestVersion(uint8 _release) public view returns (Version memory) {
210: uint16 latestBuild = uint16(buildsPerRelease[_release]);
244: function buildCount(uint8 _release) public view returns (uint256) {
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L48 )

```solidity
48: uint16 _currentBuild,
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L20 )

```solidity
20: uint16 _currentBuild,
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L17 )

```solidity
17: uint8 char = uint8(nameBytes[i]);
```

### <a id=G3>[G-3]</a> `abi.encode()` is less efficient than `abi.encodePacked()`

##### Description

`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the `keccak` method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.
For example:
`abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0))` encodes to the same as `abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))`
and `abi.encodePacked(uint[](1,2), uint[](3))` encodes to the same as `abi.encodePacked(uint[](1), uint[](2,3))`
Therefore these examples will also generate the same hashes even so they are different inputs.
On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.

##### Recommendation

Use `abi.encodePacked()` where possible to save gas

##### *Instances (6):*

File: [2023-03-aragon/packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L272 )

```solidity
272: return abi.encode(magicNumber);
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L33 )

```solidity
33: return keccak256(abi.encode(_dao, _plugin));
52: abi.encode(
73: abi.encode(_pluginSetupRef.versionTag, _pluginSetupRef.pluginSetupRepo, _helpersHash)
80: return keccak256(abi.encode(_helpers));
89: return keccak256(abi.encode(_permissions));
```

### <a id=G4>[G-4]</a> Multiple accesses of a mapping/array should use a local variable cache.

##### Description

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata. [Similar findings](https://github.com/code-423n4/2022-06-infinity-findings/issues/186)

##### Recommendation

Use a local variable cache.

##### *Instances (5):*

File: [2023-03-aragon/packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L186 )

```solidity
186: (bool success, bytes memory response) = to.call{value: _actions[i].value}(
187: _actions[i].data
```

File: [2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L103 )

```solidity
103: _pluginSettings[i].pluginSetupRef,
104: _pluginSettings[i].data
112: _pluginSettings[i].pluginSetupRef,
```

### <a id=G5>[G-5]</a> `>=` costs less gas than `>`.

##### Description

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which [saves 3 gas](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

##### Recommendation

Use `>=` instead if appropriate.

##### *Instances (14):*

File: [2023-03-aragon/packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L178 )

```solidity
178: if (_actions.length > MAX_ACTIONS) {
```

File: [2023-03-aragon/packages/contracts/src/core/utils/BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol#L10 )

```solidity
10: return bitValue > 0;
```

File: [2023-03-aragon/packages/contracts/src/framework/dao/DAORegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L59 )

```solidity
59: if ((bytes(subdomain).length > 0)) {
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L143 )

```solidity
143: if (_release - latestRelease > 1) {
147: if (_release > latestRelease) {
181: if (_releaseMetadata.length > 0) {
195: if (_release > latestRelease) {
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L55 )

```solidity
55: if (!(bytes(subdomain).length > 0)) {
```

File: [2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L378 )

```solidity
378: if (_params.permissions.length > 0) {
535: if (_params.permissions.length > 0) {
635: if (_params.permissions.length > 0) {
669: if (_initData.length > 0) {
```

File: [2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L20 )

```solidity
20: if (char > 96 && char < 123) {
25: if (char > 47 && char < 58) {
```
