At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol 

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol

- Declare custom errors between contract declaration and import statements

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L15
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19

- Use private variable naming convention (_var) (#L15)

`IDAO private immutable _dao;`

- Use `dao_` instead of `_dao` in constructor, for naming convention (#L19)

`constructor(IDAO dao_) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L16
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L20

- Use private variable naming convention (#L16)

`IDAO private _dao;`

- Use `dao_` instead of `_dao` in init function, for naming convention (#L20)

`function __DaoAuthorizableUpgradeable_init(IDAO dao_) internal onlyInitializing {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L17

- Follow private variable naming convention (#L17)

`Counters.Counter private _proposalCounter;`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L17

- Follow private variable naming convention (#L17)

`CountersUpgradeable.Counter private _proposalCounter;`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L8
    https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/BitMap.sol#L16

- Specifiy function visibility (#L8)

`function hasBit(uint256 bitmap, uint8 index) *external/public/internal/private* pure returns (bool) {`

- Specify function visibility (#L16)

`function flipBit(uint256 bitmap, uint8 index) *external/public/internal/private* pure returns (uint256) {`