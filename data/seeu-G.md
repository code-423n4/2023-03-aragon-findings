# Gas Optimization Issues

| ID  | Issue | Contexts | Instances | Total Gas Saved |
| --- | ----- | -------- | --------- | --------------- |
| [G-01](#g-01-when-possible-use-non-strict-comparison--andor--instead-of--) | When possible, use non-strict comparison >= and/or =< instead of > < | 17 | 47 | 705 |
| [G-02](#g-02-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead) | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | 6 | 14 | - |


| Total issues | Total contexts | Total instances | Total minimum gas saved |
| ------------ | -------------- | --------------- | ----------------------- |
| 2            | 23             | 61              | 705                  |

## [G-01] When possible, use non-strict comparison >= and/or =< instead of > <

### Description

Non-strict inequalities are cheaper than strict ones due to some supplementary checks (`ISZERO`, 3 gas). It will save 15–20 gas.

### Findings

- [packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)
  ```Solidity
  ::178 =>         if (_actions.length > MAX_ACTIONS) {
  ```
- [packages/contracts/src/core/permission/PermissionManager.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/permission/PermissionManager.sol)
  ```Solidity
  ::150 =>         for (uint256 i; i < items.length; ) {
  ::170 =>         for (uint256 i; i < _items.length; ) {
  ```
- [packages/contracts/src/core/utils/BitMap.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/utils/BitMap.sol)
  ```Solidity
  ::10 =>     return bitValue > 0;
  ::17 =>     return bitmap ^ (1 << index);
  ```
- [packages/contracts/src/framework/dao/DAOFactory.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAOFactory.sol)
  ```Solidity
  ::95 =>         for (uint256 i; i < _pluginSettings.length; ++i) {
  ```
- [packages/contracts/src/framework/dao/DAORegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/dao/DAORegistry.sol)
  ```Solidity
  ::59 =>         if ((bytes(subdomain).length > 0)) {
  ```
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)
  ```Solidity
  ::143 =>         if (_release - latestRelease > 1) {
  ::147 =>         if (_release > latestRelease) {
  ::181 =>         if (_releaseMetadata.length > 0) {
  ::195 =>         if (_release > latestRelease) {
  ```
- [packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol)
  ```Solidity
  ::55 =>         if (!(bytes(subdomain).length > 0)) {
  ```
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol)
  ```Solidity
  ::326 =>         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {
  ::378 =>         if (_params.permissions.length > 0) {
  ::485 =>         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {
  ::535 =>         if (_params.permissions.length > 0) {
  ::590 =>         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {
  ::635 =>         if (_params.permissions.length > 0) {
  ::669 =>         if (_initData.length > 0) {
  ```
