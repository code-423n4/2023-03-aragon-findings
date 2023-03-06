## token/ERC20/governance/GovernanceERC20

* [Array lengths of `_mintSettings.receivers` and `_mintSettings.amounts` can be cached into memory before used to optimize gas usage](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L7)

## core/permissions/PermissionsManager

* Cache array length ([L150](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L150) / [L170](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L170))

## core/dao/DAO

* [Cache `actions` length in `execute`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L178)

## plugins/governance/majority-voting/MajorityVotingBase

* [`proposal_.parameters.supportThreshold` in `isSupportThresholdReached` and `isSupportThresholdReachedEarly` can be cached into memory before read twice](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L313-L338)

## plugins/governance/majority-voting/token/TokenVoting

* [Cache array length](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L124)

## plugins/governance/majority-voting/addresslist/AddreslistVoting

* [Cache array length](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L130)

## plugins/governance/multisig/Multisig

- [Comment in L70 is not up to date (missing `updateMultisigSettings`)](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L70)
- [`startDate` and `endDate` missing natspec in `createProposal`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L198-L204)
- [Cache `_actions.length` in memory](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L252)
- [Change `proposal_.approvals += 1` to `++proposal_.approvals` in `approve`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276)

## plugins/utils/Addreslist

* Cache array length ([L60](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/utils/Addresslist.sol#L60) / [L78](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/utils/Addresslist.sol#L78))

## framework/plugin/setup/PluginSetupProcessor

* Move `_getPreparedSetupId` after checking whether the plugin is installed (saves gas in case the plugin is already installed) (functions: [`prepareInstallation`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L310-L323), [`applyInstallation`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L357-L368))
* [`repoRegistry` can be made `immutable`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L128)

## framework/utils/ens/ENSSubdomainRegistrar

* [Move `nodeResolver` check in `initialize` before storing `_ens` or `_node` to avoid the extra storage writes in case the resolution check fails](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L60-L67)

## framework/plugin/repo/PluginRepoFactory

* [`pluginRepoBase` and `pluginRepoRegistry` can be made `immutable`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L15-L18)