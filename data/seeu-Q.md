| Index |
| --- |
| [Low issues](#low-issues) |
| [Non-Critical issues](#non-critical-issues) |

# Low issues

| ID                                                       | Issue                                      | Contexts | Instances |
| -------------------------------------------------------- | ------------------------------------------ | -------- | --------- |
| [L-01](#l-01-decimals-is-not-part-of-erc20-standard-and-it-may-fail) | decimals() is not part of ERC20 standard and it may fail | 1 | 1 |
| [L-02](#l-02-timestamp-dependency) | Timestamp dependency | 3 | 7 |
| [L-03](#l-03-use-_safemint-instead-of-_mint) | Use `_safeMint` instead of `_mint` | 2 | 4 |
| [L-04](#l-04-upgradeable-contracts-are-lacking-the-storage-variable-__gap50) | Upgradeable contracts are lacking the storage variable `__gap[50]` | 10 | 10 |
| [L-05](#l-05-usage-of-calls-inside-of-loop) | Usage of calls inside of loop | 1 | 1 |
| [L-06](#l-06-draft-openzeppelin-dependencies) | Draft OpenZeppelin dependencies | 3 | 3 |

| Total issues | Total contexts | Total instances |
| ------------ | -------------- | --------------- |
| 6            | 20             | 26              |


## [L-01] decimals() is not part of ERC20 standard and it may fail

Since `decimals()` is not a part of the official ERC20 standard, it could not work for some tokens.

### Description

### Findings

[packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L77](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L77)
```Solidity
return ERC20WrapperUpgradeable.decimals();
```

### References

- [[L-02] DECIMALS() NOT PART OF ERC20 STANDARD](https://code4rena.com/reports/2022-07-axelar#l-02-decimals-not-part-of-erc20-standard)


## [L-02] Timestamp dependency

### Description

The timestamp of a block is provided by the miner who mined the block. As a result, the timestamp is not guaranteed to be accurate or to be the same across different nodes in the network. In particular, an attacker can potentially mine a block with a timestamp that is favorable to them, known as "selective packing".

For example, an attacker could mine a block with a timestamp that is slightly in the future, allowing them to bypass a time-based restriction in a smart contract that relies on `block.timestamp`. This could potentially allow the attacker to execute a malicious action that would otherwise be blocked by the restriction.

It is reccomended to, instead, use an alternative timestamp source, such as an oracle, that is not susceptible to manipulation by a miner.

### Findings

- [packages/contracts/src/plugins/governance/admin/Admin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol)
  ```Solidity
  ::67 =>         uint64 currentTimestamp64 = block.timestamp.toUint64();
  ```
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)
  ```Solidity
  ::516 =>         uint64 currentTime = block.timestamp.toUint64();
  ::568 =>         uint64 currentTimestamp = block.timestamp.toUint64();
  ```
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)
  ```Solidity
  ::221 =>             _startDate = block.timestamp.toUint64();
  ::222 =>         } else if (_startDate < block.timestamp.toUint64()) {
  ::223 =>             revert DateOutOfBounds({limit: block.timestamp.toUint64(), actual: _startDate});
  ::404 =>         uint64 currentTimestamp64 = block.timestamp.toUint64();
  ```

### References
- [Timestamp dependence | Solidity Best Practices for Smart Contract Security](https://consensys.net/blog/developers/solidity-best-practices-for-smart-contract-security/)
- [What Is Timestamp Dependence?](https://halborn.com/what-is-timestamp-dependence/)


## [L-03] Use `_safeMint` instead of `_mint`

### Description

In favor of `_safeMint()`, which guarantees that the receiver is either an EOA or implements IERC721Receiver, `_mint()` is deprecated.

### Findings

- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)
  ```Solidity
  ::82 =>             _mint(_mintSettings.receivers[i], _mintSettings.amounts[i]);
  ::107 =>         _mint(to, amount);
  ```
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)
  ```Solidity
  ::112 =>     function _mint(
  ::116 =>         super._mint(to, amount);
  ```

### References
- [OpenZeppelin warning ERC721.sol#L271](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271)
- [solmate _safeMint](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180)
- [OpenZeppelin _safeMint](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250)


## [L-04] Upgradeable contracts are lacking the storage variable `__gap[50]`

### Description

Even if it's possible that certain contracts aren't already subclassifiedsub-classed, including the storage variable `__gap[50]` now will prevent forgetting to do so in the future.

### Findings

- [packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)
- [packages/contracts/src/framework/utils/TokenFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol)
- [packages/contracts/src/plugins/token/MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)
- [packages/contracts/src/plugins/token/MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol)
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)

### Resources

- [Storage Gaps | OpenZeppelin docs](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)
- [[L-02] UPGRADEABLE CONTRACT IS MISSING A __GAP[50] STORAGE VARIABLE TO ALLOW FOR NEW STORAGE VARIABLES IN LATER VERSIONS](https://code4rena.com/reports/2022-06-badger#l-02-upgradeable-contract-is-missing-a-__gap50-storage-variable-to-allow-for-new-storage-variables-in-later-versions)

## [L-05] Usage of calls inside of loop

### Description

A denial-of-service attack might result from calls made inside a loop.

### Findings

[packages/contracts/src/core/dao/DAO.sol#L184-L188](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol#L184-L188)
```Solidity
for (uint256 i = 0; i < _actions.length; ) {
          address to = _actions[i].to;
          (bool success, bytes memory response) = to.call{value: _actions[i].value}(
              _actions[i].data
          );
```

### References

- [Calls inside a loop | Slither](https://github.com/crytic/slither/wiki/Detector-Documentation#calls-inside-a-loop)

## [L-06] Draft OpenZeppelin dependencies

### Description

OpenZeppelin draft contracts may not have undergone sufficient security auditing or are subject to change as a result of upcoming development.

### Findings

- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6)
  ```Solidity
  import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol)";
  ```
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5)
  ```Solidity
  import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol)";
  ```
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8)
  ```Solidity
  import {IERC20PermitUpgradeable} from "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol)";
  ```

### References
- [[L-02] Draft OpenZeppelin Dependencies | prePO contest](https://code4rena.com/reports/2022-12-prepo/#l-02-draft-openzeppelin-dependencies)


# Non-Critical issues

| ID    | Issue | Contexts | Instances |
| ----- | ----- | -------- | --------- |
| [NC-01](#nc-01-unnamed-return-parameters) | Unnamed return parameters | 34 | 76 |
| [NC-02](#nc-02-pragma-version--0817-too-recent-to-be-trusted) | Pragma Version >= 0.8.17 too recent to be trusted | 62 | 62 |
| [NC-03](#nc-03-for-modern-and-more-readable-code-update-import-usages) | For modern and more readable code; update import usages | 7 | 20 |
| [NC-04](#nc-04-use-of-abiencodepacked-instead-of-bytesconcat-for-solidity-version--084) | Usage of abi.encodePacked instead of bytes.concat() for Solidity version >= 0.8.4 | 4 | 4 |

| Total issues | Total contexts | Total instances |
| ------------ | -------------- | --------------- |
| 4            | 107            | 162             |


## [NC-01] Unnamed return parameters

### Description

To increase explicitness and readability, take into account introducing and utilizing named return parameters.

### Findings

- [packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)
  ```Solidity
  ::146 =>     function getTrustedForwarder() external view virtual override returns (address) {
  ::320 =>     function daoURI() external view returns (string memory) {
  ```
- [packages/contracts/src/core/plugin/Plugin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol)
  ```Solidity
  ::20 =>     function pluginType() public pure override returns (PluginType) {
  ::27 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/core/plugin/PluginCloneable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol)
  ```Solidity
  ::27 =>     function pluginType() public pure override returns (PluginType) {
  ::34 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)
  ```Solidity
  ::30 =>     function pluginType() public pure override returns (PluginType) {
  ::46 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ::55 =>     function implementation() public view returns (address) {
  ```
- [packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol)
  ```Solidity
  ::25 =>     function dao() public view returns (IDAO) {
  ```
- [packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol)
  ```Solidity
  ::26 =>     function dao() public view returns (IDAO) {
  ```
- [packages/contracts/src/core/plugin/proposal/Proposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol)
  ```Solidity
  ::20 =>     function proposalCount() public view override returns (uint256) {
  ::27 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol)
  ```Solidity
  ::20 =>     function proposalCount() public view override returns (uint256) {
  ::27 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/core/utils/BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol)
  ```Solidity
  ::8 => function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {
  ::16 => function flipBit(uint256 bitmap, uint8 index) pure returns (uint256) {
  ```
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)
  ```Solidity
  ::209 =>     function getLatestVersion(uint8 _release) public view returns (Version memory) {
  ::217 =>     function getLatestVersion(address _pluginSetup) public view returns (Version memory) {
  ::224 =>     function getVersion(Tag calldata _tag) public view returns (Version memory) {
  ::231 =>     function getVersion(bytes32 _tagHash) public view returns (Version memory) {
  ::244 =>     function buildCount(uint8 _release) public view returns (uint256) {
  ::251 =>     function tagHash(Tag memory _tag) internal pure returns (bytes32) {
  ::264 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol)
  ```Solidity
  ::43 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol)
  ```Solidity
  ::32 => function _getPluginInstallationId(address _dao, address _plugin) pure returns (bytes32) {
  ::79 => function hashHelpers(address[] memory _helpers) pure returns (bytes32) {
  ```
- [packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol)
  ```Solidity
  ::13 => function isSubdomainValid(string calldata subDomain) pure returns (bool) {
  ```
- [packages/contracts/src/plugins/counter-example/v1/CounterV1.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol)
  ```Solidity
  ::39 =>     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {
  ```
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol)
  ```Solidity
  ::137 =>     function implementation() external view virtual override returns (address) {
  ```
- [packages/contracts/src/plugins/counter-example/v2/CounterV2.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol)
  ```Solidity
  ::58 =>     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {
  ```
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol)
  ```Solidity
  ::177 =>     function implementation() external view virtual override returns (address) {
  ```
- [packages/contracts/src/plugins/governance/admin/Admin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol)
  ```Solidity
  ::48 =>     function isMember(address _account) external view returns (bool) {
  ```
- [packages/contracts/src/plugins/governance/admin/AdminSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol)
  ```Solidity
  ::93 =>     function implementation() external view returns (address) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)
  ```Solidity
  ::308 =>     function canExecute(uint256 _proposalId) public view virtual returns (bool) {
  ::313 =>     function isSupportThresholdReached(uint256 _proposalId) public view virtual returns (bool) {
  ::341 =>     function isMinParticipationReached(uint256 _proposalId) public view virtual returns (bool) {
  ::352 =>     function supportThreshold() public view virtual returns (uint32) {
  ::357 =>     function minParticipation() public view virtual returns (uint32) {
  ::363 =>     function minDuration() public view virtual returns (uint64) {
  ::369 =>     function minProposerVotingPower() public view virtual returns (uint256) {
  ::375 =>     function votingMode() public view virtual returns (VotingMode) {
  ::483 =>     function _canExecute(uint256 _proposalId) internal view virtual returns (bool) {
  ::515 =>     function _isProposalOpen(Proposal storage proposal_) internal view virtual returns (bool) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)
  ```Solidity
  ::48 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ::78 =>     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {
  ::143 =>     function isMember(address _account) external view returns (bool) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol)
  ```Solidity
  ::129 =>     function implementation() external view returns (address) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)
  ```Solidity
  ::51 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ::61 =>     function getVotingToken() public view returns (IVotesUpgradeable) {
  ::66 =>     function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {
  ::137 =>     function isMember(address _account) external view returns (bool) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol)
  ```Solidity
  ::258 =>     function implementation() external view virtual override returns (address) {
  ::265 =>     function _getTokenInterfaceIds(address token) private view returns (bool[] memory) {
  ::276 =>     function _isERC20(address token) private view returns (bool) {
  ```
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)
  ```Solidity
  ::289 =>     function canApprove(uint256 _proposalId, address _account) external view returns (bool) {
  ::294 =>     function canExecute(uint256 _proposalId) external view returns (bool) {
  ::328 =>     function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {
  ::342 =>     function isMember(address _account) external view returns (bool) {
  ::365 =>     function _canApprove(uint256 _proposalId, address _account) internal view returns (bool) {
  ::389 =>     function _canExecute(uint256 _proposalId) internal view returns (bool) {
  ::403 =>     function _isProposalOpen(Proposal storage proposal_) internal view returns (bool) {
  ```
- [
packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol)
  ```Solidity
  ::109 =>     function implementation() external view returns (address) {
  ```
- [packages/contracts/src/plugins/token/MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)
  ```Solidity
  ::59 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ::110 =>     function isClaimed(uint256 _index) public view override returns (bool) {
  ```
- [packages/contracts/src/plugins/token/MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol)
  ```Solidity
  ::62 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/plugins/utils/Addresslist.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol)
  ```Solidity
  ::40 =>     function isListed(address _account) public view virtual returns (bool) {
  ::47 =>     function addresslistLengthAtBlock(uint256 _blockNumber) public view virtual returns (uint256) {
  ::53 =>     function addresslistLength() public view virtual returns (uint256) {
  ```
- [packages/contracts/src/plugins/utils/Ratio.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol)
  ```Solidity
  ::17 => function _applyRatioCeiled(uint256 _value, uint256 _ratio) pure returns (uint256 result) {
  ```
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)
  ```Solidity
  ::93 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)
  ```Solidity
  ::59 =>     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
  ```
- [packages/contracts/src/utils/Proxy.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol)
  ```Solidity
  ::12 => function createERC1967Proxy(address _logic, bytes memory _data) returns (address) {
  ```
- [packages/contracts/src/utils/UncheckedMath.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/UncheckedMath.sol)
  ```Solidity
  ::8 => function _uncheckedIncrement(uint256 i) pure returns (uint256) {
  ::19 => function _uncheckedAdd(uint256 a, uint256 b) pure returns (uint256) {
  ::29 => function _uncheckedSub(uint256 a, uint256 b) pure returns (uint256) {
  ```

### References

- [Unnamed return parameters | Opyn Bull Strategy Contracts Audit](https://blog.openzeppelin.com/opyn-bull-strategy-contracts-audit/#unnamed-return-parameters)

## [NC-02] Pragma Version >= 0.8.17 too recent to be trusted

### Description

In recert versions, unexpected problems might be reported. Use a more robust, non-legacy version like `0.8.10`.

### Findings

- [packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)
- [packages/contracts/src/core/dao/IDAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IDAO.sol)
- [packages/contracts/src/core/dao/IEIP4824.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/IEIP4824.sol)
- [packages/contracts/src/core/permission/IPermissionCondition.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/IPermissionCondition.sol)
- [packages/contracts/src/core/permission/PermissionLib.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionLib.sol)
- [packages/contracts/src/core/permission/PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)
- [packages/contracts/src/core/plugin/IPlugin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/IPlugin.sol)
- [packages/contracts/src/core/plugin/Plugin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/Plugin.sol)
- [packages/contracts/src/core/plugin/PluginCloneable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginCloneable.sol)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol)
- [packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol)
- [packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol)
- [packages/contracts/src/core/plugin/membership/IMembership.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/membership/IMembership.sol)
- [packages/contracts/src/core/plugin/proposal/IProposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/IProposal.sol)
- [packages/contracts/src/core/plugin/proposal/Proposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol)
- [packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol)
- [packages/contracts/src/core/utils/BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol)
- [packages/contracts/src/core/utils/CallbackHandler.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/CallbackHandler.sol)
- [packages/contracts/src/core/utils/auth.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/auth.sol)
- [packages/contracts/src/framework/dao/DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol)
- [packages/contracts/src/framework/dao/DAORegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol)
- [packages/contracts/src/framework/plugin/repo/IPluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol)
- [packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)
- [packages/contracts/src/framework/plugin/setup/IPluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol)
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol)
- [packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol)
- [packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol)
- [packages/contracts/src/framework/utils/TokenFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/TokenFactory.sol)
- [packages/contracts/src/framework/utils/ens/ENSMigration.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)
- [packages/contracts/src/plugins/counter-example/MultiplyHelper.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol)
- [packages/contracts/src/plugins/governance/admin/Admin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol)
- [packages/contracts/src/plugins/governance/admin/AdminSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol)
- [packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol)
- [packages/contracts/src/plugins/governance/multisig/IMultisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)
- [packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol)
- [packages/contracts/src/plugins/token/IMerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol)
- [packages/contracts/src/plugins/token/IMerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/IMerkleMinter.sol)
- [packages/contracts/src/plugins/token/MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)
- [packages/contracts/src/plugins/token/MerkleMinter.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleMinter.sol)
- [packages/contracts/src/plugins/utils/Addresslist.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol)
- [packages/contracts/src/plugins/utils/Ratio.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol)
- [packages/contracts/src/token/ERC20/IERC20MintableUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/IERC20MintableUpgradeable.sol)
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol)
- [packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol)
- [packages/contracts/src/utils/Proxy.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol)
- [packages/contracts/src/utils/UncheckedMath.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/UncheckedMath.sol)

### References

- [Ethereum Solidity changelog](https://github.com/ethereum/solidity/blob/develop/Changelog.md)
- [[N-09] PRAGMA VERSION^0.8.17 VERSION TOO RECENT TO BE TRUSTED.](https://code4rena.com/reports/2022-12-caviar/#n-09-pragma-version0817--version-too-recent-to-be-trusted)

## [NC-03] For modern and more readable code; update import usages

### Description

A less obvious way that solidity code is clearer is the struct Point. Prior to now, we imported it via global import, but we didn't use it. The Point struct contaminated the source code with an extra object that was not needed and that we were not utilizing.

To be sure to only import what you need, use specific imports using curly brackets.

### Findings

- [packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)
  ```Solidity
  ::5 => import "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165StorageUpgradeable.sol)";
  ::6 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol)";
  ::7 => import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol)";
  ::8 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol)";
  ::9 => import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol)";
  ::10 => import "@openzeppelin/contracts-upgradeable/token/ERC721/IERC721ReceiverUpgradeable.sol)";
  ::11 => import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol)";
  ::12 => import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155ReceiverUpgradeable.sol)";
  ::13 => import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol)";
  ::14 => import "@openzeppelin/contracts/interfaces/IERC1271.sol)";
  ```
- [packages/contracts/src/core/permission/PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)
  ```Solidity
  ::5 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol)";
  ::7 => import "./IPermissionCondition.sol)";
  ::8 => import "./PermissionLib.sol)";
  ```
- [packages/contracts/src/core/plugin/proposal/Proposal.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/Proposal.sol)
  ```Solidity
  ::8 => import "./IProposal.sol)";
  ```
- [packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol)
  ```Solidity
  ::8 => import "./IProposal.sol)";
  ```
- [packages/contracts/src/framework/utils/ens/ENSMigration.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol)
  ```Solidity
  ::7 => import "@ensdomains/ens-contracts/contracts/registry/ENSRegistry.sol)";
  ::8 => import "@ensdomains/ens-contracts/contracts/resolvers/PublicResolver.sol)";
  ```
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol)
  ```Solidity
  ::5 => import "@ensdomains/ens-contracts/contracts/registry/ENS.sol)";
  ::6 => import "@ensdomains/ens-contracts/contracts/resolvers/Resolver.sol)";
  ```
- [packages/contracts/src/utils/Proxy.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/utils/Proxy.sol)
  ```Solidity
  ::5 => import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol)";
  ```

### References

- [[N-03] FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES | PoolTogether contest](https://code4rena.com/reports/2022-12-pooltogether#n-03-for-modern-and-more-readable-code-update-import-usages)


## [NC-04] Use of abi.encodePacked instead of bytes.concat() for Solidity version >= 0.8.4

### Description

From the Solidity version `0.8.4` it was added the possibility to use `bytes.concat` with variable number of `bytes` and `bytesNN` arguments. With a more evocative name, it functions as a restricted `abi.encodePacked`.

### Findings

- [packages/contracts/src/core/permission/PermissionManager.sol#L347](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol#L347)
  ```Solidity
  return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));
  ```
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L252](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L252)
  ```Solidity
  return keccak256(abi.encodePacked(_tag.release, _tag.build));
  ```
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86)
  ```Solidity
  bytes32 subnode = keccak256(abi.encodePacked(node, _label));
  ```
- [packages/contracts/src/plugins/token/MerkleDistributor.sol#L105](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L105)
  ```Solidity
  bytes32 node = keccak256(abi.encodePacked(_index, _to, _amount));
  ```

### References

- [Solidity 0.8.4 Release Announcement](https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/)
- [Remove abi.encodePacked #11593](https://github.com/ethereum/solidity/issues/11593)