## Missing zero address checks
MerkleMinter.sol = [53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L53)

ENSSubdomainRegistrar.sol - [82](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L82)

DAO.sol - [104](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L104), [239](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L239), [283](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L283)

DaoAuthorizable.sol - [19](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19)

DAOFactory.sol - [53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L53)

PluginRepoFactory.sol - [22](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22)

PluginSetupProcessor - [277](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277)

## Function that makes calls to external addresses is missing nonReentrant modifier
DAO.sol - [168](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L168)

## Remove unused imports
GovernanceWrappedERC20.sol - [15/16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L15-L16)

IGovernanceWrappedERC20.sol - [5/6/7](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L5-L7)

IMerkleDistributor.sol - [6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L6), [8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L8)

IMerkleMinter.sol - [5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L5), [8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L8)

MerkleDistributor.sol - [8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L8)

MerkleMinter.sol - [6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L6)

IMajorityVoting.sol - [5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L5)

MajorityVotingBase.sol - [9](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L9)

AddresslistVoting.sol - [8(RATIO_BASE)](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L8), [12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L12)

AddresslistVotingSetup.sol - [5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L5)

TokenVoting.sol - [10(RATIO_BASE)](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L10), [12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L12)

IMultisig.sol - [5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L5)

MultisigSetup.sol - [5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L5)

DAOFactory.sol - [8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L8)

PluginRepo.sol - [12](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L12)

IPluginSetup.sol - [6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L6)

PluginSetup.sol - [6/7](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L6-L7)

PluginSetupProcessor.sol - [7(IDAO)](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L7)

PluginSetupProcessorHelpers.sol - [7](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L7)

IMembership.sol - [5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/membership/IMembership.sol#L5)