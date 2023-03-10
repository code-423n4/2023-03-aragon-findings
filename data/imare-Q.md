## **QA-01**: No min interval between start and end proposal date when proposal is created

Inside `Multisig#createPoposal` there is no verification that user will have enough time to for approving this proposal. A check for minimal interval between start and end date is missing.

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L205-L262

## **QA-02**: There should be a method for `PluginSetupProcessor` to verify if uninstalling a plugin version will work in the future

Manually checking that the prepared data for uninstall after installing a plugin can be a tedious maybe also unnecessary work.

The `PluginSetupProcessor` should have a method that:
1. calls for the preparation of `install data` for a version of a plugin then
2. for the same plugin version calls for preparation of `uninstall data` with the same input as in the install data preparation call
3. and finally checks that all given permission that are imposed by the `install data` are revoked by the `uninstall data`.

By having this method as a view returning bool on successfully rollback of permissions the DAO has assurance that uninstallation of a plugin will work.