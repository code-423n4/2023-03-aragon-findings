## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Draft Import Dependencies | 3 |
| [LOW&#x2011;2](#LOW&#x2011;2) | `decimals()` not part of ERC20 standard | 1 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Possible rounding issue | 5 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Minting tokens to the zero address should be avoided | 2 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Missing Contract-existence Checks Before Low-level Calls | 1 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Contracts are not using their OZ Upgradeable counterparts | 20 |
| [LOW&#x2011;7](#LOW&#x2011;7) | No Storage Gap For Upgradeable Contracts | 4 |

Total: 36 contexts over 7 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Add a timelock to critical functions | 8 |
| [NC&#x2011;2](#NC&#x2011;2) | Constants Should Be Defined Rather Than Using Magic Numbers | 2 |
| [NC&#x2011;3](#NC&#x2011;3) | Constants in comparisons should appear on the left side | 3 |
| [NC&#x2011;4](#NC&#x2011;4) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 29 |
| [NC&#x2011;5](#NC&#x2011;5) | Critical Changes Should Use Two-step Procedure | 8 |
| [NC&#x2011;6](#NC&#x2011;6) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 2 |
| [NC&#x2011;7](#NC&#x2011;7) | Large or complicated code bases should implement fuzzing tests | 1 |
| [NC&#x2011;8](#NC&#x2011;8) | Imports can be grouped together | 82 |
| [NC&#x2011;9](#NC&#x2011;9) | NatSpec return parameters should be included in contracts | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | Initial value check is missing in Set Functions | 7 |
| [NC&#x2011;11](#NC&#x2011;11) | Contracts should have full test coverage | 1 |
| [NC&#x2011;12](#NC&#x2011;12) | Lines are too long | 173 |
| [NC&#x2011;13](#NC&#x2011;13) | Missing event for critical parameter change | 4 |
| [NC&#x2011;14](#NC&#x2011;14) | Implementation contract may not be initialized | 21 |
| [NC&#x2011;15](#NC&#x2011;15) | Non-usage of specific imports | 4 |
| [NC&#x2011;16](#NC&#x2011;16) | Use a more recent version of Solidity | 62 |
| [NC&#x2011;17](#NC&#x2011;17) | Omissions in Events | 1 |
| [NC&#x2011;18](#NC&#x2011;18) | Empty blocks should be removed or emit something | 11 |
| [NC&#x2011;19](#NC&#x2011;19) | Use `bytes.concat()` | 4 |
| [NC&#x2011;20](#NC&#x2011;20) | Implement some type of version counter that will be incremented automatically for contract upgrades | 5 |

Total: 429 contexts over 20 issues

## Low Risk Issues


### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Draft Import Dependencies
Contracts are importing draft dependencies. These imported contracts are still a draft and are not considered ready for mainnet use and have not received adequate security auditing or are liable to change with future development.

#### <ins>Proof Of Concept</ins>


```solidity
6: import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6

```solidity
5: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5

```solidity
8: import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8



#### <ins>Recommended Mitigation Steps</ins>

Stop using draft imported contracts and use their release version if it exists.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> `decimals()` not part of ERC20 standard

`decimals()` is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

#### <ins>Proof Of Concept</ins>


```solidity
77: return ERC20WrapperUpgradeable.decimals()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L77




### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Possible rounding issue

Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

#### <ins>Proof Of Concept</ins>

The function `_applyRatioCeiled` states that "_ratio The ratio that must be in the interval `[0, 10**6]`." but there is no actual limitation to the input of `_ratio` and any `uint` can be set between 0 to 10**6.

```solidity
24: result = _value / RATIO_BASE;

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol#L24






### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Minting tokens to the zero address should be avoided

The core function `mint` is used by users to mint an option position by providing a token as collateral and borrowing the max amount of liquidity. `Address(0)` check is missing in both this function and the internal function `_mint`, which is triggered to mint the tokens to the to address. Consider applying a check in the function to ensure tokens aren't minted to the zero address.


#### <ins>Proof Of Concept</ins>


```solidity

function mint(address to, uint256 amount) external override auth(MINT_PERMISSION_ID) {
        _mint(to, amount);
    }

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L106


```solidity
    function _mint(address account, uint256 amount) internal virtual override {
        super._mint(account, amount);
        require(totalSupply() <= _maxSupply(), "ERC20Votes: total supply risks overflowing votes");

        _writeCheckpoint(_totalSupplyCheckpoints, _add, amount);
    }
```
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC20/extensions/ERC20VotesUpgradeable.sol#L185





### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```solidity
186: (bool success, bytes memory response) = to.call{value: _actions[i].value}(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L186




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
14: import "@openzeppelin/contracts/interfaces/IERC1271.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L14

```solidity
5: import {ERC165} from "@openzeppelin/contracts/utils/introspection/ERC165.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L5

```solidity
5: import {Context} from "@openzeppelin/contracts/utils/Context.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L5

```solidity
5: import {Counters} from "@openzeppelin/contracts/utils/Counters.sol";
6: import {ERC165} from "@openzeppelin/contracts/utils/introspection/ERC165.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L5-L6

```solidity
5: import {ERC165} from "@openzeppelin/contracts/utils/introspection/ERC165.sol";
6: import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";
7: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L5-L7

```solidity
5: import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L5

```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";
6: import {Address} from "@openzeppelin/contracts/utils/Address.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L5-L6

```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L5

```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L5

```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L5

```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";
6: import {Address} from "@openzeppelin/contracts/utils/Address.sol";
7: import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L5-L7

```solidity
10: import {MerkleProof} from "@openzeppelin/contracts/utils/cryptography/MerkleProof.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L10

```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L5

```solidity
5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol#L5



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> No storage gap for upgradeable contract might lead to storage slot collision

For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts.

Refer to the bottom part of this article: https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable

#### <ins>Proof Of Concept</ins>

However, the contract doesn't contain a storage gap. The storage gap is essential for upgradeable contract because "It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments". See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```solidity
contract GovernanceERC20 is
    IERC20MintableUpgradeable,
    Initializable,
    ERC165Upgradeable,
    ERC20VotesUpgradeable,
    DaoAuthorizableUpgradeable
{
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#20

```solidity
contract MultiplyHelper is PluginUUPSUpgradeable {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L10

```solidity
contract Admin is IMembership, PluginCloneable, ProposalUpgradeable {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L15

```solidity
contract GovernanceWrappedERC20 is
    IGovernanceWrappedERC20,
    Initializable,
    ERC165Upgradeable,
    ERC20VotesUpgradeable,
    ERC20WrapperUpgradeable
{
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#27



#### <ins>Recommended Mitigation Steps</ins>
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
```solidity
    uint256[50] private __gap;
```



## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). Consider adding a timelock to the following functions:

#### <ins>Proof Of Concept</ins>


```solidity
139: function setTrustedForwarder(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L139

```solidity
161: function setMetadata(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L161

```solidity
239: function setSignatureValidator(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L239

```solidity
326: function setDaoURI(string calldata newDaoURI) external auth(SET_METADATA_PERMISSION_ID) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L326

```solidity
144: function setupBases() private {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L144

```solidity
100: function setDefaultResolver(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L100

```solidity
52: function setNewVariable(uint256 _newVariable) external reinitializer(2) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L52

```solidity
53: function changeDistributorBase(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L53









### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Constants Should Be Defined Rather Than Using Magic Numbers

#### <ins>Proof Of Concept</ins>


```solidity
540: if (_votingSettings.minDuration < 60 minutes) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L540

```solidity
544: if (_votingSettings.minDuration > 365 days) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L544





### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Constants in comparisons should appear on the left side

Doing so will prevent <a href="https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html">typo bugs</a>

#### <ins>Proof Of Concept</ins>

```solidity
30: if (char == 45) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L30

```solidity
87: if (totalVotingPower_ == 0) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L87

```solidity
220: if (_startDate == 0) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L220






### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>

```solidity
40: bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L40

```solidity
43: bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L43

```solidity
46: bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L46

```solidity
49: bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
        keccak256("SET_TRUSTED_FORWARDER_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L49

```solidity
53: bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
        keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L53

```solidity
57: bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
        keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L57

```solidity
15: bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L15

```solidity
35: bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35

```solidity
15: bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15

```solidity
49: bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49

```solidity
52: bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52

```solidity
16: bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
        keccak256("REGISTER_PLUGIN_REPO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16

```solidity
27: bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
        keccak256("APPLY_INSTALLATION_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27

```solidity
31: bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31

```solidity
34: bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
        keccak256("APPLY_UNINSTALLATION_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L34

```solidity
39: bytes32 private constant EMPTY_ARRAY_HASH =
        0x569e75fc77c1a856f6daaf9e69d8a9566ca34aa47f9133711ce065a571af0cfd;

    /// @notice The hash obtained from the bytes-encoded zero value.
    /// @dev The hash is computed via `keccak256(abi.encode(0))`.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L39

```solidity
18: bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
        keccak256("UPGRADE_REGISTRY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18

```solidity
18: bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
        keccak256("UPGRADE_REGISTRAR_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18

```solidity
22: bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
        keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22

```solidity
12: bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12

```solidity
14: bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14

```solidity
14: bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14

```solidity
23: bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
        keccak256("EXECUTE_PROPOSAL_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L23

```solidity
183: bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
        keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183

```solidity
27: bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
        keccak256("UPDATE_ADDRESSES_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27

```solidity
71: bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
        keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71

```solidity
24: bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L24

```solidity
27: bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
        keccak256("CHANGE_DISTRIBUTOR_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L27

```solidity
28: bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
139: function setTrustedForwarder(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L139

```solidity
161: function setMetadata(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L161

```solidity
239: function setSignatureValidator(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L239

```solidity
326: function setDaoURI(string calldata newDaoURI) external auth(SET_METADATA_PERMISSION_ID) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L326

```solidity
144: function setupBases() private {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L144

```solidity
100: function setDefaultResolver(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L100

```solidity
52: function setNewVariable(uint256 _newVariable) external reinitializer(2) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L52

```solidity
53: function changeDistributorBase(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L53



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.







#### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
673: revert(reason);
685: revert(reason);

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L673

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L685












### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Large or complicated code bases should implement fuzzing tests

Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement <a href="https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05">fuzzing tests</a>. Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

#### <ins>Proof Of Concept</ins>

Various in-scope contract files.




### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Imports can be grouped together

Consider importing OZ first, then all interfaces, then all utils.

#### <ins>Proof Of Concept</ins>


```solidity
5: import "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165StorageUpgradeable.sol";
6: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
7: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
8: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
9: import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";
10: import "@openzeppelin/contracts-upgradeable/token/ERC721/IERC721ReceiverUpgradeable.sol";
11: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";
12: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155ReceiverUpgradeable.sol";
13: import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";
14: import "@openzeppelin/contracts/interfaces/IERC1271.sol";
16: import {PermissionManager} from "../permission/PermissionManager.sol";
17: import {CallbackHandler} from "../utils/CallbackHandler.sol";
18: import {hasBit, flipBit} from "../utils/BitMap.sol";
19: import {IEIP4824} from "./IEIP4824.sol";
20: import {IDAO} from "./IDAO.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L6

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L7

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L8

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L9

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L10

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L11

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L12

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L13

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L14

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L16

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L17

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L18

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L19

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L20




```solidity
5: import {DAO} from "../../core/dao/DAO.sol";
6: import {PermissionLib} from "../../core/permission/PermissionLib.sol";
7: import {createERC1967Proxy} from "../../utils/Proxy.sol";
8: import {PluginRepo} from "../plugin/repo/PluginRepo.sol";
9: import {PluginSetupProcessor} from "../plugin/setup/PluginSetupProcessor.sol";
10: import {hashHelpers, PluginSetupRef} from "../plugin/setup/PluginSetupProcessorHelpers.sol";
11: import {IPluginSetup} from "../plugin/setup/IPluginSetup.sol";
12: import {DAORegistry} from "./DAORegistry.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L5-L12

```solidity
5: import {IDAO} from "../../core/dao/IDAO.sol";
6: import {ENSSubdomainRegistrar} from "../utils/ens/ENSSubdomainRegistrar.sol";
7: import {InterfaceBasedRegistry} from "../utils/InterfaceBasedRegistry.sol";
8: import {isSubdomainValid} from "../utils/RegistryUtils.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L5-L8


```solidity
5: import {IDAO} from "../../../core/dao/IDAO.sol";
6: import {ENSSubdomainRegistrar} from "../../utils/ens/ENSSubdomainRegistrar.sol";
7: import {InterfaceBasedRegistry} from "../../utils/InterfaceBasedRegistry.sol";
8: import {isSubdomainValid} from "../../utils/RegistryUtils.sol";
9: import {IPluginRepo} from "./IPluginRepo.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L5-L9

```solidity
5: import {ERC165} from "@openzeppelin/contracts/utils/introspection/ERC165.sol";
6: import {ERC165Checker} from "@openzeppelin/contracts/utils/introspection/ERC165Checker.sol";
7: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";
9: import {PermissionLib} from "../../../core/permission/PermissionLib.sol";
10: import {createERC1967Proxy as createERC1967} from "../../../utils/Proxy.sol";
11: import {IPluginSetup} from "./IPluginSetup.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L6

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L7

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L9

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L10

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L11



```solidity
5: import "@ensdomains/ens-contracts/contracts/registry/ENS.sol";
6: import "@ensdomains/ens-contracts/contracts/resolvers/Resolver.sol";
8: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
10: import {DaoAuthorizableUpgradeable} from "../../../core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol";
11: import {IDAO} from "../../../core/dao/IDAO.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L6

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L8

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L10

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L11



```solidity
5: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";
6: import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
7: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";
9: import {IProposal} from "../../../core/plugin/proposal/IProposal.sol";
10: import {ProposalUpgradeable} from "../../../core/plugin/proposal/ProposalUpgradeable.sol";
11: import {PluginUUPSUpgradeable} from "../../../core/plugin/PluginUUPSUpgradeable.sol";
12: import {IDAO} from "../../../core/dao/IDAO.sol";
13: import {RATIO_BASE, RatioOutOfBounds} from "../../utils/Ratio.sol";
14: import {IMajorityVoting} from "./IMajorityVoting.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L6

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L7

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L9

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L10

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L11

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L12

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L13

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L14



```solidity
5: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";
7: import {IDAO} from "../../../../core/dao/IDAO.sol";
8: import {RATIO_BASE, _applyRatioCeiled} from "../../../utils/Ratio.sol";
10: import {IMembership} from "../../../../core/plugin/membership/IMembership.sol";
11: import {Addresslist} from "../../../utils/Addresslist.sol";
12: import {IMajorityVoting} from "../IMajorityVoting.sol";
13: import {MajorityVotingBase} from "../MajorityVotingBase.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L7

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L8

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L10

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L11

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L12

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L13



```solidity
5: import {IVotesUpgradeable} from "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";
6: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";
8: import {IMembership} from "../../../../core/plugin/membership/IMembership.sol";
9: import {IDAO} from "../../../../core/dao/IDAO.sol";
10: import {RATIO_BASE, _applyRatioCeiled} from "../../../utils/Ratio.sol";
11: import {MajorityVotingBase} from "../MajorityVotingBase.sol";
12: import {IMajorityVoting} from "../IMajorityVoting.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L6

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L8

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L9

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L10

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L11

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L12



```solidity
5: import {SafeCastUpgradeable} from "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";
7: import {IDAO} from "../../../core/dao/IDAO.sol";
8: import {IMembership} from "../../../core/plugin/membership/IMembership.sol";
9: import {PluginUUPSUpgradeable} from "../../../core/plugin/PluginUUPSUpgradeable.sol";
11: import {ProposalUpgradeable} from "../../../core/plugin/proposal/ProposalUpgradeable.sol";
12: import {Addresslist} from "../../utils/Addresslist.sol";
13: import {IMultisig} from "./IMultisig.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L7

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L8

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L9

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L11

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L12

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L13



```solidity
5: import {Clones} from "@openzeppelin/contracts/proxy/Clones.sol";
6: import {ERC165Upgradeable} from "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165Upgradeable.sol";
7: import {IERC20Upgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";
9: import {IDAO} from "../../core/dao/IDAO.sol";
10: import {PluginUUPSUpgradeable} from "../../core/plugin/PluginUUPSUpgradeable.sol";
11: import {IERC20MintableUpgradeable} from "../../token/ERC20/IERC20MintableUpgradeable.sol";
12: import {createERC1967Proxy} from "../../utils/Proxy.sol";
13: import {IMerkleDistributor} from "./IMerkleDistributor.sol";
14: import {MerkleDistributor} from "./MerkleDistributor.sol";
15: import {IMerkleMinter} from "./IMerkleMinter.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L5

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L6

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L7

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L9

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L10

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L11

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L12

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L13

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L14

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L15










### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> NatSpec return parameters should be included in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
#### <ins>Proof Of Concept</ins>


```solidity
    function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {
        // create dao
        dao = DAO(payable(createERC1967Proxy(daoBase, bytes(""))));

        // initialize the DAO and give the `ROOT_PERMISSION_ID` permission to this contract.
        dao.initialize(
            _daoSettings.metadata,
            address(this),
            _daoSettings.trustedForwarder,
            _daoSettings.daoURI
        );
    }
```
https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L141


#### <ins>Recommended Mitigation Steps</ins>

Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)

```solidity
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```



### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Initial value check is missing in Set Functions

A check regarding whether the current value and the new value are the same should be added

#### <ins>Proof Of Concept</ins>

```solidity
139: function setTrustedForwarder(
        address _newTrustedForwarder
    ) external override auth(SET_TRUSTED_FORWARDER_PERMISSION_ID) {
        _setTrustedForwarder(_newTrustedForwarder);
    }
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L139

```solidity
161: function setMetadata(
        bytes calldata _metadata
    ) external override auth(SET_METADATA_PERMISSION_ID) {
        _setMetadata(_metadata);
    }
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L161

```solidity
239: function setSignatureValidator(
        address _signatureValidator
    ) external override auth(SET_SIGNATURE_VALIDATOR_PERMISSION_ID) {
        signatureValidator = IERC1271(_signatureValidator);

        emit SignatureValidatorSet({signatureValidator: _signatureValidator});
    }
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L239

```solidity
326: function setDaoURI(string calldata newDaoURI) external auth(SET_METADATA_PERMISSION_ID) {
        _setDaoURI(newDaoURI);
    }
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L326

```solidity
144: function setupBases() private {
        distributorBase = new MerkleDistributor();
        governanceERC20Base = address(
            new GovernanceERC20(
                IDAO(address(0)),
                "baseName",
                "baseSymbol",
                GovernanceERC20.MintSettings(new address[](0), new uint256[](0))
            )
        );
        governanceWrappedERC20Base = address(
            new GovernanceWrappedERC20(IERC20Upgradeable(address(0)), "baseName", "baseSymbol")
        );
        merkleMinterBase = address(new MerkleMinter());
    }
}
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L144


```solidity
52: function setNewVariable(uint256 _newVariable) external reinitializer(2) {
        newVariable = _newVariable;
    }
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L52

```solidity
53: function changeDistributorBase(
        IMerkleDistributor _distributorBase
    ) external override auth(CHANGE_DISTRIBUTOR_PERMISSION_ID) {
        distributorBase = _distributorBase;
    }
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L53






### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Contracts should have full test coverage

The test coverage rate of the project is 80%. Testing all functions is best practice in terms of security criteria.
While 80% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

As stated in the documentation of the contest that it is 60% but in actuality it is 80% when running the tests:

```js
- What is the overall line coverage percentage provided by your tests?:  60
```
Due to its capacity, test coverage is expected to be 100%.



### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```solidity
134: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L134

```solidity
262: /// Gas cost increases in future hard forks might break this function. As an alternative, EIP-2930-type transactions using access lists can be employed.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L262

```solidity
268: /// @param _input An alias being equivalent to `msg.data`. This feature of the fallback function was introduced with the [solidity compiler version 0.7.6](https://github.com/ethereum/solidity/releases/tag/v0.7.6)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L268

```solidity
338: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L338

```solidity
19: /// @notice Checks if an address has permission on a contract via a permission identifier and considers if `ANY_ADDRESS` was used in the granting process.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L19

```solidity
40: /// @notice Executes a list of actions. If no failure map is provided, one failing action results in the entire excution to be reverted. If a non-zero failure map is provided, allowed actions can fail without the remaining actions being reverted.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L40

```solidity
41: /// @param _callId The ID of the call. The definition of the value of `callId` is up to the calling contract and can be used, e.g., as a nonce.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L41

```solidity
43: /// @param _allowFailureMap A bitmap allowing execution to succeed, even if individual actions might revert. If the bit at index `i` is 1, the execution succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L43

```solidity
58: /// @dev The value of `callId` is defined by the component/contract calling the execute function. A `Plugin` implementation can use it, for example, as a nonce.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L58

```solidity
96: /// @dev This event is intended to be emitted in the `receive` function and is therefore bound by the gas limitations for `send`/`transfer` calls introduced by [ERC-2929](https://eips.ethereum.org/EIPS/eip-2929).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L96

```solidity
121: /// @notice Checks whether a signature is valid for the provided hash by forwarding the call to the set [ERC-1271](https://eips.ethereum.org/EIPS/eip-1271) signature validator contract.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L121

```solidity
127: /// @notice Registers an ERC standard having a callback by registering its [ERC-165](https://eips.ethereum.org/EIPS/eip-165) interface ID and callback function signature.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L127

```solidity
9: /// @notice A distinct Uniform Resource Identifier (URI) pointing to a JSON object following the "EIP-4824 DAO JSON-LD Schema". This JSON file splits into four URIs: membersURI, proposalsURI, activityLogURI, and governanceURI. The membersURI should point to a JSON file that conforms to the "EIP-4824 Members JSON-LD Schema". The proposalsURI should point to a JSON file that conforms to the "EIP-4824 Proposals JSON-LD Schema". The activityLogURI should point to a JSON file that conforms to the "EIP-4824 Activity Log JSON-LD Schema". The governanceURI should point to a flatfile, normatively a .md file. Each of the JSON files named above can be statically-hosted or dynamically-generated.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IEIP4824.sol#L9

```solidity
7: /// @notice This interface can be implemented to support more customary permissions depending on on- or off-chain state, e.g., by querying token ownershop or a secondary condition, respectively.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/IPermissionCondition.sol#L7

```solidity
36: /// @param condition The `PermissionCondition` that will be asked for authorization on calls connected to the specified permission identifier.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol#L36

```solidity
26: /// @notice A mapping storing permissions as hashes (i.e., `permissionHash(where, who, permissionId)`) and their status encoded by an address (unset, allowed, or redirecting to a `PermissionCondition`).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L26

```solidity
85: /// @notice A modifier to make functions on inheriting contracts authorized. Permissions to call the function are checked through this permission manager.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L85

```solidity
99: /// @notice Grants permission to an address to call methods in a contract guarded by an auth modifier with the specified permission identifier.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L99

```solidity
104: /// @dev Note, that granting permissions with `_who` or `_where` equal to `ANY_ADDR` does not replace other permissions with specific `_who` and `_where` addresses that exist in parallel.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L104

```solidity
119: /// @dev Note, that granting permissions with `_who` or `_where` equal to `ANY_ADDR` does not replace other permissions with specific `_who` and `_where` addresses that exist in parallel.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L119

```solidity
229: /// @dev Note, that granting permissions with `_who` or `_where` equal to `ANY_ADDR` does not replace other permissions with specific `_who` and `_where` addresses that exist in parallel.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L229

```solidity
113: /// @notice Grants permission to an address to call methods in a target contract guarded by an auth modifier with the specified permission identifier if the referenced condition permits it.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L113

```solidity
118: /// @param _condition The `PermissionCondition` that will be asked for authorization on calls connected to the specified permission identifier.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L118

```solidity
129: /// @notice Revokes permission from an address to call methods in a target contract guarded by an auth modifier with the specified permission identifier.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L129

```solidity
134: /// @dev Note, that revoking permissions with `_who` or `_where` equal to `ANY_ADDR` does not revoke other permissions with specific `_who` and `_where` addresses that exist in parallel.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L134

```solidity
192: /// @notice Checks if an address has permission on a contract via a permission identifier and considers if `ANY_ADDRESS` was used in the granting process.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L192

```solidity
228: /// @param _condition An address either resolving to a `PermissionCondition` contract address or being the `ALLOW_FLAG` address (`address(2)`).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L228

```solidity
281: /// @dev Note, that revoking permissions with `_who` or `_where` equal to `ANY_ADDR` does not revoke other permissions with specific `_who` and `_where` addresses that might have been granted in parallel.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L281

```solidity
291: /// @notice Checks if a caller is granted permissions on a target contract via a permission identifier and redirects the approval to a `PermissionCondition` if this was specified in the setup.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L291

```solidity
337: /// @notice Generates the hash for the `permissionsHashed` mapping obtained from the word "PERMISSION", the contract address, the address owning the permission, and the permission identifier.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L337

```solidity
353: /// @dev By default, every permission is unrestricted and it is the derived contract's responsibility to override it. Note, that the `ROOT_PERMISSION_ID` is included not required to be set it again.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L353

```solidity
361: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L361

```solidity
13: /// @notice An abstract, non-upgradeable contract to inherit from when creating a plugin being deployed via the minimal clones pattern (see [ERC-1167](https://eips.ethereum.org/EIPS/eip-1167)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L13

```solidity
15: /// @notice An abstract, upgradeable contract to inherit from when creating a plugin being deployed via the UUPS pattern (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L15

```solidity
53: /// @notice Returns the address of the implementation contract in the [proxy storage slot](https://eips.ethereum.org/EIPS/eip-1967) slot the [UUPS proxy](https://eips.ethereum.org/EIPS/eip-1822) is pointing to.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L53

```solidity
59: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L59

```solidity
65: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L65

```solidity
12: /// @notice An abstract contract providing a meta-transaction compatible modifier for non-upgradeable contracts instantiated via the `new` keyword to authorize function calls through an associated DAO.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L12

```solidity
29: /// @notice A modifier to make functions on inheriting contracts authorized. Permissions to call the function are checked through the associated DAO's permission manager.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L29

```solidity
12: /// @notice An abstract contract providing a meta-transaction compatible modifier for upgradeable or cloneable contracts to authorize function calls through an associated DAO.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L12

```solidity
30: /// @notice A modifier to make functions on inheriting contracts authorized. Permissions to call the function are checked through the associated DAO's permission manager.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L30

```solidity
37: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L37

```solidity
18: /// @param allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L18

```solidity
12: /// @notice An abstract contract containing the traits and internal functionality to create and execute proposals that can be inherited by non-upgradeable DAO plugins.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L12

```solidity
42: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L42

```solidity
69: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L69

```solidity
12: /// @notice An abstract contract containing the traits and internal functionality to create and execute proposals that can be inherited by upgradeable DAO plugins.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L12

```solidity
42: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L42

```solidity
69: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L69

```solidity
82: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L82

```solidity
14: /// @notice A free function checking if a caller is granted permissions on a target contract via a permission identifier that redirects the approval to a `PermissionCondition` if this was specified in the setup.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol#L14

```solidity
7: /// @notice This contract handles callbacks by registering a magic number together with the callback function's selector. It provides the `_handleCallback` function that inherting have to call inside their `fallback()` function  (`_handleCallback(msg.callbackSelector, msg.data)`).  This allows to adaptively register ERC standards (e.g., [ERC-721](https://eips.ethereum.org/EIPS/eip-721), [ERC-1115](https://eips.ethereum.org/EIPS/eip-1155), or future versions of [ERC-165](https://eips.ethereum.org/EIPS/eip-165)) and returning the required magic numbers for the associated callback functions for the inheriting contract so that it doesn't need to be upgraded.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L7

```solidity
28: /// @dev This function is supposed to be called via `_handleCallback(msg.sig, msg.data)` in the `fallback()` function of the inheriting contract.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L28

```solidity
52: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L52

```solidity
41: /// @param data The bytes-encoded data containing the input parameters for the installation as specified in the plugin's build metadata JSON file.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L41

```solidity
72: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L72

```solidity
14: /// @notice Creates a new plugin version as the latest build for an existing release number or the first build for a new release number for the provided `PluginSetup` contract address and metadata.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L14

```solidity
255: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L255

```solidity
41: /// @param _maintainer The maintainer of the plugin repo. This address has permission to update metadata, upgrade the repo logic, and manage the repo permissions.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L41

```solidity
44: /// @dev After the creation of the `PluginRepo` and release of the first version by the factory, ownership is transferred to the `_maintainer` address.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L44

```solidity
61: /// @notice Set the final permissions for the published plugin repository maintainer. All permissions are revoked from the plugin factory and granted to the specified plugin maintainer.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L61

```solidity
111: /// @notice Internal method creating a `PluginRepo` via the [ERC-1967](https://eips.ethereum.org/EIPS/eip-1967) proxy pattern from the provided base contract and registering it in the Aragon plugin registry.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L111

```solidity
71: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L71

```solidity
10: /// @notice The interface required for a plugin setup contract to be consumed by the `PluginSetupProcessor` for plugin installations, updates, and uninstallations.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L10

```solidity
14: /// @param permissions The array of multi-targeted permission operations to be applied by the `PluginSetupProcessor` to the installing or updating DAO.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L14

```solidity
20: /// @notice The payload for plugin updates and uninstallations containing the existing contracts as well as optional data to be consumed by the plugin setup.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L20

```solidity
23: /// @param data The bytes-encoded data containing the input parameters for the preparation of update/uninstall as specified in the corresponding ABI on the version's metadata.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L23

```solidity
32: /// @param _data The bytes-encoded data containing the input parameters for the installation as specified in the plugin's build metadata JSON file.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L32

```solidity
55: /// @return permissions The array of multi-targeted permission operations to be applied by the `PluginSetupProcessor` to the uninstalling DAO.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L55

```solidity
63: /// @dev The implementation can be instantiated via the `new` keyword, cloned via the minimal clones pattern (see [ERC-1167](https://eips.ethereum.org/EIPS/eip-1167)), or proxied via the UUPS pattern (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L63

```solidity
29: /// @notice A convenience function to create an [ERC-1967](https://eips.ethereum.org/EIPS/eip-1967) proxy contract pointing to an implementation and being associated to a DAO.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L29

```solidity
21: /// @notice This contract processes the preparation and application of plugin setups (installation, update, uninstallation) on behalf of a requesting DAO.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L21

```solidity
22: /// @dev This contract is temporarily granted the `ROOT_PERMISSION_ID` permission on the applying DAO and therefore is highly security critical.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L22

```solidity
37: /// @notice The hash obtained from the bytes-encoded empty array to be used for UI updates being required to submit an empty permission array.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L37

```solidity
49: /// @param currentAppliedSetupId The current setup id that plugin holds. Needed to confirm that `prepareUpdate` or `prepareUninstallation` happens for the plugin's current/valid dependencies.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L49

```solidity
50: /// @param preparedSetupIdToBlockNumber The mapping between prepared setup IDs and block numbers at which `prepareInstallation`, `prepareUpdate` or `prepareUninstallation` was executed.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L50

```solidity
63: /// @param data The bytes-encoded data containing the input parameters for the installation preparation as specified in the corresponding ABI on the version's metadata.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L63

```solidity
85: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L85

```solidity
110: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L110

```solidity
213: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L213

```solidity
245: /// @param setupPayload The payload containing the plugin and helper contract addresses deployed in a preparation step as well as optional data to be consumed by the plugin setup.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L245

```solidity
86: ///  This includes the bytes-encoded data containing the input parameters for the update preparation as specified in the corresponding ABI on the version's metadata.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L86

```solidity
111: ///  This includes the bytes-encoded data containing the input parameters for the uninstallation preparation as specified in the corresponding ABI on the version's metadata.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L111

```solidity
134: /// @dev This is thrown if the `APPLY_INSTALLATION_PERMISSION_ID`, `APPLY_UPDATE_PERMISSION_ID`, or APPLY_UNINSTALLATION_PERMISSION_ID is missing.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L134

```solidity
158: /// @notice Thrown if a prepared setup ID is not eligible to be applied. This can happen if another setup has been already applied or if the setup wasn't prepared in the first place.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L158

```solidity
181: /// @param data The bytes-encoded data containing the input parameters for the preparation as specified in the corresponding ABI on the version's metadata.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L181

```solidity
395: /// @dev The list of `_params.setupPayload.currentHelpers` has to be specified in the same order as they were returned from previous setups preparation steps (the latest `prepareInstallation` or `prepareUpdate` step that has happend) on which the update is prepared for.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L395

```solidity
551: /// @dev The list of `_params.setupPayload.currentHelpers` has to be specified in the same order as they were returned from previous setups preparation steps (the latest `prepareInstallation` or `prepareUpdate` step that has happend) on which the uninstallation was prepared for.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L551

```solidity
611: /// @dev The list of `_params.setupPayload.currentHelpers` has to be specified in the same order as they were returned from previous setups preparation steps (the latest `prepareInstallation` or `prepareUpdate` step that has happend) on which the uninstallation was prepared for.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L611

```solidity
649: /// @dev If the block number stored in `states[pluginInstallationId].blockNumber` exceeds the one stored in `pluginState.preparedSetupIdToBlockNumber[preparedSetupId]`, the prepared setup with `preparedSetupId` is outdated and not applicable anymore.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L649

```solidity
41: /// @param _preparationType The type of preparation the plugin is currently undergoing. Without this, it is possible to call `applyUpdate` even after `applyInstallation` is called.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L41

```solidity
52: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52

```solidity
74: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L74

```solidity
9: /// @dev This function allows empty (zero-length) subdomains. If this should not be allowed, make sure to add a respective check when using this function in your code.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L9

```solidity
72: /// @notice Creates a new `GovernanceERC20` token or a `GovernanceWrappedERC20` from an existing [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token depending on the address used in the `TokenConfig` provided.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L72

```solidity
74: /// @param _tokenConfig The token configuration struct containing the name, and symbol of the token to be create, but also an address. For `address(0)`, a new governance token is created. For any other address pointing to an [ERC-20](https://eips.ethereum.org/EIPS/eip-20)-compatible contract, a wrapped governance token is created.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L74

```solidity
77: /// @return The created `MerkleMinter` contract used to mint the `ERC20VotesUpgradeable` tokens or `address(0)` if an existing token was provided.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L77

```solidity
15: /// @notice This contract registers ENS subdomains under a parent domain specified in the initialization process and maintains ownership of the subdomain since only the resolver address is set. This contract must either be the domain node owner or an approved operator of the node owner. The default resolver being used is the one specified in the parent domain.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L15

```solidity
72: /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72

```solidity
110: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110

```solidity
49: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L49

```solidity
31: /// @dev This only gets called for daos that install it for the first time. The initializer modifier protects it from being called a second time for old proxies.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L31

```solidity
51: /// @dev This gets called when a dao already has `CounterV1` installed and updates to this verison `CounterV2`. For these DAOs, this `setNewVariable` can only be called once which is achieved by `reinitializer(2)`.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L51

```solidity
68: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L68

```solidity
61: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L61

```solidity
75: /// @dev Currently, there is no reliable way to revoke the `ADMIN_EXECUTE_PERMISSION_ID` from all addresses it has been granted to. Accordingly, only the `EXECUTE_PERMISSION_ID` is revoked for this uninstallation.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L75

```solidity
13: /// @param None The default option state of a voter indicating the absence of from the vote. This option neither influences support nor participation.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L13

```solidity
44: /// @notice Checks if the support value defined as $$/texttt{support} = /frac{N_/text{yes}}{N_/text{yes}+N_/text{no}}$$ for a proposal vote is greater than the support threshold.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L44

```solidity
49: /// @notice Checks if the worst-case support value defined as $$/texttt{worstCaseSupport} = /frac{N_/text{yes}}{ N_/text{total}-N_/text{abstain}}$$ for a proposal vote is greater than the support threshold.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L49

```solidity
54: /// @notice Checks if the participation value defined as $$/texttt{participation} = /frac{N_/text{yes}+N_/text{no}+N_/text{abstain}}{N_/text{total}}$$ for a proposal vote is greater or equal than the minimum participation value.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L54

```solidity
84: /// @param _tryEarlyExecution If `true`,  early execution is tried after the vote cast. The call does not revert if early execution is not possible.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L84

```solidity
27: /// where $N_/text{yes}$, $N_/text{no}$, and $N_/text{abstain}$ are the yes, no, and abstain votes that have been cast and $N_/text{total}$ is the total voting power available at /// proposal creation time.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L27

```solidity
31: /// Two limit values are associated with these parameters and decide if a proposal execution should be possible: $/texttt{supportThreshold} /in [0,1]$ and $/texttt{minParticipation} /in /// [0,1]$.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L31

```solidity
33: /// For threshold values, $>$ comparison is used. This **does not** include the threshold value. E.g., for $/texttt{supportThreshold} = 50/%$, the criterion is fulfilled if there is at /// least one more yes than no votes ($N_/text{yes} = N_/text{no} + 1 $).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L33

```solidity
34: /// For minimum values, $/ge$ comparison is used. This **does** include the minimum participation value. E.g., for $/texttt{minParticipation} = 40/%$ and $N_/text{total} = 10$, the /// criterion is fulfilled if 4 out of 10 votes were casted.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L34

```solidity
38: /// However, this is not enforced by the contract code and developers can make unsafe parameters and only the frontend will warn about bad parameter settings.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L38

```solidity
62: /// This contract allows a proposal to be executed early, iff the vote outcome cannot change anymore by more people voting. Accordingly, vote replacement and early execution are /// mutually exclusive options.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L62

```solidity
63: /// The outcome cannot change anymore iff the support threshold is met even if all remaining votes are no votes. We call this number the worst-case number of no votes and define it as
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L63

```solidity
108: /// @param EarlyExecution In early execution mode, a proposal can be executed early before the end date if the vote outcome cannot mathematically change by more voters voting.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L108

```solidity
136: /// @param allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L136

```solidity
428: /// @param _allowFailureMap Allows proposal to succeed even if an action reverts. Uses bitmap representation. If the bit at index `x` is 1, the tx succeeds even if the action at `x` failed. Passing 0 will be treated as atomic execution.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L428

```solidity
432: /// @param _tryEarlyExecution If `true`,  early execution is tried after the vote cast. The call does not revert if early execution is not possible.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L432

```solidity
447: /// @param _tryEarlyExecution If `true`,  early execution is tried after the vote cast. The call does not revert if early execution is not possible.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L447

```solidity
527: // Require the support threshold value to be in the interval [0, 10^6-1], because `>` comparision is used in the support criterion and >100% could never be reached.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L527

```solidity
580: // Since `minDuration` is limited to 1 year, `startDate + minDuration` can only overflow if the `startDate` is after `type(uint64).max - minDuration`. In this case, the proposal creation will revert and another date can be picked.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580

```solidity
593: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L593

```solidity
16: /// @notice The majority voting implementation using an [OpenZepplin `Votes`](https://docs.openzeppelin.com/contracts/4.x/api/governance#Votes) compatible governance token.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L16

```solidity
25: /// @notice An [OpenZepplin `Votes`](https://docs.openzeppelin.com/contracts/4.x/api/governance#Votes) compatible contract referencing the token being used for voting.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L25

```solidity
39: /// @param addr The token address. If this is `address(0)`, a new `GovernanceERC20` token is deployed. If not, the existing token is wrapped as an `GovernanceWrappedERC20`.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L39

```solidity
11: /// @notice Adds new members to the address list. Previously, it checks if the new addresslist length would be greater than `type(uint16).max`, the maximal number of approvals.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L11

```solidity
15: /// @notice Removes existing members from the address list. Previously, it checks if the new addresslist length is at least as long as the minimum approvals parameter requires. Note that `minApprovals` is must be at least 1 so the address list cannot become empty.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L15

```solidity
33: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L33

```solidity
201: /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L201

```solidity
304: /// @param allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L304

```solidity
128: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L128

```solidity
19: /// @notice A component minting [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens and distributing them on merkle trees using `MerkleDistributor` clones.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L19

```solidity
97: /// @notice This empty reserved space is put in place to allow future versions to add new variables without shifting down storage in the inheritance chain (see [OpenZepplins guide about storage gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L97

```solidity
22: /// @notice Thrown when the address list update is invalid, which can be caused by the addition of an existing member or removal of a non-existing member.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L22

```solidity
19: /// @notice An [OpenZepplin `Votes`](https://docs.openzeppelin.com/contracts/4.x/api/governance#Votes) compatible [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token that can be used for voting and is managed by a DAO.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L19

```solidity
21: /// @notice Wraps an existing [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token by inheriting from `ERC20WrapperUpgradeable` and allows to use it for voting by inheriting from `ERC20VotesUpgradeable`. The latter is compatible with [OpenZepplin `Votes`](https://docs.openzeppelin.com/contracts/4.x/api/governance#Votes) interface.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L21

```solidity
24: /// 2. call `depositFor` to wrap them, which safely transfers the underlying [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens to the contract and mints wrapped [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L24

```solidity
25: /// To get the [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens back, the owner of the wrapped tokens can call `withdrawFor`, which  burns the wrapped tokens [ERC-20](https://eips.ethereum.org/EIPS/eip-20) tokens and safely transfers the underlying tokens back to the owner.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L25

```solidity
26: /// @dev This contract intentionally has no public mint functionality because this is the responsibility of the underlying [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token contract.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L26

```solidity
7: /// @notice Free function to create a [ERC-1967](https://eips.ethereum.org/EIPS/eip-1967) proxy contract based on the passed base contract address.
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol#L7

```solidity
11: /// @dev Initializes the upgradeable proxy with an initial implementation specified by _logic. If _data is non-empty, it’s used as data in a delegate call to _logic. This will typically be an encoded function call, and allows initializing the storage of the proxy like a Solidity constructor (see [OpenZepplin ERC1967Proxy-constructor](https://docs.openzeppelin.com/contracts/4.x/api/proxy#ERC1967Proxy-constructor-address-bytes-)).
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol#L11





### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
144: function setupBases() private {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L144

```solidity
100: function setDefaultResolver(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L100

```solidity
52: function setNewVariable(uint256 _newVariable) external reinitializer(2) {
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L52

```solidity
53: function changeDistributorBase(
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L53





### <a href="#Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
92: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L92

```solidity
17: constructor(IDAO _dao) DaoAuthorizable(_dao)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L17

```solidity
16: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L16

```solidity
25: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25

```solidity
19: constructor(IDAO _dao)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19

```solidity
53: constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L53

```solidity
30: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L30

```solidity
112: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L112

```solidity
22: constructor(PluginRepoRegistry _pluginRepoRegistry)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22

```solidity
34: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L34

```solidity
277: constructor(PluginRepoRegistry _repoRegistry)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277

```solidity
68: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L68

```solidity
45: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L45

```solidity
23: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L23

```solidity
24: constructor(MultiplyHelper _helper)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L24

```solidity
27: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L27

```solidity
20: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L20

```solidity
61: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61

```solidity
19: constructor()
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L19

```solidity
49: constructor(
        IDAO _dao,
        string memory _name,
        string memory _symbol,
        MintSettings memory _mintSettings
    )
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L49

```solidity
38: constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol)
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L38






### <a href="#Summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
7: import "./IPermissionCondition.sol";
8: import "./PermissionLib.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L7-L8

```solidity
8: import "./IProposal.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L8

```solidity
8: import "./IProposal.sol";

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L8




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/">0.8.19</a>:
SMTChecker: New trusted mode that assumes that any compile-time available code is the actual used code, even in external calls. 
Bug Fixes: 
- Assembler: Avoid duplicating subassembly bytecode where possible.
- Code Generator: Avoid including references to the deployed label of referenced functions if they are called right away.
- ContractLevelChecker: Properly distinguish the case of missing base constructor arguments from having an unimplemented base function.
- SMTChecker: Fix internal error caused by unhandled z3 expressions that come from the solver when bitwise operators are used.
- SMTChecker: Fix internal error when using the custom NatSpec annotation to abstract free functions.
- TypeChecker: Also allow external library functions in using for.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IEIP4824.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/IPermissionCondition.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/IPlugin.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/membership/IMembership.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol#L5

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L5

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/IERC20MintableUpgradeable.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol#L3

```solidity
pragma solidity 0.8.17;
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/UncheckedMath.sol#L3



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters
The following events should also add the previous original value in addition to the new value.

#### <ins>Proof Of Concept</ins>


```solidity
89: event NewURI(string daoURI);
```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L89




#### <ins>Recommended Mitigation Steps</ins>
The events should include the new value and old value where possible.





### <a href="#Summary">[NC&#x2011;18]</a><a name="NC&#x2011;18"> Empty blocks should be removed or emit something

#### <ins>Proof Of Concept</ins>

```solidity
136: function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L136

```solidity
320: } catch {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L320

```solidity
17: constructor(IDAO _dao) DaoAuthorizable(_dao) {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol#L17

```solidity
63: ) internal virtual override auth(UPGRADE_PLUGIN_PERMISSION_ID) {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L63

```solidity
259: ) internal virtual override auth(UPGRADE_REPO_PERMISSION_ID) {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L259

```solidity
27: {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L27

```solidity
672: {} catch Error(string memory reason) {
682: try PluginUUPSUpgradeable(_proxy).upgradeTo(_implementation) {} catch Error(

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L672

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L682



```solidity
56: ) internal virtual override auth(UPGRADE_REGISTRY_PERMISSION_ID) {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L56

```solidity
10: contract Dummy {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol#L10

```solidity
76: ) internal virtual override auth(UPGRADE_REGISTRAR_PERMISSION_ID) {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L76






### <a href="#Summary">[NC&#x2011;19]</a><a name="NC&#x2011;19"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
347: return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId))

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L347

```solidity
252: return keccak256(abi.encodePacked(_tag.release, _tag.build))

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L252

```solidity
86: bytes32 subnode = keccak256(abi.encodePacked(node, _label))

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86

```solidity
105: bytes32 node = keccak256(abi.encodePacked(_index, _to, _amount))

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L105




#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> Implement some type of version counter that will be incremented automatically for contract upgrades

As part of the upgradeability of Proxies , the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

#### <ins>Proof Of Concept</ins>

```solidity
136: function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L136

```solidity
61: function _authorizeUpgrade(

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L61

```solidity
257: function _authorizeUpgrade(

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L257

```solidity
54: function _authorizeUpgrade(

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L54

```solidity
74: function _authorizeUpgrade(

```

https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L74




#### <ins>Recommended Mitigation Steps</ins>

I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

For example
```solidity
uint256 public authorizeUpgradeCounter;

function _authorizeUpgrade(address newImplementation) internal {
    authorizeUpgradeCounter+=1;
}
```



