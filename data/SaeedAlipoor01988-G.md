###############  Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
examples\core\dao\DAO.sol::40 => bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
examples\core\dao\DAO.sol::43 => bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
examples\core\dao\DAO.sol::46 => bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");
examples\core\dao\DAO.sol::50 => keccak256("SET_TRUSTED_FORWARDER_PERMISSION");
examples\core\dao\DAO.sol::54 => keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");
examples\core\dao\DAO.sol::58 => keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");
examples\core\permission\PermissionManager.sol::15 => bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");
examples\core\permission\PermissionManager.sol::347 => return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));
examples\core\plugin\PluginUUPSUpgradeable.sol::35 => bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");
examples\framework\dao\DAORegistry.sol::15 => bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");
examples\framework\dao\DAORegistry.sol::64 => bytes32 labelhash = keccak256(bytes(subdomain));
examples\framework\plugin\repo\PluginRepo.sol::49 => bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
examples\framework\plugin\repo\PluginRepo.sol::52 => bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");
examples\framework\plugin\repo\PluginRepo.sol::252 => return keccak256(abi.encodePacked(_tag.release, _tag.build));
examples\framework\plugin\repo\PluginRepoRegistry.sol::17 => keccak256("REGISTER_PLUGIN_REPO_PERMISSION");
examples\framework\plugin\repo\PluginRepoRegistry.sol::63 => bytes32 labelhash = keccak256(bytes(subdomain));
examples\framework\plugin\setup\PluginSetupProcessor.sol::28 => keccak256("APPLY_INSTALLATION_PERMISSION");
examples\framework\plugin\setup\PluginSetupProcessor.sol::31 => bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");
examples\framework\plugin\setup\PluginSetupProcessor.sol::35 => keccak256("APPLY_UNINSTALLATION_PERMISSION");
examples\framework\plugin\setup\PluginSetupProcessor.sol::38 => /// @dev The hash is computed via `keccak256(abi.encode([]))`.
examples\framework\plugin\setup\PluginSetupProcessor.sol::43 => /// @dev The hash is computed via `keccak256(abi.encode(0))`.
examples\framework\plugin\setup\PluginSetupProcessorHelpers.sol::33 => return keccak256(abi.encode(_dao, _plugin));
examples\framework\plugin\setup\PluginSetupProcessorHelpers.sol::51 => keccak256(
examples\framework\plugin\setup\PluginSetupProcessorHelpers.sol::57 => keccak256(_data),
examples\framework\plugin\setup\PluginSetupProcessorHelpers.sol::72 => keccak256(
examples\framework\plugin\setup\PluginSetupProcessorHelpers.sol::80 => return keccak256(abi.encode(_helpers));
examples\framework\plugin\setup\PluginSetupProcessorHelpers.sol::89 => return keccak256(abi.encode(_permissions));
examples\framework\utils\InterfaceBasedRegistry.sol::19 => keccak256("UPGRADE_REGISTRY_PERMISSION");
examples\framework\utils\ens\ENSSubdomainRegistrar.sol::19 => keccak256("UPGRADE_REGISTRAR_PERMISSION");
examples\framework\utils\ens\ENSSubdomainRegistrar.sol::23 => keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");
examples\framework\utils\ens\ENSSubdomainRegistrar.sol::86 => bytes32 subnode = keccak256(abi.encodePacked(node, _label));
examples\plugins\counter-example\MultiplyHelper.sol::12 => bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
examples\plugins\counter-example\v1\CounterV1.sol::14 => bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
examples\plugins\counter-example\v1\CounterV1PluginSetup.sol::48 => bytes4(keccak256("initialize(address,address,uint256)")),
examples\plugins\counter-example\v1\CounterV1PluginSetup.sol::69 => keccak256("EXECUTE_PERMISSION")
examples\plugins\counter-example\v1\CounterV1PluginSetup.sol::114 => keccak256("EXECUTE_PERMISSION")
examples\plugins\counter-example\v2\CounterV2.sol::14 => bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
examples\plugins\counter-example\v2\CounterV2PluginSetup.sol::49 => bytes4(keccak256("initialize(address,address,uint256)")),
examples\plugins\counter-example\v2\CounterV2PluginSetup.sol::70 => keccak256("EXECUTE_PERMISSION")
examples\plugins\counter-example\v2\CounterV2PluginSetup.sol::116 => bytes4(keccak256("setNewVariable(uint256)")),
examples\plugins\counter-example\v2\CounterV2PluginSetup.sol::154 => keccak256("EXECUTE_PERMISSION")
examples\plugins\governance\admin\Admin.sol::24 => keccak256("EXECUTE_PROPOSAL_PERMISSION");
examples\plugins\governance\majority-voting\MajorityVotingBase.sol::184 => keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");
examples\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol::28 => keccak256("UPDATE_ADDRESSES_PERMISSION");
examples\plugins\governance\multisig\Multisig.sol::72 => keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");
examples\plugins\token\MerkleDistributor.sol::105 => bytes32 node = keccak256(abi.encodePacked(_index, _to, _amount));
examples\plugins\token\MerkleMinter.sol::24 => bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");
examples\plugins\token\MerkleMinter.sol::28 => keccak256("CHANGE_DISTRIBUTOR_PERMISSION");
examples\test\permission\PermissionManagerTest.sol::11 => bytes32 public constant TEST_PERMISSION_1_ID = keccak256("TEST_PERMISSION_1");
examples\test\permission\PermissionManagerTest.sol::12 => bytes32 public constant TEST_PERMISSION_2_ID = keccak256("TEST_PERMISSION_2");
examples\test\plugin\PluginMockData.sol::24 => keccak256("MOCK_PERMISSION")
examples\test\plugin\PluginMockData.sol::41 => abi.encodeWithSelector(bytes4(keccak256("initialize(address)")), _dao)
examples\test\plugin\PluginTest.sol::9 => bytes32 public constant DO_SOMETHING_PERMISSION_ID = keccak256("DO_SOMETHING_PERMISSION");
examples\test\plugin\SharedPluginTest.sol::15 => bytes32 public constant ID_GATED_ACTION_PERMISSION_ID = keccak256("ID_GATED_ACTION_PERMISSION");
examples\test\utils\InterfaceBasedRegistryMock.sol::8 => bytes32 public constant REGISTER_PERMISSION_ID = keccak256("REGISTER_PERMISSION");
examples\token\ERC20\governance\GovernanceERC20.sol::28 => bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");
```
#### Tools used
Manually