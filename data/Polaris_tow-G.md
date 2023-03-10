# SETTING THE CONSTRUCTOR TO PAYABLE
Saves ~13 gas per instance
https://github.com/aragon/osx/blob/4fcb8bc06356350a2725b35bad7cbe95dd188f63/packages/contracts/src/core/dao/DAO.sol#L92
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19
https://github.com/aragon/osx/blob/0e8c3e9ed708e3589ebd52d8aaa57f6d95199461/packages/contracts/src/test/dao/DAOMock.sol#L14
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/core/plugin/PluginCloneable.sol#L16
https://github.com/aragon/osx/blob/0e8c3e9ed708e3589ebd52d8aaa57f6d95199461/packages/contracts/src/framework/dao/DAOFactory.sol#L53
https://github.com/aragon/osx/blob/0e8c3e9ed708e3589ebd52d8aaa57f6d95199461/packages/contracts/src/framework/dao/DAORegistry.sol#L30
https://github.com/aragon/osx/blob/6e73e744c11fdcf824695f7581b6d3fa1e65e4d6/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25
https://github.com/aragon/osx/blob/6e73e744c11fdcf824695f7581b6d3fa1e65e4d6/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L23
https://github.com/aragon/osx/blob/7b3ae59042d62d68d21146f846d52b068a1bced2/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L49
`constructor() {`

#  EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/core/dao/DAO.sol#L40
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/core/dao/DAO.sol#L43
https://github.com/aragon/osx/blob/0e8c3e9ed708e3589ebd52d8aaa57f6d95199461/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18
https://github.com/aragon/osx/blob/0e8c3e9ed708e3589ebd52d8aaa57f6d95199461/packages/contracts/src/framework/dao/DAORegistry.sol#L15
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/plugins/token/MerkleMinter.sol#L24
https://github.com/aragon/osx/blob/c5c7c23f007deb1414abfc5dda4900b19fb6051b/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L52
https://github.com/aragon/osx/blob/c5c7c23f007deb1414abfc5dda4900b19fb6051b/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/plugins/governance/admin/Admin.sol#L23
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L31
https://github.com/aragon/osx/blob/7b3ae59042d62d68d21146f846d52b068a1bced2/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27
https://github.com/aragon/osx/blob/ea81c3830eed88418f34fc4108b405c454271b29/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L182
`bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
        keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");`
# USAGE OF UINT/INT SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Use a larger size then downcast where needed.
https://github.com/aragon/osx/blob/4fcb8bc06356350a2725b35bad7cbe95dd188f63/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L33
https://github.com/aragon/osx/blob/4fcb8bc06356350a2725b35bad7cbe95dd188f63/packages/contracts/src/test/token/TestERC20.sol#L8
https://github.com/aragon/osx/blob/0e8c3e9ed708e3589ebd52d8aaa57f6d95199461/packages/contracts/src/framework/plugin/repo/IPluginRepo.sol#L20
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L36
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L49
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L35
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L20
https://github.com/aragon/osx/blob/24ded72ca5352113313573771bc88a3a527c1cd9/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol#L48
https://github.com/aragon/osx/blob/6e73e744c11fdcf824695f7581b6d3fa1e65e4d6/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L103
`uint16 _currentBuild`








