# Gas Report

## `abi.encodePacked` is more gas efficient than `abi.encode`

`abi.encode` pads all elementary types to 32 bytes, whereas `abi.encodePacked` will only use the minimal required memory to encode the data. See [here](https://docs.soliditylang.org/en/v0.8.11/abi-spec.html?highlight=encodepacked#non-standard-packed-mode) for more info.

Instances: 6
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L33)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L52)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L73](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L73)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L80](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L80)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L89](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L89)
- [packages/contracts/src/core/dao/DAO.sol#L272](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L272)

<br>

## Use assembly to check for `address(0)`

Saves 16000 deployment gas per instance and 6 runtime gas per instance.

```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

Instances: 18
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L98](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L98)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L151](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L151)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L181](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L181)
- [packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L39](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L39)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L44](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L44)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L57](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L57)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L81](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L81)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L43)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L56](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L56)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L80](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L80)
- [packages/contracts/src/framework/utils/TokenFactory.sol#L86](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L86)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L65](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L65)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L89](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L89)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L103](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L103)
- [packages/contracts/src/core/dao/DAO.sol#L225](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L225)
- [packages/contracts/src/core/dao/DAO.sol#L252](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L252)
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L116](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L116)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L106](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L106)

<br>

## Use assembly to calculate hashes

Saves 5000 deployment gas per instance and 80 runtime gas per instance.

### Unoptimized
```
function solidityHash(uint256 a, uint256 b) public view {
	//unoptimized
	keccak256(abi.encodePacked(a, b));
}
```

### Optimized
```
function assemblyHash(uint256 a, uint256 b) public view {
	//optimized
	assembly {
		mstore(0x00, a)
		mstore(0x20, b)
		let hashedVal := keccak256(0x00, 0x40)
	}
}
```

Instances: 12
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L49)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L70](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L70)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L116](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L116)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L154](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L154)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L48](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L48)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L69](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L69)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L114](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L114)
- [packages/contracts/src/plugins/token/MerkleDistributor.sol#L105](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L105)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86)
- [packages/contracts/src/framework/dao/DAORegistry.sol#L64](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L64)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L252](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L252)
- [packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L63)

<br>

## Cache storage variables rather than re-reading from storage

Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read.

Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory/calldata.

Instances: 9
- [packages/contracts/src/framework/utils/TokenFactory.sol#L124](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L124)
- [packages/contracts/src/framework/utils/TokenFactory.sol#L128](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L128)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L93](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L93)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L94](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L94)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L95](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L95)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L143](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L143)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L144](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L144)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L147](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L147)

<br>

## Use `calldata` instead of `memory` for function arguments that are read only

Instances: 18
- [packages/contracts/src/plugins/token/MerkleDistributor.sol#L87](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L87)
- [packages/contracts/src/plugins/token/MerkleDistributor.sol#L103](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L103)
- [packages/contracts/src/utils/Proxy.sol#L12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L12)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L49)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L50](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L50)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L251](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L251)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L44](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L44)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L47](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L47)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L68](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L68)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L79](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L79)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L87](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L87)
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L35](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L35)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L667](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L667)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L723](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L723)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L724](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L724)
- [packages/contracts/src/core/utils/CallbackHandler.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L33)
- [packages/contracts/src/core/dao/DAO.sol#L155](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L155)
- [packages/contracts/src/core/dao/DAO.sol#L250](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L250)

<br>

## Duplicated `require`/`revert` checks should be refactored to a modifier or function.

Instances: 2
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L673](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L673)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L685](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L685)

<br>

## Use `indexed` to save gas
Using `indexed` for value type event parameters (address, uint, bool) saves at least 80 gas in emitting the event (https://gist.github.com/Tomosuke0930/9bf61e01a8c3e214d95a9b84dcb41d97).

Note that for other types however (string, bytes), it is more expensive.

Instances: 18
- [packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L33)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L228](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L228)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L229](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L229)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L230](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L230)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L231](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L231)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L119](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L119)
- [packages/contracts/src/plugins/token/IMerkleDistributor.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L15)
- [packages/contracts/src/plugins/token/IMerkleMinter.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L21)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L100](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L100)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L101](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L101)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L109](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L109)
- [packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L25)
- [packages/contracts/src/core/utils/CallbackHandler.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L25)
- [packages/contracts/src/core/dao/IDAO.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L63)
- [packages/contracts/src/core/dao/IDAO.sol#L91](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L91)
- [packages/contracts/src/core/dao/IDAO.sol#L99](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L99)
- [packages/contracts/src/core/dao/IDAO.sol#L111](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L111)
- [packages/contracts/src/core/dao/IDAO.sol#L119](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L119)

<br>

## Use `constant`, `immutable` where applicable

Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD. Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas).

Instances: 7
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L20](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L20)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L21)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L20](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L20)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L21)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L15)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L18)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L128](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L128)

<br>

## Inline `internal` functions that are only called once

Saves 20-40 gas per instance. See https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/ for more details.

Instances: 4
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L287](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L287)
- [packages/contracts/src/framework/dao/DAOFactory.sol#L73](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L73)
- [packages/contracts/src/framework/dao/DAOFactory.sol#L121](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L121)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L58](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L58)

<br>

## Expressions for constant values such as a call to `keccak256` should use `immutable` rather than `constant`

See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detailed description of the issue.

Instances: 13
- [packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14)
- [packages/contracts/src/plugins/token/MerkleMinter.sol#L24](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L24)
- [packages/contracts/src/framework/dao/DAORegistry.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31)
- [packages/contracts/src/core/dao/DAO.sol#L40](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40)
- [packages/contracts/src/core/dao/DAO.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L43)
- [packages/contracts/src/core/dao/DAO.sol#L46](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L46)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35)
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28)

<br>

## Using `storage` instead of `memory` for structs/arrays saves gas


When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read.

Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

Instances: 2
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L44](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L44)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L68](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L68)

<br>

## Use `unchecked` for operations that cannot overflow/underflow

By bypassing Solidity's built in overflow/underflow checks using `unchecked`, we can save gas. This is especially beneficial for the index variable within for loops (saves 120 gas per iteration).

Instances: 2
- [packages/contracts/src/framework/utils/RegistryUtils.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16)
- [packages/contracts/src/framework/dao/DAOFactory.sol#L95](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95)

<br>

## Use `private` rather than `public` for constants

Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table. If needed to be viewed externally, the values can be read from the verified contract source code.

Instances: 27
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183)
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71)
- [packages/contracts/src/plugins/governance/admin/Admin.sol#L23](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L23)
- [packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14)
- [packages/contracts/src/plugins/token/MerkleMinter.sol#L24](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L24)
- [packages/contracts/src/plugins/token/MerkleMinter.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L27)
- [packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22)
- [packages/contracts/src/framework/dao/DAORegistry.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52)
- [packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L34](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L34)
- [packages/contracts/src/core/dao/DAO.sol#L40](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40)
- [packages/contracts/src/core/dao/DAO.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L43)
- [packages/contracts/src/core/dao/DAO.sol#L46](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L46)
- [packages/contracts/src/core/dao/DAO.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L49)
- [packages/contracts/src/core/dao/DAO.sol#L53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L53)
- [packages/contracts/src/core/dao/DAO.sol#L57](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L57)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35)
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28)

<br>

## Change `public` functions to `external`

Functions marked as `public` that are not called internally should be set to `external` to save gas and improve code quality. External call cost is less expensive than of public functions.

Instances: 25
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L269](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L269)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L283](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L283)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L294](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L294)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L303](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L303)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L308](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L308)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L352](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L352)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L357](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L357)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L363](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L363)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L369](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L369)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L375](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L375)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L395](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L395)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L61](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L61)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L308](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L308)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L328](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L328)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L333](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L333)
- [packages/contracts/src/plugins/utils/Addresslist.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L33)
- [packages/contracts/src/plugins/utils/Addresslist.sol#L47](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L47)
- [packages/contracts/src/plugins/utils/Addresslist.sol#L53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L53)
- [packages/contracts/src/plugins/token/MerkleDistributor.sol#L88](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L88)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L217](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L217)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L244](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L244)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L30](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L30)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L55](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L55)
- [packages/contracts/src/core/plugin/Plugin.sol#L20](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/Plugin.sol#L20)
- [packages/contracts/src/core/plugin/PluginCloneable.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L27)

<br>

## Naming a return variable and still calling the `return` keyword wastes gas

Instances: 6
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L37](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L37)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L36](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L36)
- [packages/contracts/src/plugins/token/MerkleMinter.sol#L79](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L79)
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L26](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L26)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L289](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L289)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L401](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L401)

<br>

## Usage of `uint` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Consider using a larger size then downcasting where needed.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

Instances: 2
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L65](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L65)

<br>

## `++i` costs less gas than `i++`

True for `--i`/`i--` as well, and is especially important in for loops. Saves 5 gas per iteration.

Instances: 1
- [packages/contracts/src/framework/utils/RegistryUtils.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16)

<br>
