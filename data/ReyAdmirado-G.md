| | issue |
| ----------- | ----------- |
| 1 | [State variables only set in the constructor should be declared immutable.](#1-state-variables-only-set-in-the-constructor-should-be-declared-immutable) |
| 2 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#2-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 3 | [state variables should be cached in stack variables rather than re-reading them from storage](#3-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage-ones-not-caught-by-c4udit) |
| 4 | [not using the named return variables when a function returns, wastes deployment gas](#4-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 5 | [can make the variable outside the loop to save gas](#5-can-make-the-variable-outside-the-loop-to-save-gas) |
| 6 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#6-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 7 | [require() or revert() statements that check input arguments should be at the top of the function](#7-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function) |
| 8 | [internal functions only called once can be inlined to save gas](#8-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 9 | [abi.encode() is less efficient than abi.encodepacked()](#9-abiencode-is-less-efficient-than-abiencodepacked) |
| 10 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#10-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 11 | [ Ternary over if ... else](#11-ternary-over-if--else) |
| 12 | [public functions not called by the contract should be declared external instead](#12-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 13 | [should use arguments instead of state variable](#13-should-use-arguments-instead-of-state-variable) |
| 14 | [before some functions we should check some variables for possible gas save](#14-before-some-functions-we-should-check-some-variables-for-possible-gas-save) |
| 15 | [instead of calculating a statevar with keccak256() every time the contract is made pre calculate them before and only give the result to a constant](#15-instead-of-calculating-a-statevar-with-keccak256-every-time-the-contract-is-made-pre-calculate-them-before-and-only-give-the-result-to-a-constant) |
| 16 | [Optimize names to save gas](#16-optimize-names-to-save-gas) |
| 17 | [Non-strict inequalities are cheaper than strict ones](#17-non-strict-inequalities-are-cheaper-than-strict-ones) |
| 18 | [Setting the constructor to payable](#18-setting-the-constructor-to-payable) |
| 19 | [part of the code can be pre calculated](#19-part-of-the-code-can-be-pre-calculated) |
| 20 | [avoid an unnecessary initialization and do not write a default value for addresses](#20-avoid-an-unnecessary-initialization-and-do-not-write-a-default-value-for-addresses) |

## 1. State variables only set in the constructor should be declared immutable.

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmaccess (100 gas) with a PUSH32 (3 gas).

- [PluginSetupProcessor.sol#L128](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L128)

- [PluginRepoFactory.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L15)
- [PluginRepoFactory.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L18)

- [CounterV2PluginSetup.sol#L20](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L20)
- [CounterV2PluginSetup.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L21)

- [CounterV1PluginSetup.sol#L20](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L20)
- [CounterV1PluginSetup.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L21)


## 2. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [InterfaceBasedRegistry.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18)

- [ENSSubdomainRegistrar.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18)
- [ENSSubdomainRegistrar.sol#L22](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22)

- [Admin.sol#L23](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L23)

- [MajorityVotingBase.sol#L183](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183)

- [AddresslistVoting.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27)

- [Multisig.sol#L71](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71)

- [PluginSetupProcessor.sol#L28](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L28)
- [PluginSetupProcessor.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31)
- [PluginSetupProcessor.sol#L34](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L34)

- [PluginRepo.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49)
- [PluginRepo.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52)

- [PluginRepoRegistry.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16)

- [DAORegistry.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15)

- [MultiplyHelper.sol#L12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12)

- [CounterV2.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14)

- [CounterV1.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14)

- [MerkleMinter.sol#L24](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L24)
- [MerkleMinter.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L27)

- [PermissionManager.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L15)

- [DAO.sol#L40](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40)
- [DAO.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L43)
- [DAO.sol#L46](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L46)
- [DAO.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L49)
- [DAO.sol#L53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L53)
- [DAO.sol#L57](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L57)

- [GovernanceERC20.sol#L28](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28)

- [ProposalUpgradeable.sol#L35](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L35)


## 3. state variables should be cached in stack variables rather than re-reading them from storage (ones not caught by C4udit)

Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

example 
possible 97 gas save with risking 3 gas. if the check fails we save 97 for the second `quitPeriod` read and if it passes we just lose 3 gas. cache it before this line

`node` can be cached because it is used twice in L86 and L93. this will save 97 gas. cache it before L94
- [ENSSubdomainRegistrar.sol#L86](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86)

`resolver` can be cached at the start of the function because it is used twice in L94 and L95. this will save 97 gas
- [ENSSubdomainRegistrar.sol#L94](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L94)

possible gas save with only risking 3 gas. if the check passes (usually passes) we save gas for the second `multisigSettings.minApprovals` read and if it passes we just lose 3 gas. cache it before the if statement
- [Multisig.sol#L178](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L178)

`latestRelease` is being checked at least twice in this function(if the first if succeeds second use is inside it, and if it fails the second if statement check is the second use) so we should cache `latestRelease` before L143 to reduce one storage read.
- [PluginRepo.sol#L143-L148](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L143-L148)


## 4. not using the named return variables when a function returns, wastes deployment gas

- [PluginSetupProcessor.sol#L401](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L401)

- [CounterV2PluginSetup.sol#L37](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L37)

- [CounterV1PluginSetup.sol#L36](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L36)

- [MerkleMinter.sol#L79](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L79)


## 5. can make the variable outside the loop to save gas

make the variable outside and only give the value to variable inside

- [PermissionManager.sol#L151](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L151)
- [PermissionManager.sol#L171](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L171)

- [DAO.sol#L185](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L185)
- [DAO.sol#L186](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L186)


## 6. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

- [DAOFactory.sol#L95](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95)


## 7. require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

swap the position of the third require with the first one because its gonna be cheaper checks first
- [Multisig.sol#L216-L228](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216-L228)

swap these two requires
- [PluginRepo.sol#L195-L200](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L195-L200)


## 8. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.
following are the ones that makes sense to be inlined

- [DAOFactory.sol#L141](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L141)
- [DAOFactory.sol#L156](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L156)

- [PluginRepoFactory.sol#L65](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L65)

- [PermissionManager.sol#L212](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L212)
- [PermissionManager.sol#L354](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L354)

- [Proposal.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L33)

- [ProposalUpgradeable.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L33)


## 9. abi.encode() is less efficient than abi.encodepacked()

- [DAO.sol#L272](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L272)


## 10. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

`buildsPerRelease` can be made into 256 bit. if the state var is not being put in the same slot with another small statevar to in one slot, it makes sense to make them into 256 bit variables so it doesnt waste gas when doing operations
- [PluginRepo.sol#L55](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55)

same with `latestRelease`
- [PluginRepo.sol#L65](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L65)


## 11. Ternary over if ... else

Using ternary operator instead of the if else statement saves gas.

- [MajorityVotingBase.sol#L570-L578](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L570-L578)
- [MajorityVotingBase.sol#L582-L590](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L582-L590)


## 12. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

- [MajorityVotingBase.sol#L392](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L392)
- [MajorityVotingBase.sol#L363](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L363)
- [MajorityVotingBase.sol#L369](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L369)
- [MajorityVotingBase.sol#L375](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L375)
- [MajorityVotingBase.sol#L352](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L352)
- [MajorityVotingBase.sol#L357](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L357)
- [MajorityVotingBase.sol#L291](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L291)

- [Multisig.sol#L305](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L305)

- [TokenVoting.sol#L61](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L61)

- [PluginRepo.sol#L209](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L209)
- [PluginRepo.sol#L217](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L217)
- [PluginRepo.sol#L244](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L244)

- [DaoAuthorizableUpgradeable.sol#L26](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L26)

- [DaoAuthorizable.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L25)

- [CounterV2.sol#L58](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L58)

- [CounterV1.sol#L39](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L39)

- [Addresslist.sol#L47](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L47)

- [GovernanceWrappedERC20.sol#L71](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L71)
- [GovernanceWrappedERC20.sol#L81](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L81)
- [GovernanceWrappedERC20.sol#L89](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L89)

- [MerkleDistributor.sol#L83](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L83)


## 13. should use arguments instead of state variable

This will save 100 gas because it gets rid of a storage read and instead uses a argument that we already have which gives the same result

switch `ens.resolver(_node)` with `_ens.resolver(_node)` to save gas
- [ENSSubdomainRegistrar.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L63)


## 14. before some functions we should check some variables for possible gas save

before transfer we should check for amount being 0 so the function doesnt run when its not gonna do anything

- [GovernanceWrappedERC20.sol#L103](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L103)

- [MerkleDistributor.sol#L77](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L77)


## 15. instead of calculating a statevar with keccak256() every time the contract is made pre calculate them before and only give the result to a constant

- [InterfaceBasedRegistry.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18)

- [ENSSubdomainRegistrar.sol#L18](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18)
- [ENSSubdomainRegistrar.sol#L22](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22)

- [Admin.sol#L23](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L23)

- [MajorityVotingBase.sol#L183](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183)

- [AddresslistVoting.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27)

- [Multisig.sol#L71](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71)

- [PluginSetupProcessor.sol#L28](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L28)
- [PluginSetupProcessor.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31)
- [PluginSetupProcessor.sol#L34](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L34)

- [PluginRepo.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49)
- [PluginRepo.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52)

- [PluginRepoRegistry.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16)

- [DAORegistry.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15)

- [MultiplyHelper.sol#L12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12)

- [CounterV2.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14)

- [CounterV1.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14)

- [MerkleMinter.sol#L24](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L24)
- [MerkleMinter.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L27)

- [PermissionManager.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L15)

- [DAO.sol#L40](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40)
- [DAO.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L43)
- [DAO.sol#L46](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L46)
- [DAO.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L49)
- [DAO.sol#L53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L53)
- [DAO.sol#L57](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L57)

- [GovernanceERC20.sol#L28](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28)

- [ProposalUpgradeable.sol#L35](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L35)


## 16. Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

See more [here](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).
you can use this [tool](https://emn178.github.io/solidity-optimize-name/) to get the optimized version for function and properties signitures 

many functions in MajorityVotingBase which makes it very wasteful if the names dont be optimzied.(its inherited many times too which makes it even more important)
- [MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)


## 17. Non-strict inequalities are cheaper than strict ones

In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).
consider replacing >= with the strict counterpart > and add `- 1` to the second side

- [MajorityVotingBase.sol#L347](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L347)
- [MajorityVotingBase.sol#L519](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L519)

- [Multisig.sol#L395](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L395)
- [Multisig.sol#L407](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L407)
- [Multisig.sol#L408](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L408)

- [PluginSetupProcessor.sol#L405](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L405)
- [PluginSetupProcessor.sol#L655](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L655)


## 18. Setting the constructor to payable

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.

- [TokenFactory.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol)
- [ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)
- [AddresslistVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol)
- [TokenVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol)
- [MultisigSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol)
- [AdminSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol)
- [DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol)
- [DAORegistry.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol)
- [PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)
- [PluginRepoFactory.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol)
- [PluginRepoRegistry.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)
- [PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol)
- [PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)
- [CounterV2PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol)
- [CounterV1PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol)
- [GovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)
- [DAO.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol)
- [GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)
- [Plugin.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/Plugin.sol)
- [PluginCloneable.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol)
- [PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)



## 19. part of the code can be pre calculated

just pre calculate the answer of this part of code and use the answer inside the code instead to avoid unnecessary gas usage. use a comment to show what it was to the readers

`RATIO_BASE - 1` can be calculated and defined as a constant to stop a operation each time the function runs.(2 instances in L528 and L530)
- [MajorityVotingBase.sol#L528-L530](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L528-L530)

`bytes4(keccak256("initialize(address,address,uint256)"))` can be pre calculated and saved as a constant or a immutable and used that way to save gas
- [CounterV2PluginSetup.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L49)

same with `bytes4(keccak256("setNewVariable(uint256)")`
- [CounterV2PluginSetup.sol#L116](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L116)

same here `keccak256("EXECUTE_PERMISSION")`
- [CounterV2PluginSetup.sol#L154](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L154)

`bytes4(keccak256("initialize(address,address,uint256)"))`
- [CounterV1PluginSetup.sol#L48](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L48)

`keccak256("EXECUTE_PERMISSION")`
- [CounterV1PluginSetup.sol#L69](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L69)
- [CounterV1PluginSetup.sol#L114](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L114)




## 20. avoid an unnecessary initialization and do not write a default value for addresses

Address x = address(0)


- [PermissionLib.sol#L10](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionLib.sol#L10)

- [PermissionManager.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L21)