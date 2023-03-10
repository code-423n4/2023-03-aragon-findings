# Summary
|ID     | Finding|  Gas saved | Instances|
|:----: | :---           |  :----:         |:----:         |
|G-01      |Make up to 3 fields in an event indexed| 473 |  - |
|G-02      |Switch if statements in `_isGranted()`| 40 | 1 |
|G-03      |Make own counter instead of using OpenZeppelin Counter| 90 | 2 |
|G-04      |Using double if/require statement instead of && consumes less gas| 40 | 12 |
|G-05      |Remove return values when they are never used| 12805 | 2 |
|G-06      |Make for loops unchecked| - | 2 |
|G-07      |Use msg.sender directly instead of the function `_msgSender()`| 30 | 12 |
|G-08      |Don't save block and transaction properties to a memory variable| - | 1 |
|G-09      |Save state variables in memory when they are used more than once| 180 | 2 |
|G-10      |Place if statement before assignement to a variable| - | 2 |
# Details
## [G-01] Make up to 3 fields in an event indexed
Indexing all fields in an event saves gas. If the event has more than 3 fields, only index 3.

[DAO.sol#L89](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L89): `setDaoURI()` gas saved 473 
```diff
-   event NewURI(string daoURI);
+   event NewURI(string indexed daoURI);
```
This can be done for every event.
## [G-02] Switch if statements in `_isGranted()`
The function [`_isGranted()`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L297-L323) is used in every function with the `auth()` modifier. The following if statements can be switched so when an authorized caller calls a function, it needs to do one less if statement because it will only check ALLOW_FLAG and return true. Where as previous it first checked the UNSET_FLAG and afterwards ALLOW_FLAG. This saves around 40 gas for every function that has access control (uses the auth modifier) and where the caller is authorized. It only dissadvantages unauthorized callers.
```solidity
        if (accessFlagOrCondition == UNSET_FLAG) return false;
        if (accessFlagOrCondition == ALLOW_FLAG) return true;
```
can be changed to:
```solidity
        if (accessFlagOrCondition == ALLOW_FLAG) return true;
        if (accessFlagOrCondition == UNSET_FLAG) return false;
```
## [G-03] Make own counter instead of using OpenZeppelin Counter
Both [Proposal.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol) and [ProposalUpgradeable.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol) use the Counter from OpenZeppelin. It's more gas efficiënt if you create your own counter.

```diff
Proposal.sol

L14:
-   using CountersUpgradeable for CountersUpgradeable.Counter;

    /// @notice The incremental ID for proposals and executions.
-   CountersUpgradeable.Counter private proposalCounter;
+   uint256 private counter;

L20:
    function proposalCount() public view override returns (uint256) {
-       return proposalCounter.current();
+       return counter;
    }

L33:
    function _createProposalId() internal returns (uint256 proposalId) {
-       proposalId = proposalCount();
+       proposalId = counter++;
-       proposalCounter.increment();
    }
```
|Link     | Function |  Gas saved |
|:----      |  :----         |:----:         |
|[Admin.sol#L62-L79](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L62-L79)| `executeProposal()` |96 |
|[AddresslistVoting.sol#L83-L140](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L83-L140)| `createProposal()` |93 |
|[Multisig.sol#L205-L286](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L205-L286)| `createProposal()` |93 |

## [G-04] Using double if/require statement instead of && consumes less gas
Saves around 15 gas for require statements and 40 for if statements
```diff
-       if (_where == ANY_ADDR && _who == ANY_ADDR) {
+       if (_where == ANY_ADDR) {
+         if (_who == ANY_ADDR) {
            revert AnyAddressDisallowedForWhoAndWhere();
          }    
+       }
```
- [PermissionManager.sol#L236-L238](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L236-L238)
- [PluginSetupProcessor.sol#L700-L710](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L700-L710)
- [RegistryUtils.sol#L20-L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L20-L27)
- [AddresslistVoting.sol#L97-L99](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97-L99)
- [AddresslistVoting.sol#L185-L187](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L185-L187)
- [AddresslistVoting.sol#L214-L219](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L214-L219)
- [TokenVoting.sol#L182-L185](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L182-L185)
- [TokenVoting.sol#L211-L216](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L211-L216)
- [Multisig.sol#L216-L218](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216-L218)
- [Multisig.sol#L283-L285](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L283-L285)
- [GovernanceERC20.sol#L116-L118](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L116-L118)
- [GovernanceWrappedERC20.sol#L106-L108](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L106-L108)
## [G-05] Remove return values when they are never used
The function [`execute()`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L176) has two return variables, `execResults` and `failureMap`. Right now the function is only called in [Proposal.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L72-L80) in `_executeProposal()`. This function saves the return variables but does nothing with them and also returns it. 

The `_executeProposal()` is called in [Admin.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L62-L78), [Multisig.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L348-L359) and [MajorityVotingBase.sol](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L457-L466). All those functions only call the `_executeProposal()` function but don't even save the return variables.

Long story, short. The functions `execute()` and `_executeProposal()` don't need to return anything. When you remove the return statement and values, 12805 gas is saved for the `execute()` function.

The function `createProposal()` in [AddresslistVoting](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L91) and [TokenVoting](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L79) returns proposalId that is used nowhere else. This return statement can also be removed.
## [G-06] Make for loops unchecked
The risk of for loops getting overflowed is extremely low. Because it always increments by 1 and is limited to the arrays length. Even if the arrays are extremely long, it will take a massive amount of time and gas to let the for loop overflow. The longer the array, the more gas you will save.
```diff
-       for (uint256 i; i < _pluginSettings.length; ++i) {
+       for (uint256 i; i < _pluginSettings.length;) {
            // Prepare plugin.
            (
                address plugin,
                IPluginSetup.PreparedSetupData memory preparedSetupData
            ) = pluginSetupProcessor.prepareInstallation(
                    address(createdDao),
                    PluginSetupProcessor.PrepareInstallationParams(
                        _pluginSettings[i].pluginSetupRef,
                        _pluginSettings[i].data
                    )
                );

            // Apply plugin.
            pluginSetupProcessor.applyInstallation(
                address(createdDao),
                PluginSetupProcessor.ApplyInstallationParams(
                    _pluginSettings[i].pluginSetupRef,
                    plugin,
                    preparedSetupData.permissions,
                    hashHelpers(preparedSetupData.helpers)
                )
            );
+           unchecked {
+               ++i;
+           }
        }
```
- [DAOFactory.sol#L95-L117](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95-L117)
- [RegistryUtils.sol#L16-L36](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L16-L36)
## [G-07] Use msg.sender directly instead of the function `_msgSender()`
The project use `_msgSender()` from OpenZeppelin Context contract. This function returns msg.sender so it does exactly the same as msg.sender
Saves around 30 gas for every instance
- [DaoAuthorizable.sol#L32](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L32)
- [DaoAuthorizableUpgradeable.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L33)
- [Admin.sol#L70](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L70)
- [MajorityVotingBase.sol#L270](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L270)
- [AddresslistVoting.sol#L97](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97)
- [AddresslistVoting.sol#L102](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L102)
- [AddresslistVoting.sol#L98](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L98)
- [TokenVoting.sol#L91-L92](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L91-L92)
- [TokenVoting.sol#L96](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L96)
- [Multisig.sol#L216-L217](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216-L217)
- [Multisig.sol#L231](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L231)
- [Multisig.sol#L266](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L266)
## [G-08] Don't save block and transaction properties to a memory variable
Most of the block and transaction properties only use 2 gas. While reading memory uses the MLOAD opcode which is 3 gas. And you also save some gas because you don't have to write it to a variable.
- [MajorityVotingBase.sol#L270](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L270)
## [G-09] Save state variables in memory when they are used more than once
Reading from storage costs 2100 gas for the first time and 100 for the second time. Reading from memory only costs 3 gas.
When you save the variable to memory first you have the 2100 gas cost. 
But when a variable is used more than once you save 97 gas for each extra read.

Example:
```solidity
        return
            (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >
            proposal_.parameters.supportThreshold * proposal_.tally.no;
```
Can be changed to
```solidity
        uint32 treshold =  proposal_.parameters.supportThreshold;
        return
            (RATIO_BASE - treshold) * proposal_.tally.yes >
            treshold * proposal_.tally.no;
```
|Link     | Variable | Function |  Gas saved |
|:----     |  :----  |  :----         |:----:         |
|[MajorityVotingBase.sol#L319-L320](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L319-L320)|`proposal_.parameters.supportThreshold`| `vote` |188 |
|[MajorityVotingBase.sol#L335-L337](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L335-L337)|`proposal_.parameters.supportThreshold`| `vote` |148 |
## [G-10] Place if statement before assignement to a variable
When you do an if check before you assign the value to a variable and it fails you save the cost of assigning to a variable first.
- [MajorityVotingBase.sol#L573-L577](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L573-L577)
- [MajorityVotingBase.sol#L587-L589](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L587-L589)

