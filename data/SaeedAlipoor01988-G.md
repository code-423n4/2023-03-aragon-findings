###############  Use immutable for OpenZeppelin AccessControl's Roles Declarations ###############  

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
examples\token\ERC20\governance\GovernanceERC20.sol::28 => bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");
```

###############  Getter functions for state variables ###############  

#### Impact

save gas cost by change string private _daoURI to string public _daoURI and remove function daoURI() external view returns (string memory).

If you have public state variables in your contract, the compiler will create getter functions for these automatically. Therefore, if you have already defined public state variables, you don't have to write getter functions explicitly for those variables. It isn't recommended to write getter functions for public state variables. 
https://www.oreilly.com/library/view/mastering-blockchain-programming/9781839218262/048537c8-6e8f-4cef-860a-b923a96946e3.xhtml

#### Findings:
```
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L70
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L320
```