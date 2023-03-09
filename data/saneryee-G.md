# Gas Optimizations Report

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |     7     |
| [G-002] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |     3     |
| [G-003] | Don't use `_msgSender()` if not supporting EIP-2771                                                                                |     6     |
| [G-004] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate                 |     1     |
| [G-005] | Using `calldata` instead of memory for read-only arguments in external functions saves gas                                         |     7     |
| [G-006] | Multiple accesses of a `mapping/array` should use a local variable cache                                                           |     1     |
| [G-007] | internal functions only called once can be inlined to save gas                                                                     |     9     |
| [G-008] | Expressions that cannot be overflowed can be unchecked                                                                             |     3     |

## [G-001] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:7

[packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L96](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L96)

```solidity
96:    address[] memory helpers = new address[](1);
```

[packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L266](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L266)

```solidity
266:    bytes4[] memory interfaceIds = new bytes4[](3);
```

[packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L108](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L108)

```solidity
108:    bool[] memory supportedIds = _getTokenInterfaceIds(token);
```

[packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L212](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L212)

```solidity
212:    bool[] memory supportedIds = _getTokenInterfaceIds(token);
```

[packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L59](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L59)

```solidity
59:    address[] memory helpers = new address[](1);
```

[packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L132](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L132)

```solidity
132:    address[] memory activeHelpers = new address[](1);
```

[packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L58](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L58)

```solidity
58:    address[] memory helpers = new address[](1);
```

### Recommendation

Using `storage` instead of `memory`

## [G-002] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:3

[packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L46](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L46)

```solidity
46:    ) internal virtual onlyInitializing {
```

[packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L39](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L39)

```solidity
39:    function __PluginUUPSUpgradeable_init(IDAO _dao) internal virtual onlyInitializing {
```

