### [G-01] Use **Pre-increments** ****`++i` instead of `i += 1`**

Pre-increments are cheaper compared to `i+=1`

```solidity
275: unchecked {
276:            proposal_.approvals += 1;
277:        }
```

**Mitigation:**

```diff
275: unchecked {
-276:            proposal_.approvals += 1;
+276:            ++proposal_.approvals;
277:        }
```

- [Multisig.sol#L276](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276)

### [G-02] `Multisig.sol._execute(): proposals[_proposalId]` Use cached value

See `@audit` tag:

```solidity
File: /src/plugins/governance/multisig/Multisig.sol
348: function _execute(uint256 _proposalId) internal {
349:     Proposal storage proposal_ = proposals[_proposalId];

353:        _executeProposal(
354:            dao(),
355:            _proposalId,
356:            proposals[_proposalId].actions,  //@audit: initial access
357:            proposals[_proposalId].allowFailureMap //@audit: 2nd access
358:        );
359:    }
```

**Mitigation:**

```diff
File: /src/plugins/governance/multisig/Multisig.sol
348: function _execute(uint256 _proposalId) internal {
349:     Proposal storage proposal_ = proposals[_proposalId];

353:        _executeProposal(
354:            dao(),
355:            _proposalId,
-356:            proposals[_proposalId].actions,
-357:            proposals[_proposalId].allowFailureMap
+356:            proposal_.actions,
+357:            proposal_.allowFailureMap
358:        );
359:    }
```

- [Multisig.sol#L348](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L348)

### **[G‑03] State variables only set in the constructor should be declared `immutable`**

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a `PUSH32` (3 gas).

While `string`s are not value types, and therefore cannot be `immutable`/`constant` if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract `abstract` with `virtual` functions for the `string` accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

```solidity
File: /src/framework/plugin/repo/PluginRepoFactory.sol
	15: PluginRepoRegistry public pluginRepoRegistry;
	18: address public pluginRepoBase;
```

- [PluginRepoFactory.sol#L13](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L13)

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessor.sol
128: PluginRepoRegistry public repoRegistry;
```

- [PluginSetupProcessor.sol#L128](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L128)

### **[G‑04] `keccak256()` should only need to be called on a specific string literal once**

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once.

```solidity
File: /src/plugins/counter-example/v2/CounterV2PluginSetup.sol
49: bytes memory initData = abi.encodeWithSelector(
50:            bytes4(keccak256("initialize(address,address,uint256)")),
51:            _dao,
52:            multiplyHelper,
53:            _num
54:        );

...
115: initData = abi.encodeWithSelector(
116:                bytes4(keccak256("setNewVariable(uint256)")),
117:                _newVariable
118:            );
```

- [CounterV2PluginSetup.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L49)

```solidity
47: bytes memory initData = abi.encodeWithSelector(
48:            bytes4(keccak256("initialize(address,address,uint256)")),
49:            _dao,
50:            multiplyHelper,
51:            _num
52:        );
```

- [CounterV1PluginSetup.sol#L47](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L47)

### [G-05] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```solidity
File: /src/core/permission/PermissionManager.sol
151: PermissionLib.SingleTargetPermission memory item = items[i];
171: PermissionLib.MultiTargetPermission memory item = _items[i];
```

- [PermissionManager.sol#L151](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L151)