# QA Report

## Use `indexed` for event parameters

Index event fields make the field more quickly accessible to off-chain tools that parse events. 

If the variable is value type (uint, address, bool), then using `indexed` saves gas. Otherwise, each index field costs extra gas during emission.

Instances: 18
- [packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L33)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L228](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L228)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L229](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L229)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L230](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L230)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L231](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L231)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L119](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L119)
- [packages/contracts/src/plugins/token/IMerkleDistributor.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L15)
- [packages/contracts/src/plugins/token/IMerkleMinter.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L21)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L100](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L100)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L101](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L101)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L109](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L109)
- [packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L25)
- [packages/contracts/src/core/utils/CallbackHandler.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L25)
- [packages/contracts/src/core/dao/IDAO.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L63)
- [packages/contracts/src/core/dao/IDAO.sol#L91](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L91)
- [packages/contracts/src/core/dao/IDAO.sol#L99](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L99)
- [packages/contracts/src/core/dao/IDAO.sol#L111](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L111)
- [packages/contracts/src/core/dao/IDAO.sol#L119](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L119)

<br>

## Lines too long

[Solidity's style guide](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length) states lines should be kept within 79 characters.
 
Also, if lines exceed 164 characters then a horizontal scroll bar will be required when viewing the file on Github.

Extensions such as prettier are a simple solution.

Instances: 89
- [packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L44](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L44)
- [packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L49)
- [packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L54](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L54)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L27)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L31)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L33)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L34](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L34)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L62](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L62)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L63)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L108](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L108)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L136](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L136)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L428](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L428)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L527](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L527)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L593](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L593)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L16)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L25)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L39](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L39)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L33)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L201](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L201)
- [packages/contracts/src/plugins/governance/multisig/Multisig.sol#L304](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L304)
- [packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L11)
- [packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L15)
- [packages/contracts/src/plugins/governance/admin/Admin.sol#L61](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L61)
- [packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L75](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L75)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L31)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L51](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L51)
- [packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L68](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L68)
- [packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L49)
- [packages/contracts/src/plugins/token/MerkleDistributor.sol#L128](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L128)
- [packages/contracts/src/plugins/token/MerkleMinter.sol#L97](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L97)
- [packages/contracts/src/utils/Proxy.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L11)
- [packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52)
- [packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L74](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L74)
- [packages/contracts/src/framework/utils/RegistryUtils.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L9)
- [packages/contracts/src/framework/utils/TokenFactory.sol#L72](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L72)
- [packages/contracts/src/framework/utils/TokenFactory.sol#L74](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L74)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L15)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72)
- [packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110)
- [packages/contracts/src/framework/dao/DAORegistry.sol#L72](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L72)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L41](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L41)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L61](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L61)
- [packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L111](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L111)
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L255](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L255)
- [packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L71](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L71)
- [packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L14)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L41](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L41)
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L29](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L29)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L17](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L17)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L49)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L50](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L50)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L63)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L85](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L85)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L86](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L86)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L110](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L110)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L111](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L111)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L158](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L158)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L213](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L213)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L245](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L245)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L395](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L395)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L551](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L551)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L611](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L611)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L649](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L649)
- [packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L23](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L23)
- [packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L63)
- [packages/contracts/src/core/utils/auth.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/auth.sol#L14)
- [packages/contracts/src/core/utils/CallbackHandler.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L7)
- [packages/contracts/src/core/utils/CallbackHandler.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L52)
- [packages/contracts/src/core/dao/IEIP4824.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IEIP4824.sol#L9)
- [packages/contracts/src/core/dao/DAO.sol#L134](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L134)
- [packages/contracts/src/core/dao/DAO.sol#L268](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L268)
- [packages/contracts/src/core/dao/DAO.sol#L338](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L338)
- [packages/contracts/src/core/dao/IDAO.sol#L40](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L40)
- [packages/contracts/src/core/dao/IDAO.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L43)
- [packages/contracts/src/core/dao/IDAO.sol#L58](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L58)
- [packages/contracts/src/core/dao/IDAO.sol#L96](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L96)
- [packages/contracts/src/core/dao/IDAO.sol#L121](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L121)
- [packages/contracts/src/core/dao/IDAO.sol#L127](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L127)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L15)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L53](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L53)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L59](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L59)
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L65](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L65)
- [packages/contracts/src/core/plugin/PluginCloneable.sol#L13](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L13)
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L19](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L19)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L21)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L24](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L24)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L25)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L26](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L26)

<br>

## OpenZeppelin draft dependencies

The following imported contracts are still a draft and are not considered ready for mainnet use.

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

Instances: 3
- [packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6)
- [packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8)

<br>

## Remove unused imports

Improves readability and saves gas.

Instances: 28
- [packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L5)
- [packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L9)
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L8)
- [packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L5)
- [packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L10](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L10)
- [packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L5)
- [packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/IMultisig.sol#L5)
- [packages/contracts/src/plugins/token/IMerkleDistributor.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L6)
- [packages/contracts/src/plugins/token/IMerkleDistributor.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleDistributor.sol#L8)
- [packages/contracts/src/plugins/token/MerkleDistributor.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L8)
- [packages/contracts/src/plugins/token/MerkleMinter.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L6)
- [packages/contracts/src/plugins/token/IMerkleMinter.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L5)
- [packages/contracts/src/plugins/token/IMerkleMinter.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L8)
- [packages/contracts/src/framework/dao/DAOFactory.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L8)
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L6)
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L7)
- [packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L9)
- [packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L7)
- [packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L6)
- [packages/contracts/src/core/dao/DAO.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L11)
- [packages/contracts/src/core/plugin/membership/IMembership.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/membership/IMembership.sol#L5)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L15)
- [packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L16)
- [packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L5)
- [packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L6)
- [packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L7)

<br>

## Missing `address(0)` checks in constructor/initialize

Failing to check for invalid parameters on deployment may result in an erroneous input and require an expensive redeployment.

Instances: 2
- [packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L120](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L120)
- [packages/contracts/src/core/dao/DAO.sol#L104](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L104)

<br>
