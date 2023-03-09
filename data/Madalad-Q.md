# Low Risk Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[L-01]|OpenZeppelin draft dependencies|3|

Total issues: 1

Total instances: 3

&nbsp;
# Non-Critical Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[N-01]|Use `indexed` for event parameters|18|
|[N-02]|Lines too long|89|
|[N-03]|Remove unused imports|28|
|[N-04]|Missing `address(0)` checks in constructor/initialize|2|

Total issues: 4

Total instances: 137

&nbsp;
# Low Risk Issues
## [L-01] OpenZeppelin draft dependencies

The following imported contracts are still a draft and are not considered ready for mainnet use.

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

Instances: 3
- [contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L6)
- [contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L5)
- [contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L8)

&nbsp;

&nbsp;

# Non-Critical Issues
## [N-01] Use `indexed` for event parameters

Index event fields make the field more quickly accessible to off-chain tools that parse events. 

If the variable is value type (uint, address, bool), then using `indexed` saves gas. Otherwise, each index field costs extra gas during emission.

Instances: 18
- [contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L33)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L228](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L228)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L229](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L229)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L230](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L230)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L231](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L231)
- [contracts/src/plugins/governance/multisig/Multisig.sol#L119](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/Multisig.sol#L119)
- [contracts/src/plugins/token/IMerkleDistributor.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/IMerkleDistributor.sol#L15)
- [contracts/src/plugins/token/IMerkleMinter.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/IMerkleMinter.sol#L21)
- [contracts/src/framework/plugin/repo/PluginRepo.sol#L100](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepo.sol#L100)
- [contracts/src/framework/plugin/repo/PluginRepo.sol#L101](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepo.sol#L101)
- [contracts/src/framework/plugin/repo/PluginRepo.sol#L109](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepo.sol#L109)
- [contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L25)
- [contracts/src/core/utils/CallbackHandler.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/utils/CallbackHandler.sol#L25)
- [contracts/src/core/dao/IDAO.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L63)
- [contracts/src/core/dao/IDAO.sol#L91](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L91)
- [contracts/src/core/dao/IDAO.sol#L99](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L99)
- [contracts/src/core/dao/IDAO.sol#L111](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L111)
- [contracts/src/core/dao/IDAO.sol#L119](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L119)

&nbsp;
## [N-02] Lines too long

[Solidity's style guide](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length) states lines should be kept within 79 characters.
 
Also, if lines exceed 164 characters then a horizontal scroll bar will be required when viewing the file on Github.

Extensions such as prettier are a simple solution.

Instances: 89
- [contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L44](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L44)
- [contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L49)
- [contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L54](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L54)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L27)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L31)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L33)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L34](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L34)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L62](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L62)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L63)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L108](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L108)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L136](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L136)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L428](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L428)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L527](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L527)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L593](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L593)
- [contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L16)
- [contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L25)
- [contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L39](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L39)
- [contracts/src/plugins/governance/multisig/Multisig.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/Multisig.sol#L33)
- [contracts/src/plugins/governance/multisig/Multisig.sol#L201](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/Multisig.sol#L201)
- [contracts/src/plugins/governance/multisig/Multisig.sol#L304](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/Multisig.sol#L304)
- [contracts/src/plugins/governance/multisig/IMultisig.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/IMultisig.sol#L11)
- [contracts/src/plugins/governance/multisig/IMultisig.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/IMultisig.sol#L15)
- [contracts/src/plugins/governance/admin/Admin.sol#L61](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/admin/Admin.sol#L61)
- [contracts/src/plugins/governance/admin/AdminSetup.sol#L75](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/admin/AdminSetup.sol#L75)
- [contracts/src/plugins/counter-example/v2/CounterV2.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/counter-example/v2/CounterV2.sol#L31)
- [contracts/src/plugins/counter-example/v2/CounterV2.sol#L51](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/counter-example/v2/CounterV2.sol#L51)
- [contracts/src/plugins/counter-example/v2/CounterV2.sol#L68](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/counter-example/v2/CounterV2.sol#L68)
- [contracts/src/plugins/counter-example/v1/CounterV1.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/counter-example/v1/CounterV1.sol#L49)
- [contracts/src/plugins/token/MerkleDistributor.sol#L128](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/MerkleDistributor.sol#L128)
- [contracts/src/plugins/token/MerkleMinter.sol#L97](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/MerkleMinter.sol#L97)
- [contracts/src/utils/Proxy.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/utils/Proxy.sol#L11)
- [contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L52)
- [contracts/src/framework/utils/InterfaceBasedRegistry.sol#L74](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L74)
- [contracts/src/framework/utils/RegistryUtils.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/RegistryUtils.sol#L9)
- [contracts/src/framework/utils/TokenFactory.sol#L72](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/TokenFactory.sol#L72)
- [contracts/src/framework/utils/TokenFactory.sol#L74](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/TokenFactory.sol#L74)
- [contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L15)
- [contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72)
- [contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110)
- [contracts/src/framework/dao/DAORegistry.sol#L72](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/dao/DAORegistry.sol#L72)
- [contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L41](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L41)
- [contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L61](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L61)
- [contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L111](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L111)
- [contracts/src/framework/plugin/repo/PluginRepo.sol#L255](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepo.sol#L255)
- [contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L71](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L71)
- [contracts/src/framework/plugin/repo/IPluginRepo.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/IPluginRepo.sol#L14)
- [contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L41](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol#L41)
- [contracts/src/framework/plugin/setup/PluginSetup.sol#L29](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetup.sol#L29)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L17](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L17)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L49](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L49)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L50](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L50)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L63)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L85](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L85)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L86](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L86)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L110](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L110)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L111](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L111)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L158](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L158)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L213](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L213)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L245](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L245)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L395](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L395)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L551](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L551)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L611](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L611)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L649](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L649)
- [contracts/src/framework/plugin/setup/IPluginSetup.sol#L23](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/IPluginSetup.sol#L23)
- [contracts/src/framework/plugin/setup/IPluginSetup.sol#L63](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/IPluginSetup.sol#L63)
- [contracts/src/core/utils/auth.sol#L14](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/utils/auth.sol#L14)
- [contracts/src/core/utils/CallbackHandler.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/utils/CallbackHandler.sol#L7)
- [contracts/src/core/utils/CallbackHandler.sol#L52](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/utils/CallbackHandler.sol#L52)
- [contracts/src/core/dao/IEIP4824.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IEIP4824.sol#L9)
- [contracts/src/core/dao/DAO.sol#L134](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/DAO.sol#L134)
- [contracts/src/core/dao/DAO.sol#L268](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/DAO.sol#L268)
- [contracts/src/core/dao/DAO.sol#L338](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/DAO.sol#L338)
- [contracts/src/core/dao/IDAO.sol#L40](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L40)
- [contracts/src/core/dao/IDAO.sol#L43](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L43)
- [contracts/src/core/dao/IDAO.sol#L58](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L58)
- [contracts/src/core/dao/IDAO.sol#L96](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L96)
- [contracts/src/core/dao/IDAO.sol#L121](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L121)
- [contracts/src/core/dao/IDAO.sol#L127](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/IDAO.sol#L127)
- [contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L15)
- [contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L53](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L53)
- [contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L59](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L59)
- [contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L65](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L65)
- [contracts/src/core/plugin/PluginCloneable.sol#L13](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/plugin/PluginCloneable.sol#L13)
- [contracts/src/token/ERC20/governance/GovernanceERC20.sol#L19](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L19)
- [contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L21](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L21)
- [contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L24](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L24)
- [contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L25](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L25)
- [contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L26](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L26)

&nbsp;
## [N-03] Remove unused imports

Improves readability and saves gas.

Instances: 28
- [contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L5)
- [contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L9)
- [contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L8)
- [contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L5)
- [contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L10](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L10)
- [contracts/src/plugins/governance/multisig/MultisigSetup.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L5)
- [contracts/src/plugins/governance/multisig/IMultisig.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/governance/multisig/IMultisig.sol#L5)
- [contracts/src/plugins/token/IMerkleDistributor.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/IMerkleDistributor.sol#L6)
- [contracts/src/plugins/token/IMerkleDistributor.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/IMerkleDistributor.sol#L8)
- [contracts/src/plugins/token/MerkleDistributor.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/MerkleDistributor.sol#L8)
- [contracts/src/plugins/token/MerkleMinter.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/MerkleMinter.sol#L6)
- [contracts/src/plugins/token/IMerkleMinter.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/IMerkleMinter.sol#L5)
- [contracts/src/plugins/token/IMerkleMinter.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/plugins/token/IMerkleMinter.sol#L8)
- [contracts/src/framework/utils/ens/ENSMigration.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/ens/ENSMigration.sol#L7)
- [contracts/src/framework/utils/ens/ENSMigration.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/utils/ens/ENSMigration.sol#L8)
- [contracts/src/framework/dao/DAOFactory.sol#L8](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/dao/DAOFactory.sol#L8)
- [contracts/src/framework/plugin/setup/PluginSetup.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetup.sol#L6)
- [contracts/src/framework/plugin/setup/PluginSetup.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetup.sol#L7)
- [contracts/src/framework/plugin/setup/PluginSetup.sol#L9](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetup.sol#L9)
- [contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L7)
- [contracts/src/framework/plugin/setup/IPluginSetup.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/setup/IPluginSetup.sol#L6)
- [contracts/src/core/dao/DAO.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/DAO.sol#L11)
- [contracts/src/core/plugin/membership/IMembership.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/plugin/membership/IMembership.sol#L5)
- [contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L15](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L15)
- [contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L16](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L16)
- [contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L5)
- [contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L6](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L6)
- [contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L7](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol#L7)

&nbsp;
## [N-04] Missing `address(0)` checks in constructor/initialize

Failing to check for invalid parameters on deployment may result in an erroneous input and require an expensive redeployment.

Instances: 2
- [contracts/src/framework/plugin/repo/PluginRepo.sol#L120](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/framework/plugin/repo/PluginRepo.sol#L120)
- [contracts/src/core/dao/DAO.sol#L104](https://github.com/code-423n4/2023-03-aragon/blob/main/contracts/src/core/dao/DAO.sol#L104)

&nbsp;
