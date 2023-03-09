# PluginSetupProcessor.sol doesn't check if the plugin's address is zero

In [PluginSetupProcessor.sol#L303](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L303)

`(plugin, preparedSetupData) = PluginSetup(version.pluginSetup).prepareInstallation(
    _dao,
    _params.data
    );
`

## Severity : low

## Recommended Mitigation Steps
The tx should revert if plugin address is zero. 