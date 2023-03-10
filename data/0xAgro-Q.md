# QA Report
## Finding Summary

[**Low Severity**](#Low-Severity)
1. [**Unchecked Cast May Overflow**](#1-Unchecked-Cast-May-Overflow)
2. [**Low Test Coverage**](#2-Low-Test-Coverage)
3. [**Draft EIP Used**](#3-Draft-EIP-Used)

[**Non-Critical**](#Non-Critical)
1. [**Contracts Missing @title Tag**](#1-Contracts-Missing-title-Tag)
2. [**Inconsistent Comment Initial Spacing**](#2-Inconsistent-Comment-Initial-Spacing)
3. [**Inconsistent Named Returns**](#3-Inconsistent-Named-Returns)
4. [**bytes.concat() Can Be Used Over abi.encodePacked()**](#4-bytesconcat-can-be-used-over-abiencodepacked)
5. [**Explicit Function Scope Not Used**](#5-Explicit-Function-Scope-Not-Used)
6. [**Space Between License and Pragma**](#6-Space-Between-License-and-Pragma)
7. [**Extra Spaces In License Identifier**](#7-extra-spaces-in-license-identifier)
8. [**Inconsistent Comment Capitalization**](#8-Inconsistent-Comment-Capitalization)
9. [**Inconsistent Trailing Period**](#9-Inconsistent-Trailing-Period)
10. [**Only Single Line NatSpec Comments Used**](#10-Only-Single-Line-NatSpec-Comments-Used)
11. [**Named Returns Not Returning Named Variable**](#11-Named-Returns-Not-Returning-Named-Variable)
12. [**ERC20 Token Recovery**](#12-ERC20-Token-Recovery)
13. [**Constants Used Multiple Times**](#13-Constants-Used-Multiple-Times)

[**Style Guide Violations**](#Style-Guide-Violations)
1. [**Maximum Line Length**](#1-Maximum-Line-Length)
2. [**Order of Functions**](#2-Order-of-Functions)
3. [**Whitespace in Expressions**](#3-Whitespace-in-Expressions)
4. [**Function Declaration Style**](#4-Function-Declaration-Style)
5. [**Order of Layout**](#5-Order-of-Layout)

# Low Severity

## 1. Unchecked Cast May Overflow

As of Solidity 0.8 overflows are handled automatically; however, not for casting. For example `uint32(4294967300)` will result in `4` without reversion. Consider using a SafeCast library (which is used in other parts of the codebase).

*/packages/contracts/src/framework/utils/RegistryUtils.sol*

```solidity
17:	uint8 char = uint8(nameBytes[i]);
```

*/packages/contracts/src/plugins/governance/multisig/Multisig.sol*

```solidity
175:	uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);
414:	uint16 addresslistLength_ = uint16(addresslistLength());
```

*/packages/contracts/src/framework/plugin/repo/PluginRepo.sol*

```solidity
210:	uint16 latestBuild = uint16(buildsPerRelease[_release]);
```

*/packages/contracts/src/core/dao/DAO.sol*

```solidity
192:	if (!hasBit(_allowFailureMap, uint8(i))) {
198:	failureMap = flipBit(failureMap, uint8(i));
```

## 2. Low Test Coverage

The [Scoping Details](https://github.com/code-423n4/2023-03-aragon#scoping-details) states that the test coverage is 60%. The code coverage of the files in scope is not 100%. Consider a test coverage of 100%.

## 3. Draft EIP Used

[ERC4824](https://eips.ethereum.org/EIPS/eip-4824) is [used](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IEIP4824.sol#L8) in the codebase. EIP 4824 is in draft and could change, thus possibly impacting Aragon in the future. EIP 4824 states:

> This EIP is not yet recommended for general use or implementation, as it is subject to normative (breaking) changes.

# Non-Critical

## 1. Contracts Missing @title Tag

11 out of 62 of the contracts in scope are missing a `@title` tag. Given that 51 contracts all have a `@title` tag, consider adding one per the 11 remaining contracts.

[Proxy.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol), [UncheckedMath.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/UncheckedMath.sol), [RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol), [ENSMigration.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol), [PluginSetupProcessorHelpers.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol), [Ratio.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol), [IMerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleMinter.sol), [IMerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol), [BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol), [auth.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol), and [IGovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol) are missing a `@title` tag.

## 2. Inconsistent Comment Initial Spacing

Some comments have an initial space after `//` or `///` while others do not. It is best for code clearity to keep a consistent style. All contracts in the codebase have initial space comments. [CounterV2.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol), and [CounterV1.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol) have some non-initial space comments (`//dao.execute(...)`).

## 3. Inconsistent Named Returns

Some functions use named returns and others do not. It is best for code clearity to keep a consistent style.

1. The following contracts only have named returns (EX. `returns(uint256 foo)`): [DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol), [PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol), and [Ratio.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol).
2. The following contracts only have non-named returns (EX. `returns(uint256)`): [Proxy.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol), [UncheckedMath.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/UncheckedMath.sol), [TokenFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol), [RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol), [Admin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol), [PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol), [PluginSetupProcessorHelpers.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol), [MultiplyHelper.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol), [Addresslist.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol), [CounterV2.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol), [CounterV1.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol), [MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol), [PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol), [CallbackHandler.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol), [BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol), [PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol), [DaoAuthorizableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol), [DaoAuthorizable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol), [Plugin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol), [PluginCloneable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol), [GovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol), and [GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol).
3. The following contracts have both: [MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol), [AddresslistVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol), [AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol), [TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol), [TokenVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol), [MultisigSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol), [Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol), [AdminSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol), [PluginRepoFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol), [PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol), [CounterV2PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol), [CounterV1PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol), [MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol), [DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol), [ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol), and [Proposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol).

## 4. bytes.concat() Can Be Used Over abi.encodePacked()

Consider using `bytes.concat()` instead of `abi.encodePacked()` in contracts with Solidity version >= 0.8.4.

*/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol*

```solidity
86:	bytes32 subnode = keccak256(abi.encodePacked(node, _label));
86:	bytes32 subnode = keccak256(abi.encodePacked(node, _label));
```

*/packages/contracts/src/framework/plugin/repo/PluginRepo.sol*

```solidity
252:	return keccak256(abi.encodePacked(_tag.release, _tag.build));
252:	return keccak256(abi.encodePacked(_tag.release, _tag.build));
```

*/packages/contracts/src/plugins/token/MerkleDistributor.sol*

```solidity
105:	bytes32 node = keccak256(abi.encodePacked(_index, _to, _amount));
105:	bytes32 node = keccak256(abi.encodePacked(_index, _to, _amount));
```

*/packages/contracts/src/core/permission/PermissionManager.sol*

```solidity
347:	return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));
347:	return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));
```

## 5. Explicit Function Scope Not Used

Function scopes should be explicit and not left out if `public` (the default). The following are not explicitly deemed `public`: 

*/packages/contracts/src/utils/Proxy.sol*

* [`createERC1967Proxy`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L12).

*/packages/contracts/src/utils/UncheckedMath.sol*

* [`_uncheckedIncrement`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/UncheckedMath.sol#L8), [`_uncheckedAdd`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/UncheckedMath.sol#L19), [`_uncheckedSub`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/UncheckedMath.sol#L29).

*/packages/contracts/src/framework/utils/RegistryUtils.sol*

* [`isSubdomainValid`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L13).

*/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol*

* [`_getPluginInstallationId`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L32), [`_getPreparedSetupId`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L43), [`_getAppliedSetupId`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L67), [`hashHelpers`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L79), [`hashPermissions`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L86).

*/packages/contracts/src/plugins/utils/Ratio.sol*

* [`_applyRatioCeiled`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Ratio.sol#L17).

*/packages/contracts/src/core/utils/BitMap.sol*

* [`hasBit`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L8), [`flipBit`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L16).

*/packages/contracts/src/core/utils/auth.sol*

* [`_auth`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/auth.sol#L19).

## 6. Space Between License and Pragma

All contracts in scope have a space between the license statement and pragma ([ex](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L2)). Although it does not void the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html), all example contracts in the Style Guide do not have a space between the license statement and pragma ([ex](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#blank-lines)). This format can also be seen throughout the Solidy Documentation ([ex](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html)). Consider removing the needless space.

## 7. Extra Spaces In License Identifier

In [PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L1) there are 3 extra spaces in the license identifier. Contracts like [GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L1) do not have this extra space. Consider removing the extra spaces in [PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L1).

**Example**

*/packages/contracts/src/framework/plugin/repo/PluginRepo.sol*

```Solidity
1:	// SPDX-License-Identifier:    AGPL-3.0-or-later
```

*/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol*

```Solidity
1:	// SPDX-License-Identifier: AGPL-3.0-or-later
```

## 8. Inconsistent Comment Capitalization

There is an inconsistency in the capitalization of comments. For example, [this line](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L34) is not capitilized while - like most - [this line](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L133) is. Consider capitilizing all comments.

## 9. Inconsistent Trailing Period

Some comments like [this](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L33) have a trailing `.` but other lines like [this](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L44) do not.

Consider sticking to a single comment style for a less distracting developer experience.

## 10. Only Single Line NatSpec Comments Used

All NatSpec comments in the codebase use single line NatSpec comments through `///` like so:

```Solidity
19:	/// @title GovernanceWrappedERC20
20:	/// @author Aragon Association
21:	/// @notice Wraps an existing [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token by inheriting from `ERC20WrapperUpgradeable` and allows to use it for voting by inheriting from `ERC20VotesUpgradeable`. The latter is compatible with [OpenZepplin `Votes`](https://docs.openzeppelin.com/contracts/4.x/api/governance#Votes) interface.
22:	/// The contract also supports meta transactions. To use an `amount` of underlying tokens for voting, the token owner has to
23:	/// 1. call `approve` for the tokens to be used by this contract
24:	/// 2. call `depositFor` to wrap them, which safely transfers the underlying [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens to the contract and mints wrapped [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens.
25:	/// To get the [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens back, the owner of the wrapped tokens can call `withdrawFor`, which  burns the wrapped tokens [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens and safely transfers the underlying tokens back to the owner.
26:	/// @dev This contract intentionally has no public mint functionality because this is the responsibility of the underlying [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token contract.
```

As described in the [Solidity Documentation](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#documentation-example) multi-line comments may use the `/**` format. The use of `///` for multi-line is allowed by Solidity standards; however, for comments like [this](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L16-L96) it is far easier to read using the mulit-line `/**` style. Consider using the mulit-line `/**` NstSpec style for long NatSpec comments.

## 11. Named Returns Not Returning Named Variable

Using named returns may help developers understand what is being returned, but this should be in a `@return` tag. There is no need to confuse developers. The following functions have named returned but still return one or more variables.

*/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol*

* [`prepareInstallation`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L286), [`prepareUpdate`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L396).

*/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol*

* [`prepareInstallation`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L30).

*/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol*

* [`prepareInstallation`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L29).

*/packages/contracts/src/plugins/token/MerkleMinter.sol*

* [`merkleMint`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L74).

## 12. ERC20 Token Recovery

Consider adding a recovery function for Tokens that are accidently sent to the core contracts of the protocol. 

## 13. Constants Used Multiple Times

There is a relatively large amount of constants used in the codebase (seen [here](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40-L61) for example). In some cases like [this](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14) and [this](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12) the same constant is referenced in different contracts. Consider addings a constants contract to hold all constants used in Aragon.

# Style Guide Violations

## 1. Maximum Line Length

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

The following lines are longer than 120 characters, it is suggested to shorten these lines:

*/packages/contracts/src/utils/Proxy.sol*
*  [7](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol#L7), [11](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol#L11). 

*/packages/contracts/src/framework/utils/TokenFactory.sol*
*  [8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L8), [72](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L72), [74](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L74), [77](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L77). 

*/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol*
*  [6](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L6), [21](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L21), [42](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L42), [52](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52), [74](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L74). 

*/packages/contracts/src/framework/utils/RegistryUtils.sol*
*  [9](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L9). 

*/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol*
*  [13](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L13), [44](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L44), [49](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L49), [54](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L54), [84](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L84), [91](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L91). 

*/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol*
*  [15](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L15), [72](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72), [110](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110). 

*/packages/contracts/src/framework/utils/ens/ENSMigration.sol*
*  [2](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol#L2). 

*/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol*
*  [27](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L27), [31](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L31), [33](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L33), [34](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L34), [38](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L38), [62](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L62), [63](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L63), [69](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L69), [71](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L71), [78](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L78), [90](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L90), [95](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L95), [108](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L108), [109](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L109), [118](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L118), [119](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L119), [136](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L136), [148](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L148), [333](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L333), [428](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L428), [429](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L429), [432](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L432), [447](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L447), [527](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L527), [535](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L535), [560](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L560), [580](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580), [593](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L593). 

*/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol*
*  [16](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L16), [25](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L25), [59](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L59). 

*/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol*
*  [39](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L39). 

*/packages/contracts/src/plugins/governance/multisig/IMultisig.sol*
*  [9](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L9), [11](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L11), [15](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L15), [21](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L21), [38](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L38). 

*/packages/contracts/src/plugins/governance/multisig/Multisig.sol*
*  [33](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L33), [96](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L96), [158](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L158), [201](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L201), [203](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L203), [304](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L304). 

*/packages/contracts/src/plugins/governance/admin/Admin.sol*
*  [61](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L61). 

*/packages/contracts/src/plugins/governance/admin/AdminSetup.sol*
*  [75](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L75). 

*/packages/contracts/src/framework/dao/DAOFactory.sol*
*  [41](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L41), [50](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L50), [60](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L60), [62](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L62), [134](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L134). 

*/packages/contracts/src/framework/dao/DAORegistry.sol*
*  [72](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L72). 

*/packages/contracts/src/framework/plugin/repo/PluginRepo.sol*
*  [9](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L9), [18](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L18), [32](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L32), [255](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L255). 

*/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol*
*  [71](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L71). 

*/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol*
*  [28](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L28), [41](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L41), [44](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L44), [61](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L61), [64](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L64), [111](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L111). 

*/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol*
*  [14](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L14). 

*/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol*
*  [17](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L17), [21](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L21), [22](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L22), [37](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L37), [48](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L48), [49](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L49), [50](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L50), [57](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L57), [63](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L63), [72](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L72), [73](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L73), [85](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L85), [86](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L86), [98](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L98), [110](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L110), [111](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L111), [130](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L130), [134](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L134), [141](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L141), [158](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L158), [170](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L170), [171](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L171), [181](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L181), [213](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L213), [245](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L245), [285](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L285), [394](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L394), [395](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L395), [444](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L444), [551](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L551), [611](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L611), [646](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L646), [647](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L647), [649](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L649). 

*/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol*
*  [10](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L10), [13](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L13), [14](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L14), [20](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L20), [22](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L22), [23](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L23), [32](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L32), [44](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L44), [55](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L55), [63](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L63). 

*/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol*
*  [9](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L9), [41](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L41). 

*/packages/contracts/src/framework/plugin/setup/PluginSetup.sol*
*  [29](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L29). 

*/packages/contracts/src/plugins/utils/Addresslist.sol*
*  [22](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L22), [45](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L45). 

*/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol*
*  [31](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L31), [51](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L51), [68](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L68). 

*/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol*
*  [49](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L49). 

*/packages/contracts/src/plugins/token/MerkleDistributor.sol*
*  [128](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L128). 

*/packages/contracts/src/plugins/token/IMerkleMinter.sol*
*  [36](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L36). 

*/packages/contracts/src/plugins/token/IMerkleDistributor.sol*
*  [6](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L6). 

*/packages/contracts/src/plugins/token/MerkleMinter.sol*
*  [19](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L19), [68](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L68), [97](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L97). 

*/packages/contracts/src/core/permission/PermissionManager.sol*
*  [12](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L12), [14](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L14), [26](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L26), [53](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L53), [59](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L59), [64](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L64), [73](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L73), [85](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L85), [99](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L99), [104](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L104), [113](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L113), [118](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L118), [119](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L119), [129](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L129), [134](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L134), [192](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L192), [197](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L197), [205](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L205), [206](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L206), [207](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L207), [210](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L210), [228](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L228), [229](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L229), [281](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L281), [291](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L291), [325](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L325), [337](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L337), [353](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L353), [361](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L361). 

*/packages/contracts/src/core/permission/PermissionLib.sol*
*  [22](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol#L22), [32](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol#L32), [36](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol#L36). 

*/packages/contracts/src/core/permission/IPermissionCondition.sol*
*  [7](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/IPermissionCondition.sol#L7). 

*/packages/contracts/src/core/utils/CallbackHandler.sol*
*  [7](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L7), [28](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L28), [52](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L52). 

*/packages/contracts/src/core/utils/auth.sol*
*  [14](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol#L14). 

*/packages/contracts/src/core/dao/IDAO.sol*
*  [19](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L19), [40](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L40), [41](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L41), [43](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L43), [58](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L58), [96](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L96), [121](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L121), [127](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L127). 

*/packages/contracts/src/core/dao/DAO.sol*
*  [24](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L24), [99](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L99), [134](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L134), [260](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L260), [262](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L262), [268](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L268), [338](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L338). 

*/packages/contracts/src/core/dao/IEIP4824.sol*
*  [9](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IEIP4824.sol#L9). 

*/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol*
*  [6](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6), [15](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L15), [53](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L53), [59](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L59), [65](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L65). 

*/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol*
*  [12](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L12), [30](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L30), [37](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L37). 

*/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol*
*  [12](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L12), [29](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L29). 

*/packages/contracts/src/core/plugin/Plugin.sol*
*  [13](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L13). 

*/packages/contracts/src/core/plugin/PluginCloneable.sol*
*  [13](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L13). 

*/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol*
*  [12](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L12), [42](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L42), [69](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L69), [82](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L82). 

*/packages/contracts/src/core/plugin/proposal/Proposal.sol*
*  [12](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L12), [42](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L42), [69](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L69). 

*/packages/contracts/src/core/plugin/proposal/IProposal.sol*
*  [18](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L18). 

*/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol*
*  [6](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L6), [8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8), [10](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L10), [11](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L11), [21](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L21), [22](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L22), [24](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L24), [25](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L25), [26](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L26). 

*/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol*
*  [7](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L7), [10](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L10), [15](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L15). 

*/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol*
*  [5](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5), [7](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L7), [8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L8), [19](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L19).

## 2. Order of Functions

The Solidity Style Guide suggests the following function order: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

* [ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol): external functions are positioned after internal functions.
* [MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol): public functions are positioned after internal functions.
* [AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol): external functions are positioned after public functions.
* [TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol): external functions are positioned after public functions.
* [Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol): external functions are positioned after public functions.
* [Admin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol): external functions are positioned after public functions.
* [PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol): public functions are positioned after internal functions.
* [PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol): public functions are positioned after internal functions.
* [MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol): external functions are positioned after public functions.
* [MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol): external functions are positioned after public functions.
* [PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol): external functions are positioned after internal functions.
* [DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol): external functions are positioned after internal functions.
* [PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol): public functions are positioned after internal functions.
* [DaoAuthorizableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol): public functions are positioned after internal functions.
* [PluginCloneable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol): public functions are positioned after internal functions.
* [GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol): external functions are positioned after public functions.

## 3. Whitespace in Expressions

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#whitespace-in-expressions) recommends to *"[a]void extraneous whitespace [i]mmediately inside parenthesis, brackets or braces, with the exception of single line function declarations"*. The Style Guide also recommends against spaces before semicolons. Consider removing spaces before semicolons.

*/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol*

```solidity
130:	for (uint256 i; i < _actions.length; ) {
```

*/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol*

```solidity
124:	for (uint256 i; i < _actions.length; ) {
```

*/packages/contracts/src/plugins/governance/multisig/Multisig.sol*

```solidity
252:	for (uint256 i; i < _actions.length; ) {
```

*/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol*

```solidity
674:	} catch (bytes memory ) {
686:	} catch (bytes memory ) {
```

*/packages/contracts/src/plugins/utils/Addresslist.sol*

```solidity
60:	for (uint256 i; i < _newAddresses.length; ) {
78:	for (uint256 i; i < _exitingAddresses.length; ) {
```

*/packages/contracts/src/core/permission/PermissionManager.sol*

```solidity
150:	for (uint256 i; i < items.length; ) {
170:	for (uint256 i; i < _items.length; ) {
```

*/packages/contracts/src/core/dao/DAO.sol*

```solidity
184:	for (uint256 i = 0; i < _actions.length; ) {
```

## 4. Function Declaration Style

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#function-declaration) states that "[f]or short function declarations, it is recommended for the opening brace of the function body to be kept on the same line as the function declaration". It is not clear what length is exactly meant by "short" or "long". The [maximum line length](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) of 120 characters may be an indication, and in that case any expanded function declaration under 120 characters should be on a single line.

The following functions in their respective contracts are not compliant by this standard: 

*/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol*
* [`_authorizeUpgrade`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L54). 

*/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol*
* [`_authorizeUpgrade`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L74), [`setDefaultResolver`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L100). 

*/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol*
* [`__MajorityVotingBase_init`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L238), [`vote`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L265), [`getVoteOption`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L291), [`canVote`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L299), [`isSupportThresholdReachedEarly`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L324). 

*/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol*
* [`addAddresses`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L59), [`removeAddresses`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L69). 

*/packages/contracts/src/plugins/governance/multisig/Multisig.sol*
* [`addAddresses`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L153), [`removeAddresses`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L172). 

*/packages/contracts/src/framework/dao/DAORegistry.sol*
* [`initialize`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L37), [`register`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L50). 

*/packages/contracts/src/framework/plugin/repo/PluginRepo.sol*
* [`_authorizeUpgrade`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L257). 

*/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol*
* [`createPluginRepo`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L31). 

*/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol*
* [`validatePreparedSetupId`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L650), [`_upgradeProxy`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L664). 

*/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol*
* [`_getAppliedSetupId`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L67), [`hashPermissions`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L86). 

*/packages/contracts/src/framework/plugin/setup/PluginSetup.sol*
* [`createERC1967Proxy`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L33). 

*/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol*
* [`multiply`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L16). 

*/packages/contracts/src/plugins/utils/Addresslist.sol*
* [`isListedAtBlock`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L30). 

*/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol*
* [`initialize`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L26). 

*/packages/contracts/src/plugins/token/MerkleDistributor.sol*
* [`initialize`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L46), [`claim`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L66). 

*/packages/contracts/src/core/permission/PermissionManager.sol*
* [`grant`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L105), [`revoke`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L135), [`isPermissionRestrictedForAnyAddr`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L354). 

*/packages/contracts/src/core/utils/CallbackHandler.sol*
* [`_handleCallback`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L31). 

*/packages/contracts/src/core/utils/auth.sol*
* [`_auth`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/auth.sol#L19). 

*/packages/contracts/src/core/dao/DAO.sol*
* [`isPermissionRestrictedForAnyAddr`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L122), [`setMetadata`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L161), [`deposit`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L218). 

*/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol*
* [`_authorizeUpgrade`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L61). 

*/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol*
* [`initialize`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L46), [`decimals`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L71), [`_mint`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L112), [`_burn`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L120). 

## 5. Order of Layout

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [TokenFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol): State is positioned after Events.
* [IDAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol): Events are positioned after Functions.
* [PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol): State is positioned after a Function.
* [DaoAuthorizableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol): Modifier is positioned after Functions.
* [DaoAuthorizable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol): Modifier is positioned after Functions.

The following are also techically not compliant; however, understandably use a `__gap` for reserved space (compliant otherwise):

* [ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol), [MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol), [TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol), [Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol), [DAORegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol), [PluginRepoRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol), [Addresslist.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol), [CounterV2.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol), [CounterV1.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol), [MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol), [MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol), [PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol), [DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol), [ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol), [InterfaceBasedRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol), [AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol), [CallbackHandler.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol).