At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L15
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19

- Use private variable naming convention (_var)

`IDAO private immutable _dao;`

- Use `dao_` instead of `_dao` in constructor, so following private var naming convention is possible

`constructor(IDAO dao_) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L16
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L20

- Use private variable naming convention (_var)

`IDAO private _dao;`

- Use `dao_` instead of `_dao` in init function, so following private var naming convention is possible

`function __DaoAuthorizableUpgradeable_init(IDAO dao_) internal onlyInitializing {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L17

- Follow private variable naming convention

`Counters.Counter private _proposalCounter;`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L17

- Follow private variable naming convention

`CountersUpgradeable.Counter private _proposalCounter;`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L8
    https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L16

- Specifiy function visibility

`function hasBit(uint256 bitmap, uint8 index) *external/public/internal/private* pure returns (bool) {`
`function flipBit(uint256 bitmap, uint8 index) *external/public/internal/private* pure returns (uint256) {`