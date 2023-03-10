# [G-01] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

## Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

## Findings

Total:1

[packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L58](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L58)

```solidity
55:    mapping(uint8 => uint16) internal buildsPerRelease;
56:
57:    /// @notice The mapping between the version hash and the corresponding version information.
58:    mapping(bytes32 => Version) internal versions;
```

# [G-02] Multiple accesses of a `mapping/array` should use a local variable cache

## Impact

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory/calldata` 

## Findings

Total:1

[packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L61-L72](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L61-L72)

```solidity
    function _register(address _registrant) internal {
        if (entries[_registrant]) {
            revert ContractAlreadyRegistered({registrant: _registrant});
        }


        // Will revert if address is not a contract or doesn't fully support targetInterfaceId + ERC165.
        if (!_registrant.supportsInterface(targetInterfaceId)) {
            revert ContractInterfaceInvalid(_registrant);
        }


        entries[_registrant] = true;
    }
```

## Recommendation

```

isRegistererd = entries[_registrant];

```

# [G-03] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

## Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

## Findings

Total:1

[packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L58](https://github.com/code-423n4/2023-03-aragon/tree/main//packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L58)

```solidity
55:    mapping(uint8 => uint16) internal buildsPerRelease;
56:
57:    /// @notice The mapping between the version hash and the corresponding version information.
58:    mapping(bytes32 => Version) internal versions;
```

# [G-04] Don't use `_msgSender()` if not supporting EIP-2771

## Impact

The use of `_msgSender()` when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption.

## Findings

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

## Recommendation

Replace `_msgSender()` with `msg.sender` if there is no mechanism to support meta-transactions like EIP-2771 implemented.