[packages/contracts/src/core/plugin/PluginCloneable.sol#L22](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/core/plugin/PluginCloneable.sol#L22)

```solidity
22:    function __PluginCloneable_init(IDAO _dao) internal virtual onlyInitializing {
```

### Recommendation

Mark the function as payable.

## [G-003] Don't use `_msgSender()` if not supporting EIP-2771

### Impact

The use of `_msgSender()` when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption.

### Findings

Total:6

[packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97)

```solidity
97:    if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
```

[packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L98](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L98)

```solidity
98:    revert ProposalCreationForbidden(_msgSender());
```

[packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L91](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L91)

```solidity
91:    if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {
```

[packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L92](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L92)

```solidity
92:    revert ProposalCreationForbidden(_msgSender());
```

[packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216)

```solidity
216:    if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {
```

[packages/contracts/src/plugins/governance/multisig/Multisig.sol#L217](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/multisig/Multisig.sol#L217)

```solidity
217:    revert ProposalCreationForbidden(_msgSender());
```

### Recommendation

Replace `_msgSender()` with `msg.sender` if there is no mechanism to support meta-transactions like EIP-2771 implemented.

## [G-004] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

Total:1

[packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L58](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L58)

```solidity
55:    mapping(uint8 => uint16) internal buildsPerRelease;
56:
57:        /// @notice The mapping between the version hash and the corresponding version information.
58:        mapping(bytes32 => Version) internal versions;
```

## [G-005] Using `calldata` instead of memory for read-only arguments in external functions saves gas

### Impact

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a `for-loop` to copy each index of the `calldata` to the `memory` index. Each iteration of this `for-loop` costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.<br><br> If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one.

### Findings

Total:7

[packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L86-L87](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L86-L87)

```solidity
86:    function hashPermissions(
87:        PermissionLib.MultiTargetPermission[] memory _permissions
```

[packages/contracts/src/plugins/token/MerkleDistributor.sol#L66-L70](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/token/MerkleDistributor.sol#L66-L70)

```solidity
66:    function unclaimedBalance(
67:            uint256 _index,
68:            address _to,
69:            uint256 _amount,
70:            bytes32[] memory _proof
```

[packages/contracts/src/plugins/token/MerkleDistributor.sol#L83-L87](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/token/MerkleDistributor.sol#L83-L87)

```solidity
83:    function unclaimedBalance(
84:            uint256 _index,
85:            address _to,
86:            uint256 _amount,
87:            bytes32[] memory _proof
```

[packages/contracts/src/plugins/token/MerkleDistributor.sol#L99-L103](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/token/MerkleDistributor.sol#L99-L103)

```solidity
99:    function unclaimedBalance(
100:            uint256 _index,
101:            address _to,
102:            uint256 _amount,
103:            bytes32[] memory _proof
```

[packages/contracts/src/plugins/token/IMerkleDistributor.sol#L28-L32](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/token/IMerkleDistributor.sol#L28-L32)

```solidity
28:    function unclaimedBalance(
29:            uint256 _index,
30:            address _to,
31:            uint256 _amount,
32:            bytes32[] memory _proof
```

[packages/contracts/src/plugins/token/IMerkleDistributor.sol#L41-L45](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/token/IMerkleDistributor.sol#L41-L45)

```solidity
41:    function unclaimedBalance(
42:            uint256 _index,
43:            address _to,
44:            uint256 _amount,
45:            bytes32[] memory _proof
```

[packages/contracts/src/core/dao/IDAO.sol#L46-L48](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/core/dao/IDAO.sol#L46-L48)

```solidity
46:    function execute(
47:            bytes32 _callId,
48:            Action[] memory _actions,
```

## [G-006] Multiple accesses of a `mapping/array` should use a local variable cache

### Impact

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory/calldata` (**与[G-001]类似**)

### Findings

Total:1

[packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L71](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L71)

```solidity
71:    entries[_registrant] = true;
```


## [G-007] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:9

[packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L457](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L457)

```solidity
457:    function _execute(uint256 _proposalId) internal virtual {
```

[packages/contracts/src/framework/dao/DAOFactory.sol#L156](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/dao/DAOFactory.sol#L156)

```solidity
156:    function _setDAOPermissions(DAO _dao) internal {
```

[packages/contracts/src/framework/dao/DAOFactory.sol#L141](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/dao/DAOFactory.sol#L141)

```solidity
141:    function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {
```

[packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L65](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L65)

```solidity
65:    function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal {
```

[packages/contracts/src/core/permission/PermissionManager.sol#L327](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/core/permission/PermissionManager.sol#L327)

```solidity
327:    function _auth(bytes32 _permissionId) internal view virtual {
```

[packages/contracts/src/core/permission/PermissionManager.sol#L212](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/core/permission/PermissionManager.sol#L212)

```solidity
212:    function _initializePermissionManager(address _initialOwner) internal {
```

[packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L33](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L33)

```solidity
33:    function _createProposalId() internal returns (uint256 proposalId) {
```

[packages/contracts/src/core/plugin/proposal/Proposal.sol#L33](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/core/plugin/proposal/Proposal.sol#L33)

```solidity
33:    function _createProposalId() internal returns (uint256 proposalId) {
```

[packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L112](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L112)

```solidity
112:    function _afterTokenTransfer(address from, address to, uint256 amount) internal override {
```

## [G-008] Expressions that cannot be overflowed can be unchecked

### Impact

There are several cases that do not lead to overflow and can be unchecked.

- a previous push in array
- If an underflow occurs, the next statement will revert
- There is a check in the previous code.

### Findings

Total:3

[packages/contracts/src/plugins/utils/Ratio.sol#L23](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/utils/Ratio.sol#L23)

```solidity
23:    uint256 remainder = _value % RATIO_BASE;
```

[packages/contracts/src/plugins/token/MerkleDistributor.sol#L112](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/token/MerkleDistributor.sol#L112)

```solidity
112:    uint256 claimedBit_index = _index % 256;
```

[packages/contracts/src/plugins/token/MerkleDistributor.sol#L122](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/plugins/token/MerkleDistributor.sol#L122)

```solidity
122:    uint256 claimedBit_index = _index % 256;
```
