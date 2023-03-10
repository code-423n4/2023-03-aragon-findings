## Gas griefing/theft is possible on unsafe external call
`return` data (bool success,) has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible gas grieving/theft problem is avoided as denoted in the link below:

https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19

Here are the two instances entailed:

[File: DAO.sol#L186-L188](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L186-L188)

```solidity
            (bool success, bytes memory response) = to.call{value: _actions[i].value}(
                _actions[i].data
            );
```
[File: TokenVotingSetup.sol#L277-L279](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L277-L279)

```solidity
        (bool success, bytes memory data) = token.staticcall(
            abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))
        );
```
## Lines too long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible.

Here are some of the instances entailed:

[File: Proxy.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L11)
[File: TokenFactory.sol#L72-L74](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L72-L74)
[File: InterfaceBasedRegistry.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52)
[File: InterfaceBasedRegistry.sol#L74](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L74)
[File: RegistryUtils.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L9)

## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instance below could be refactored conforming to most other instances in the code bases as follows:

[File: Proxy.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L5)

```diff
- import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
+ import {ERC1967Proxy} from "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
```
## Exact number of days in a year
Background: The true length of a year on Earth is 365.2422 days, or about 365.25 days. We keep our calendar in sync with the seasons by having most years 365 days long but making just under 1/4 of all years 366-day "leap" years.

Here is an affected code line that may be refactored as follows:

[File: MajorityVotingBase.sol#L544-L546](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L544-L546)

```diff
-        if (_votingSettings.minDuration > 365 days) {
-            revert MinDurationOutOfBounds({limit: 365 days, actual: _votingSettings.minDuration});
+        if (_votingSettings.minDuration > 365.25 days) {
+            revert MinDurationOutOfBounds({limit: 365.25 days, actual: _votingSettings.minDuration});
        }
```
## Non-compliant contract layout with Solidity's Style Guide
According to Solidity's Style Guide below:

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

In order to help readers identify which functions they can call, and find the constructor and fallback definitions more easily, functions should be grouped according to their visibility and ordered in the following manner:

constructor, receive function (if exists), fallback function (if exists), external, public, internal, private

And, within a grouping, place the `view` and `pure` functions last.

Additionally, inside each contract, library or interface, use the following order:

type declarations, state variables, events, modifiers, functions

Consider adhering to the above guidelines for all contract instances entailed.

## Missing checks for contract existence
Performing low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed as documented in the link below:

https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions

Consider having account existence checked prior to making `call` where possible.

Specifically, as already documented in the function NatSpec, `_isERC20()` below should have this concern rectified with a simple refactoring considering [`prepareInstallation()`](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L113) calling it does have this does not implement a satisfiable check either:

[File: TokenVotingSetup.sol#L273-L281](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L273-L281)

```diff
    /// @notice Unsatisfiably determines if the contract is an ERC20 token.
    /// @dev It's important to first check whether token is a contract prior to this call.
    /// @param token The token address
    function _isERC20(address token) private view returns (bool) {
+        uint256 size;
+        // solhint-disable-next-line no-inline-assembly
+        assembly { size := extcodesize(token) }
+        require(size > 0, "Account code size cannot be zero");

        (bool success, bytes memory data) = token.staticcall(
            abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))
        );
        return success && data.length == 0x20;
    }
```
## NatSpec comments   
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

The protocol has done a tremendous job providing detailed NatSpec across the codebase. Nevertheless, this can be more fully equipped in facilitating users/developers interacting with the protocol's smart contracts.

For instance, the function below could take in a complete set of NatSpec comments, i.e. `@return` like its all other counterparts:

[File: TokenVotingSetup.sol#L262-L281](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L262-L281)

```solidity
    /// @notice Retrieves the interface identifiers supported by the token contract.
    /// @dev It is crucial to verify if the provided token address represents a valid contract before using the below.
    /// @param token The token address
    function _getTokenInterfaceIds(address token) private view returns (bool[] memory) {
        bytes4[] memory interfaceIds = new bytes4[](3);
        interfaceIds[0] = type(IERC20Upgradeable).interfaceId;
        interfaceIds[1] = type(IVotesUpgradeable).interfaceId;
        interfaceIds[2] = type(IGovernanceWrappedERC20).interfaceId;
        return token.getSupportedInterfaces(interfaceIds);
    }

    /// @notice Unsatisfiably determines if the contract is an ERC20 token.
    /// @dev It's important to first check whether token is a contract prior to this call.
    /// @param token The token address
    function _isERC20(address token) private view returns (bool) {
        (bool success, bytes memory data) = token.staticcall(
            abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))
        );
        return success && data.length == 0x20;
    }
```
## Tokens accidentally sent to the contract cannot be recovered
It is deemed unrecoverable if the tokens accidentally arrive at the contract addresses, which has happened to many popular projects. Consider adding a recovery code to your critical contracts.

## Project Upgrade and Stop Scenario
At the start of the project, the system may need to be stopped or upgraded. Consider having a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.