- [packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol)
  ```Solidity
  ::16 =>     for (uint256 i; i < nameLength; i++) {
  ::20 =>         if (char > 96 && char < 123) {
  ::25 =>         if (char > 47 && char < 58) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)
  ```Solidity
  ::319 =>             (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >
  ::336 =>             (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >
  ::520 =>             currentTime < proposal_.parameters.endDate &&
  ::528 =>         if (_votingSettings.supportThreshold > RATIO_BASE - 1) {
  ::536 =>         if (_votingSettings.minParticipation > RATIO_BASE) {
  ::540 =>         if (_votingSettings.minDuration < 60 minutes) {
  ::544 =>         if (_votingSettings.minDuration > 365 days) {
  ::575 =>             if (startDate < currentTimestamp) {
  ::587 =>             if (endDate < earliestEndDate) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol)
  ```Solidity
  ::130 =>         for (uint256 i; i < _actions.length; ) {
  ```
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol)
  ```Solidity
  ::91 =>         if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {
  ::124 =>         for (uint256 i; i < _actions.length; ) {
  ::139 =>         return votingToken.getVotes(_account) > 0;
  ```
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)
  ```Solidity
  ::159 =>         if (newAddresslistLength > type(uint16).max) {
  ::178 =>         if (newAddresslistLength < multisigSettings.minApprovals) {
  ::222 =>         } else if (_startDate < block.timestamp.toUint64()) {
  ::226 =>         if (_endDate < _startDate) {
  ::252 =>         for (uint256 i; i < _actions.length; ) {
  ::416 =>         if (_multisigSettings.minApprovals > addresslistLength_) {
  ::423 =>         if (_multisigSettings.minApprovals < 1) {
  ```
- [packages/contracts/src/plugins/token/MerkleDistributor.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/token/MerkleDistributor.sol)
  ```Solidity
  ::125 =>             (1 << claimedBit_index);
  ```
- [packages/contracts/src/plugins/utils/Addresslist.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Addresslist.sol)
  ```Solidity
  ::60 =>         for (uint256 i; i < _newAddresses.length; ) {
  ::78 =>         for (uint256 i; i < _exitingAddresses.length; ) {
  ```
- [packages/contracts/src/plugins/utils/Ratio.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/utils/Ratio.sol)
  ```Solidity
  ::18 =>     if (_ratio > RATIO_BASE) {
  ```
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol)
  ```Solidity
  ::81 =>         for (uint256 i; i < _mintSettings.receivers.length; ) {
  ```

### References
- [Solidity Gas Optimizations Tricks](https://betterprogramming.pub/solidity-gas-optimizations-and-tricks-2bcee0f9f1f2)


## [G-02] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

### Description

Gas consumption can be greater if you use items that are less than 32 bytes in size. This is such that the EVM can only handle 32 bytes at once. In order to increase the element's size from 32 bytes to the necessary amount, the EVM must do extra operations if it is lower than that. When necessary, it is advised to utilize a bigger size and then downcast.

### Findings

- [packages/contracts/src/core/dao/DAO.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/core/dao/DAO.sol)
  ```Solidity
  ::198 =>                 failureMap = flipBit(failureMap, uint8(i));
  ```
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol)
  ```Solidity
  ::55 =>     mapping(uint8 => uint16) internal buildsPerRelease;
  ::165 =>         uint16 build = ++buildsPerRelease[_release];
  ::210 =>         uint16 latestBuild = uint16(buildsPerRelease[_release]);
  ```
- [packages/contracts/src/framework/utils/RegistryUtils.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/framework/utils/RegistryUtils.sol)
  ```Solidity
  ::17 =>         uint8 char = uint8(nameBytes[i]);
  ```
- [packages/contracts/src/plugins/governance/admin/Admin.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/admin/Admin.sol)
  ```Solidity
  ::67 =>         uint64 currentTimestamp64 = block.timestamp.toUint64();
  ```
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol)
  ```Solidity
  ::516 =>         uint64 currentTime = block.timestamp.toUint64();
  ::568 =>         uint64 currentTimestamp = block.timestamp.toUint64();
  ::580 =>         uint64 earliestEndDate = startDate + votingSettings.minDuration; // Since `minDuration` is limited to 1 year, `startDate + minDuration` can only overflow if the `startDate` is after `type(uint64).max - minDuration`. In this case, the proposal creation will revert and another date can be picked.
  ```
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol](https://github.com/code-423n4/2023-03-aragon/tree/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol)
  ```Solidity
  ::175 =>         uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);
  ::214 =>         uint64 snapshotBlock = block.number.toUint64() - 1;
  ::404 =>         uint64 currentTimestamp64 = block.timestamp.toUint64();
  ::414 =>         uint16 addresslistLength_ = uint16(addresslistLength());
  ```

### References
- [Layout of State Variables in Storage | Solidity docs](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html#layout-of-state-variables-in-storage)
- [GAS OPTIMIZATIONS ISSUES by Gokberk Gulgun](https://hackmd.io/@W1m6lTsFT5WAy9C_lRTX_g/rkr5Laoys)