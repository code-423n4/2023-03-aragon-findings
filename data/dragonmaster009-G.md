At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L178
    https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L182
    https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L184

- Store `_actions.length` in stack (#L178)

`uint256 actionsLength = _actions.length;
 if (actionsLength > MAX_ACTIONS) {`

- Store `_actions.length` in stack (#L182)

`uint256 actionsLength = _actions.length
 execResults = new bytes[](actionsLength);`

- Store `_actions.length` in stack (#L184)

`uint256 actionsLength = _actions.length;
 for (uint256 i; i < actionsLength;) {`

- Don't explicitly set `i` to 0 (#L184)

`for (uint256 i; i < actionsLength;) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L150
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L170
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L260
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L287
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L328
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L331

- Store `items.length` in stack (#L150)

`uint256 itemsLength = items.length;
 for (uint256 i; i < itemsLength;) {`

- Store `items.length` in stack (#L170)

`uint256 itemsLength = items.length;
 for (uint256 i; i < itemsLength;) {`

- Use `_msgSender(), _msgData()` instead of `msg.sender, msg.data` (#L260, #L287, #L328, #L331)

`emit Granted(_permissionId, _msgSender(), _where, _who, _condition);`
`emit Revoked(_permissionId, _msgSender(), _where, _who);`
`if (!isGranted(address(this), _msgSender(), _permissionId, _msgData())) {`
`who: _msgSender(),`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L68
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95

- Store `_pluginSettings.length` in stack (#L68)

`uint256 pluginSettingsLength = _pluginSettings.length;
 if (pluginSettingsLength == 0) {`

- Store `_pluginSettings.length` in stack (#L95)

`uint256 pluginSettingsLength = _pluginSettings.length;
 for (uint256 i; i < pluginSettingsLength;) {`

- use `unchecked` when incrementing `i` at the end of for loop (#L95)

`for (uint256 i; i < pluginSettingsLength;) {
    unchecked { ++i; }
}`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L59

- Store `subdomain.length` in stack (#L59)

`uint256 subdomainLength = subdomain.length;
 if ((bytes(subdomainLength) > 0)) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L150
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L181
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L199

- Store `_releaseMetadata.length` in stack (#L150)

`uint256 releaseMetadataLength = _releaseMetadata.length;
 if (releaseMetadataLength == 0) {`

- Store `_releaseMetadata.length` in stack (#L181)

`uint256 releaseMetadataLength = _releaseMetadata.length;
  if (releaseMetadataLength > 0) {`

- Store `_releaseMetadata.length` in stack (#L199)

`uint256 releaseMetadataLength = _releaseMetadata.length;
 if (releaseMetadataLength == 0) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L55

- Store `subdomain.length` in stack (#L55)

`uint256 subdomainLength = subdomain.length;
 if (!(bytes(subdomainLength) > 0)) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L378
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L535
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L635
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L669

- Store `_params.permissions.length` in stack (#L378)

`uint256 paramsPermissionsLength = params.permissions.length;
 if (paramsPermissionsLength > 0) {`

- Store `_params.permissions.length` in stack (#L535)

`uint256 paramsPermissionsLength = params.permissions.length;
 if (paramsPermissionsLength > 0) {`

- Store `_params.permissions.length` in stack (#L635)

`uint256 paramsPermissionsLength = params.permissions.length;
 if (paramsPermissionsLength > 0) {`

- Store `_initData` in stack (#L669)

`uint256 initDataLength = _initData.length;
 if (initDataLength > 0) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16

- Use `++i` instead of `i++` (#L16)

`for (uint256 i; i < nameLength;) {
 unchecked { ++i; }`

- Use `unchecked` when incrementing at the end of for loop (#L16)

`for (uint256 i; i < nameLength;) {
 unchecked { ++i; }`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L92

- Store `data.length` in stack (#L92)

`uint256 dataLength = data.length;
 if (dataLength != 0x20) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L105
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L125

- Store `_payload.currentHelpers.length` in storage (#L105)

`uint256 payloadCurrentHelpersLength = _payload.currentHelpers.length;
 payloadCurrentHelpersLength != 0 ? 3 : 2`

- Store `_payload.currentHelpers.length` in storage (#L125)

`uint256 payloadCurrentHelpersLength = _payload.currentHelpers.length;
 if (payloadCurrentHelpersLength != 0) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L145
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L165

- Store `_payload.currentHelpers.length` in storage (#L145)

`uint256 payloadCurrentHelpersLength = _payload.currentHelpers.length;
 payloadCurrentHelpersLength != 0 ? 3 : 2`

- Store `_payload.currentHelpers.length` in storage (#L165)

`uint256 payloadCurrentHelpersLength = _payload.currentHelpers.length;
 if (payloadCurrentHelpersLength != 0) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L130

- Store `_actions.length` in stack (#L130)

`uint256 actionsLength = _actions.length;
 for (uint256 i; i < actionsLength; ) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L124

- Store `_actions.length` in stack (#L124)

`uint256 actionsLength = _actions.length;
 for (uint256 i; i < actionsLength; ) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L280

- Store `data.length` in stack (#L280)

`uint256 dataLength = data.length;
 return success && data.length == 0x20;`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L156
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L252

- Store `_members.length` in stack (#L156)

`uint256 membersLength = _members.length;
  uint16 newAddresslistLength = uint16(addresslistLength() - membersLength);`

- Store `_actions.length` in stack (#L252)

`uint256 actionsLength = _actions.length;
 for (uint256 i; i < actionsLength;) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L60
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L78
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L90

- Store `_newAddresses.length` in stack (#L60)

`uint256 newAddressesLength = _newAddresses.length;
 for (uint256 i; i < newAddressesLength;) {`

- Store `_exitingAddresses.length` in stack (#L78)

`uint256 exitingAddressesLength = _exitingAddresses.length;
 for (uint256 i; i < exitingAddressesLength;) {`

- Store `_exitingAddresses.length` in stack (#L90)

`uint256 exitingAddressesLength = _exitingAddresses.length;
 _addresslistLengthCheckpoints.push(_uncheckedSub, exitingAddressesLength);`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L70
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L72
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L73
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L81

- Store `_mintSettings.receivers.length` in stack (#L70)

`uint256 mintSettingsReceiversLength = _mintSettings.receivers.length;
 if (mintSettingsReceiversLength != mintSettingsAmountsLength) {`

- Store `_mintSettings.amounts.length` in stack (#L70)

`uint256 mintSettingsAmountsLength = _mintSettings.amounts.length;
 if (mintSettingsReceiversLength != mintSettingsAmountsLength) {`

- Store `_mintSettings.receivers.length` in stack (#L71)

`uint256 mintSettingsReceiversLength = _mintSettings.receivers.length;
 receiversArrayLength: mintSettingsReceiversLength,`

- Store `_mintSettings.amounts.length` in stack (#L72)

`uint256 mintSettingsAmountsLength = _mintSettings.amounts.length;
 amountsArrayLength: mintSettingsAmountsLength;`

- Store `_mintSettings.receivers.length` in stack (#L81)

`uint256 mintSettingsReceiversLength = _mintSettings.receivers.length;
 if (mintSettingsReceiversLength != mintSettingsAmountsLength) {`