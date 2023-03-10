# [01] Emit events before external calls

Multiple functions in the project emit an event as the last statement. Wherever possible, consider emitting events before external calls. In case of reentrancy, funds are not at risk (for external call + event ordering), however emitting events after external calls can damage frontends and monitoring tools in case of reentrancy attacks.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L232-L235

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L77-L79

# [02] Use the latest version of solidity

The project is using v0.8.17. If possible, consider updating to the latest stable version v0.8.19.

# [03] Use the latest version of OpenZeppelin

Consider updating from OpenZeppelin 4.8.1 to 4.8.2 to ensure the contracts contain the latest security fixes.

# [04] Critical changes should use two-step procedure

Lack of two-step procedure for critical operations leaves them error-prone.

Consider adding a two-steps pattern on critical changes to avoid mistakenly transferring ownership of roles or critical functionalities to the wrong address.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L139-L143

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L283-L287

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L192-L196

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L413-L433

# [05] __gap can be defined in one contract and reused across the project

It's possible to define the __gap into a single contract and reuse across the other contracts. Please, refer to this reference [implementation](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol)

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L339

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L111

# [06] If possible, consider using weth instead of raw eth

weth will be more composable and easier to reason about in general. This is more of an arquitectural decision, but it can have security ramifications. Please, refer to this [video](https://youtu.be/bemW7CJqbog?t=916) where MakerDAO recommends using weth instead of eth.

# [07] Variable shadowing

For `DAORegistry.register()`, the argument `dao` is shadowed by `DaoAuthorizableUpgradeable.dao()`, Consider renaming the `dao` argument in `DAORegistry.register()`.

# [08] Check for stale values on setter functions 

Add a check ensuring that the new value if different than the current value to avoid emitting unnecessary events.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L139-L1431

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L283-L287

# [09] Missing event on state changes

Adding events will facilitate offchain monitoring.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L100-L108

# [10] Rename `DaoAuthorizableUpgradeable.sol` to `DAOAuthorizableUpgradeable.sol`

It will be more consistent with other filenames, e.g. `DAO.sol`.

# [11] Usage of return named variables and explicit values

Some functions return named variables, others return explicit values.

Following function returns an explicit value.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L20-L22

Following function return returns a named variable.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L33-L36

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

# [12] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

Consider moving receive and fallback above external functions.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L248-L273

# [13] Comments extend more than 120 lines

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider breaking down comments into multiple lines to avoid having large line lengths.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L134

# [14] Draft OpenZeppelin dependencies

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

Consider waiting until the contract is finalized. Otherwise, make sure that development team is aware of the risks of using a draft OpenZeppelin contract and accept the risk-benefit trade-off.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8

# [15] Named imports can be used

It's possible to name the imports for OpenZeppelin dependencies.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L5-L14
