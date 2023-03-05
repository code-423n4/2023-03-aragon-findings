At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol
Line: 15, 19

- Use private variable naming convention (_var)

`IDAO private immutable _dao;`

- Use `dao_` instead of `_dao` in constructor, so following private var naming convention is possible

`constructor(IDAO dao_) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol
Line: 16, 20

- Use private variable naming convention (_var)

`IDAO private _dao;`

- Use `dao_` instead of `_dao` in init function, so following private var naming convention is possible

`function __DaoAuthorizableUpgradeable_init(IDAO dao_) internal onlyInitializing {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol
Line: 17

- Follow private variable naming convention

`Counters.Counter private _proposalCounter;`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol
Line: 17

- Follow private variable naming convention

`CountersUpgradeable.Counter private _proposalCounter;`