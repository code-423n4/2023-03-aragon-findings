## token/ERC20/governance/GovernanceERC20

* [`initialize` does not call `__ERC20Votes_init` or `__ERC165Storage_init` although these init functions are currently empty, something that may change in a future version of `ERC20VotesUpgradeable` or `ERC165StorageUpgradeable`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L69-L88)

## core/dao/DAO

* [`initialize` does not call `__UUPSUpgradeable_init` or `__ERC165Storage_init` although these init functions are currently empty, something that may change in a future version of `UUPSUpgradeable` or `ERC165StorageUpgradeable`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L110-L118)
* [`daoURI_` natspec missing in `initialize`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L96-L103)

## plugins/governance/majority-voting/MajorityVotingBase

* [`_voter` is missing from `_vote` natspec](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L444-L447)

## framework/utils/ens/ENSSubdomainRegistrar

* [`initialize` does not call `__UUPSUpgradeable_init` although this init function is currently empty, something that may change in a future version of `UUPSUpgradeable`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L58-L69)
* [Emit event in `setDefaultResolver`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L103-L107)
* [Emit event in `registerSubnode` when registering a new subnode](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L86-L95)

## framework/dao/DAOFactory

* [There is an incorrect comment in `createDao`: permission is revoked from `address(this)`, not from `pluginSetupProcessor` at L134](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L134)
* Typo `Temporarly` -> `temporary` in `createDao` ([here](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L84), [here](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L87), [here](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L123-L124), and [here](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L134))
