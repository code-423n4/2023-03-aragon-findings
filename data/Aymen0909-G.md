# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1  | State variables only set in the constructor should be declared `immutable`  | 11 |
| 2  | Use `unchecked` blocks to save gas  |  2 |
| 3  | `storage` variable should be cached into `memory` variables instead of re-reading them  |  8 |
| 4  | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  1 |


## Findings

### 1- State variables only set in the constructor should be declared `immutable` :

The state variables only set in the constructor should be declared `immutable`, this avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There are 11 instances of this issue:

File: PluginRepoFactory.sol [Line 15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L15)
```
PluginRepoRegistry public pluginRepoRegistry;
```

File: PluginRepoFactory.sol [Line 18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L18)
```
address public pluginRepoBase;
```

File: PluginSetupProcessor.sol [Line 128](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L128)
```
PluginRepoRegistry public repoRegistry;
```

File: TokenFactory.sol [Line 27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L27)
```
address public governanceERC20Base;
```

File: TokenFactory.sol [Line 30](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L30)
```
address public governanceWrappedERC20Base;
```

File: TokenFactory.sol [Line 33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L33)
```
address public merkleMinterBase;
```

File: TokenFactory.sol [Line 36](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L36)
```
MerkleDistributor public distributorBase;
```

File: CounterV1PluginSetup.sol [Line 20-21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L20-L21)
```
MultiplyHelper public multiplyHelperBase;
CounterV1 public counterBase;
```

File: CounterV2PluginSetup.sol [Line 20-21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L20-L21)
```
MultiplyHelper public multiplyHelperBase;
CounterV2 public counterBase;
```


### 2- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 2 instances of this issue:

File: AddresslistVoting.sol [Line 168-174](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L168-L174)
```
if (_voteOption == VoteOption.Yes) {
    proposal_.tally.yes = proposal_.tally.yes + 1;
} else if (_voteOption == VoteOption.No) {
    proposal_.tally.no = proposal_.tally.no + 1;
} else if (_voteOption == VoteOption.Abstain) {
    proposal_.tally.abstain = proposal_.tally.abstain + 1;
}
```

In the code linked above all the increment operations cannot realistically overflow as they represent users votes where each users only gives 1 votes, so they should be marked as `unchecked`. 

File: Multisig.sol [Line 214](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L214)
```
uint64 snapshotBlock = block.number.toUint64() - 1;
```

In the code linked above the operation cannot underflow as the `block.number` value will always be greater than 1 so they should be marked as `unchecked`. 


### 3- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 8 instances of this issue :

File: PluginRepo.sol [Line 143-147](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L143-L147)

In the code linked above the value of `latestRelease` is read multiple times (3) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File:  PluginRepo.sol [Line 157-159](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L157-L159)

In the code linked above the value of `version.tag.release` is read multiple times (3) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: PluginSetupProcessor.sol [Line 426-428](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L426-L428)

In the code linked above the value of `pluginState.currentAppliedSetupId` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: PluginSetupProcessor.sol [Line 565-567](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L565-L567)

In the code linked above the value of `pluginState.currentAppliedSetupId` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: TokenFactory.sol [Line 124-128](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L124-L128)

In the code linked above the value of `distributorBase` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: MajorityVotingBase.sol [Line 319-320](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L319-L320)

In the code linked above the value of `proposal_.parameters.supportThreshold` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: MajorityVotingBase.sol [Line 336-337](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L336-L337)

In the code linked above the value of `proposal_.parameters.supportThreshold` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: Multisig.sol [Line 178-181](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L178-L181)

In the code linked above the value of `multisigSettings.minApprovals` is read multiple times (2) from storage and its value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.


### 4- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There is 1 instance of this issue:

File: Multisig.sol [Line 276](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276)
```
proposal_.approvals += 1;
```