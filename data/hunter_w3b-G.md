# Gas Optimization

# Summary

| Number | Optimization Details                                                                                      | Context |
| :----: | :-------------------------------------------------------------------------------------------------------- | :-----: |
| [G-01] | internal functions only called once can be inlined to save gas                                            |    8    |
| [G-02] | Functions guaranteed to revert when called by normal users can be marked payable                          |    2    |
| [G-03] | EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAKE256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT |   28    |
| [G-04] | Setting the constructor to payable                                                                        |   21    |
| [G-05] | Not Using The Named Return Variables when A function Returns, Waste Deployment Gas                        |   41    |
| [G-06] | USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS                  |    4    |
| [G-07] | PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD                           |   23    |
| [G-08] | Duplicated require()/if() checks should be refactored to a modifier or function                           |    9    |
| [G-09] | Use constants instead of type(uintx).max                                                                  |    1    |
| [G-10] | Use nested if and, avoid multiple check combinations                                                      |   14    |
| [G-11] | Use assembly to write address storage values                                                              |    2    |
| [G-12] | The result of function calls should be cached rather than re-calling the function                         |   38    |
| [G-13] | Empty blocks should be removed or emit something                                                          |    2    |

## [G-1] **internal** functions only called once can be **inlined** to save gas

### Summary

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

### Details

If function called once in the contract will be inline to save gas

There are **8** instances of this issue.

### Github Permalinks

```solidity
File: /src/core/permission/PermissionManager.sol

212b    function _initializePermissionManager(address _initialOwner) internal {

327     function _auth(bytes32 _permissionId) internal view virtual {

354     function isPermissionRestrictedForAnyAddr(
355        bytes32 _permissionId
356:    ) internal view virtual returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol

```solidity
File:/src/core/plugin/proposal/Proposal.sol

33     function _createProposalId() internal returns (uint256 proposalId) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L33

```solidity
File: /src/core/plugin/proposal/ProposalUpgradeable.sol

33     function _createProposalId() internal returns (uint256 proposalId) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L33

```solidity
File: /src/framework/dao/DAOFactory.sol

141    function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {

156    function _setDAOPermissions(DAO _dao) internal {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol

```solidity
File:  /src/framework/plugin/repo/PluginRepoFactory.sol

65    function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L65

### Mitigation

instead of internal use inline.

## [G-2] Functions guaranteed to **revert** when called by normal users can be marked **payable**

### Summary

These are missed from **4naly3er**

https://gist.github.com/Picodes/16984274f6ad7b83b7a59f8b33cee6a6#gas-8-functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable

### Details

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

There are **2** instances of this issue.

### Github Permalinks

```solidity
File: /src/framework/utils/InterfaceBasedRegistry.sol

43    function __InterfaceBasedRegistry_init(
44        IDAO _managingDao,
45        bytes4 _targetInterfaceId
46:    ) internal virtual onlyInitializing {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L46

```solidity
File: /src/plugins/governance/majority-voting/MajorityVotingBase.sol

238     function __MajorityVotingBase_init(
239        IDAO _dao,
240        VotingSettings calldata _votingSettings
241:    ) internal onlyInitializing {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L241

### Mitigation

Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

## [G-3] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAKE256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

### Details

There are **28** instances of this issue.

### Github Permalinks

```solidity
File: /src/core/dao/DAO.sol

40    bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");


43    bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");


46    bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");


49    bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
50          keccak256("SET_TRUSTED_FORWARDER_PERMISSION");


53    bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
54        keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");


57    bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
58        keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol

```solidity
File: /src/core/permission/PermissionManager.sol

15    bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L15

```solidity
File: /src/core/plugin/PluginUUPSUpgradeable.sol

35    bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35

```solidity
File: /src/framework/dao/DAORegistry.sol

15    bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15

```solidity
File: /src/framework/plugin/repo/PluginRepo.sol

49    bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

52    bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

```solidity
File: /src/framework/plugin/repo/PluginRepoRegistry.sol

16    bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
17        keccak256("REGISTER_PLUGIN_REPO_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessor.sol

27    bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
28        keccak256("APPLY_INSTALLATION_PERMISSION");


31    bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");


34     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
35        keccak256("APPLY_UNINSTALLATION_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

```solidity
File: /src/framework/utils/ens/ENSSubdomainRegistrar.sol

18    bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
19        keccak256("UPGRADE_REGISTRAR_PERMISSION");


22    bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
23        keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

```solidity
File: /src/framework/utils/InterfaceBasedRegistry.sol

18    bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
19        keccak256("UPGRADE_REGISTRY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18

```solidity
File: /src/plugins/counter-example/v1/CounterV1.sol

14    bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14

```solidity
File: /src/plugins/counter-example/v2/CounterV2.sol

14    bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14

```solidity
File: /src/plugins/counter-example/MultiplyHelper.sol

12    bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12

```solidity
File: /src/plugins/governance/admin/Admin.sol

23    bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
24        keccak256("EXECUTE_PROPOSAL_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L23-L24

```solidity
File: /contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

27    bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
28        keccak256("UPDATE_ADDRESSES_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27

```solidity
File: /src/plugins/governance/majority-voting/MajorityVotingBase.sol

183    bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
184        keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183-L184

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

71    bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
72        keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71-L72

```solidity
File: /src/plugins/token/MerkleMinter.sol

24    bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");


27    bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
28        keccak256("CHANGE_DISTRIBUTOR_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol

```solidity
File: /src/token/ERC20/governance/GovernanceERC20.sol

28    bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28

## [G-4] Setting the constructor to payable

### Summary

Saves ~13 gas per instance

### Details

There are **21** instance of this issue

### Github Permalinks

```solidity
File: /src/framework/utils/ens/ENSSubdomainRegistrar.sol

45     constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L45-L47

```solidity
File: /framework/utils/TokenFactory.sol

68    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L68-L70

```solidity
File: /src/plugins/counter-example/v1/CounterV1PluginSetup.sol

23    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L23

```solidity
File: /src/plugins/counter-example/v2/CounterV2PluginSetup.sol

24    constructor(MultiplyHelper _helper) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L24-L27

```solidity
File: /src/plugins/governance/admin/AdminSetup.sol

27    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L27-L29

```solidity
File: /src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

20    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L20-L22

```solidity
File: /src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

61    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61-L74

```solidity
File: /src/plugins/governance/multisig/MultisigSetup.sol

19    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L19-L21

```solidity
File: /src/token/ERC20/governance/GovernanceERC20.sol

49    constructor(
50        IDAO _dao,
51        string memory _name,
52        string memory _symbol,
53        MintSettings memory _mintSettings
54    ) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L49-L56

```solidity
File: /src/token/ERC20/governance/GovernanceWrappedERC20.sol

38    constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L38

```solidity
File: /src/core/dao/DAO.sol

92    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L92-L94

```solidity
File: /src/core/plugin/Plugin.sol

17    constructor(IDAO _dao) DaoAuthorizable(_dao) {}
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/Plugin.sol#L17

```solidity
File: /src/core/plugin/PluginCloneable.sol

16     constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L16-L18

```solidity
File: /src/core/plugin/PluginUUPSUpgradeable.sol

25    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25-L27

```solidity
File: /src/core/plugin/dao-authorizable/DaoAuthorizable.sol

19    constructor(IDAO _dao) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19-L21

```solidity
File: /src/framework/dao/DAOFactory.sol

53    constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L53-L58

```solidity
File: /src/framework/dao/DAORegistry.sol

30    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L30-L32

```solidity
File: /src/framework/plugin/repo/PluginRepo.sol

112    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L112-L114

```solidity
File: /src/framework/plugin/repo/PluginRepoFactory.sol

22    constructor(PluginRepoRegistry _pluginRepoRegistry) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22-L26

```solidity
File: /src/framework/plugin/repo/PluginRepoRegistry.sol

34    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L34-L36

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessor.sol

277    constructor(PluginRepoRegistry _repoRegistry) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277-L279

### Mitigation

Setting the constructor to payable

## [G-5] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

### Summary

Do not use return variables when a function returns

### Details

There are **41** instances of this issue.

### Github Permalinks

```solidity
File: /src/core/dao/DAO.sol

176        returns (bytes[] memory execResults, uint256 failureMap)
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L176

```solidity
File: /src/core/dao/IEIP4824.sol

10    function daoURI() external view returns (string memory _daoURI);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IEIP4824.sol#L10

```solidity
File: /src/core/permission/IPermissionCondition.sol

20    ) external view returns (bool allowed);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/IPermissionCondition.sol#L20

```solidity
File: /src/core/plugin/proposal/Proposal.sol

33    function _createProposalId() internal returns (uint256 proposalId) {

52    ) internal virtual returns (uint256 proposalId) {

77    ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol

```solidity
File: /src/core/plugin/proposal/ProposalUpgradeable.sol

33    function _createProposalId() internal returns (uint256 proposalId) {

52    ) internal virtual returns (uint256 proposalId) {

77    ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L33

```solidity
File: /src/framework/dao/DAOFactory.sol

66    ) external returns (DAO createdDao) {

141    function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol

```solidity
File: /src/framework/plugin/repo/PluginRepoFactory.sol

51    ) external returns (PluginRepo pluginRepo) {

118    ) internal returns (PluginRepo pluginRepo) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

```solidity
File: /src/framework/plugin/setup/IPluginSetup.sol

38    ) external returns (address plugin, PreparedSetupData memory preparedSetupData);

50    ) external returns (bytes memory initData, PreparedSetupData memory preparedSetupData);

59    ) external returns (PermissionLib.MultiTargetPermission[] memory permissions);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/IPluginSetup.sol

```solidity
File: /src/framework/plugin/setup/PluginSetup.sol

26        returns (bytes memory initData, PreparedSetupData memory preparedSetupData)
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L26

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessor.sol

289    ) external returns (address plugin, IPluginSetup.PreparedSetupData memory preparedSetupData) {

401        returns (bytes memory initData, IPluginSetup.PreparedSetupData memory preparedSetupData)

555    ) external returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

```solidity
File: /src/plugins/counter-example/v1/CounterV1PluginSetup.sol

36        returns (address plugin, PreparedSetupData memory preparedSetupData)

103    ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

```solidity
File: /src/plugins/counter-example/v2/CounterV2PluginSetup.sol

37        returns (address plugin, PreparedSetupData memory preparedSetupData)

109        returns (bytes memory initData, PreparedSetupData memory preparedSetupData)

143    ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

```solidity
File: /src/plugins/governance/admin/AdminSetup.sol

35    ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

79    ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol

```solidity
File: /src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

91    ) external override returns (uint256 proposalId) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L91

```solidity
File: /src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

28    ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

90    ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

```solidity
File: /src/plugins/governance/majority-voting/token/TokenVoting.sol

79    ) external override returns (uint256 proposalId) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L79

```solidity
File: /src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

80    ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

201    ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

```solidity
File: /src/plugins/governance/majority-voting/MajorityVotingBase.sol

398        returns (
399            bool open,
400            bool executed,
401            ProposalParameters memory parameters,
402            Tally memory tally,
403            IDAO.Action[] memory actions,
404            uint256 allowFailureMap
405        )


442    ) external virtual returns (uint256 proposalId);

567    ) internal view virtual returns (uint64 startDate, uint64 endDate) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

310        returns (
311            bool executed,
312            uint16 approvals,
313            ProposalParameters memory parameters,
314            IDAO.Action[] memory actions,
315            uint256 allowFailureMap
316        )
317    {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L310

```solidity
File: /src/plugins/governance/multisig/MultisigSetup.sol

27    ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {

78    ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

```solidity
File: /src/plugins/token/IMerkleMinter.sol

47    ) external returns (IMerkleDistributor distributor);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/IMerkleMinter.sol#L47

```solidity
File: /src/plugins/utils/Ratio.sol

17   function _applyRatioCeiled(uint256 _value, uint256 _ratio) pure returns (uint256 result) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Ratio.sol#L17

## [G-6] USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS

### Details

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.

There is **4** instance of this issue.

### Github Permalinks

```solidity
File: /src/plugins/token/MerkleDistributor.sol

83    function unclaimedBalance(
84        uint256 _index,
85        address _to,
86        uint256 _amount,
87:        bytes32[] memory _proof
88    ) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L83-L91

```solidity
File: /src/core/dao/IDAO.sol

46    function execute(
47        bytes32 _callId,
48:        Action[] memory _actions,
49        uint256 _allowFailureMap
50    ) external returns (bytes[] memory, uint256);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol#L46

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

79  function hashHelpers(address[] memory _helpers) pure returns (bytes32) {
80     return keccak256(abi.encode(_helpers));
81  }


86  function hashPermissions(
87:      PermissionLib.MultiTargetPermission[] memory _permissions
88   ) pure returns (bytes32) {
89      return keccak256(abi.encode(_permissions));
90  }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessorHelpers.sol

## [G-7] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

### Summary

Contracts are allowed to override their parents functions and change the visibility from external to public and can save gas by doing so, public functions not called in the contract itself should be declared externals.

### Details

if a function is override public and not called in the contract should be declared external instead

There are **23** instance of this issue.

### Github Permalinks

```solidity
File: /src/plugins/governance/majority-voting/token/TokenVoting.sol

61:     function getVotingToken() public view returns (IVotesUpgradeable) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L61

```solidity
File: /src/plugins/governance/majority-voting/MajorityVotingBase.sol

291    function getVoteOption(
292        uint256 _proposalId,
293        address _voter
294    ) public view virtual returns (VoteOption) {
295        return proposals[_proposalId].voters[_voter];
296    }


352    function supportThreshold() public view virtual returns (uint32) {

357    function minParticipation() public view virtual returns (uint32) {

363    function minDuration() public view virtual returns (uint64) {

369    function minProposerVotingPower() public view virtual returns (uint256) {

375    function votingMode() public view virtual returns (VotingMode) {

392    function getProposal(
393        uint256 _proposalId
394    )
395        public
396        view
397        virtual
398        returns (
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

305    function getProposal(
306        uint256 _proposalId
307    )
308        public
309        view
310        returns (


333     function execute(uint256 _proposalId) public {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol

```solidity
File: /src/plugins/token/MerkleDistributor.sol

83    function unclaimedBalance(
84        uint256 _index,
85        address _to,
86        uint256 _amount,
87        bytes32[] memory _proof
88    ) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L83-L91

```solidity
File: /src/plugins/utils/Addresslist.sol

30   function isListedAtBlock(
31        address _account,
32        uint256 _blockNumber
33    ) public view virtual returns (bool) {


47    function addresslistLengthAtBlock(uint256 _blockNumber) public view virtual returns (uint256) {

53    function addresslistLength() public view virtual returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol

```solidity
File: /src/core/plugin/Plugin.sol

20    function pluginType() public pure override returns (PluginType) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/Plugin.sol#L20

```solidity
File: /src/core/plugin/PluginCloneable.sol

27    function pluginType() public pure override returns (PluginType) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L27

```solidity
File: /src/core/plugin/PluginUUPSUpgradeable.sol

30    function pluginType() public pure override returns (PluginType) {

55    function implementation() public view returns (address) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

```solidity
File: /src/core/plugin/dao-authorizable/DaoAuthorizable.sol

25    function dao() public view returns (IDAO) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L25

```solidity
File: /src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

26     function dao() public view returns (IDAO) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L26

```solidity
File: /src/framework/plugin/repo/PluginRepo.sol

209    function getLatestVersion(uint8 _release) public view returns (Version memory) {

217    function getLatestVersion(address _pluginSetup) public view returns (Version memory) {

244    function buildCount(uint8 _release) public view returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

## [G-8] Duplicated require()/if() checks should be refactored to a modifier or function

### Details

There are **9** instances of this issue

### Github Permalinks

```solidity
File: /src/plugins/counter-example/v1/CounterV1PluginSetup.sol

43        if (_multiplyHelper == address(0)) {
80        if (_multiplyHelper == address(0)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

```solidity
File: /src/plugins/counter-example/v2/CounterV2PluginSetup.sol

44        if (_multiplyHelper == address(0)) {
81        if (_multiplyHelper == address(0)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

368        if (!_isProposalOpen(proposal_)) {
393        if (!_isProposalOpen(proposal_)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol

```solidity
File: /src/core/permission/PermissionManager.sol

153            if (item.operation == PermissionLib.Operation.Grant) {
173            if (item.operation == PermissionLib.Operation.Grant) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol

```solidity
File: /src/framework/plugin/repo/PluginRepo.sol

147        if (_release > latestRelease) {
195        if (_release > latestRelease) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessor.sol

321        if (pluginState.currentAppliedSetupId != bytes32(0)) {
366        if (pluginState.currentAppliedSetupId != bytes32(0)) {


326        if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {
484        if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {
590        if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {


378        if (_params.permissions.length > 0) {
535        if (_params.permissions.length > 0) {
635        if (_params.permissions.length > 0) {


426        if (pluginState.currentAppliedSetupId != appliedSetupId) {
565        if (pluginState.currentAppliedSetupId != appliedSetupId) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

## [G-9] Use constants instead of type(uintx).max

### Summary

type(uintx).max it uses more gas in the distribution process and also for each transaction than constant usage.

### Details

there are **1** instances of this issue

### Github Permalinks

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

159         if (newAddresslistLength > type(uint16).max) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L159

## [G-10] Use nested if and, avoid multiple check combinations

### Summary

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

### Details

there are **14** instances of this issue

### Github Permalinks

```solidity
File: /src/framework/plugin/repo/PluginRepo.sol

157        if (version.tag.release != 0 && version.tag.release != _release) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L157

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessor.sol

700        if (
701            msg.sender != _dao &&
702            !DAO(payable(_dao)).hasPermission(address(this), msg.sender, _permissionId, bytes(""))
703        )
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L700-L703

```solidity
File: /src/framework/utils/RegistryUtils.sol

20        if (char > 96 && char < 123) {


25        if (char > 47 && char < 58) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol

```solidity
File: /src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

97        if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {


185        if (_tryEarlyExecution && _canExecute(_proposalId)) {


214      if (
215            proposal_.voters[_account] != VoteOption.None &&
216            proposal_.parameters.votingMode != VotingMode.VoteReplacement
217        )
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

```solidity
File: /src/plugins/governance/majority-voting/token/TokenVoting.sol

182        if (_tryEarlyExecution && _canExecute(_proposalId)) {

211        if (
212            proposal_.voters[_account] != VoteOption.None &&
213            proposal_.parameters.votingMode != VotingMode.VoteReplacement
214        )
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

216        if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {

283        if (_tryExecution && _canExecute(_proposalId)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol

```solidity
File: /src/token/ERC20/governance/GovernanceERC20.sol

116        if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L116

```solidity
File: /src/token/ERC20/governance/GovernanceWrappedERC20.sol

106        if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L106

### Recomendation Code

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

-283:      if (_tryExecution && _canExecute(_proposalId)) {

+       if(_tryExecution) {
+          if(_canExecute(_proposalId)){
            }

           ...}
```

## [G-11] Use assembly to write address storage values

### Details

there are **2** instances of this issue

### Github Permalinks

```solidity
File: /src/core/dao/DAO.sol

283    function _setTrustedForwarder(address _trustedForwarder) internal {
284:        trustedForwarder = _trustedForwarder;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L283

```solidity
File: /src/framework/utils/ens/ENSSubdomainRegistrar.sol

100    function setDefaultResolver(
101        address _resolver
102    ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
103        if (_resolver == address(0)) {
104            revert InvalidResolver({node: node, resolver: _resolver});
105        }
106
107:        resolver = _resolver;
108    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L100-L108

### Recommendation Code

```solidity
File: /src/core/dao/DAO.sol

    function _setTrustedForwarder(address _trustedForwarder) internal {
-          trustedForwarder = _trustedForwarder;

+          assembly {
+             sstore(trustedForwarder.slot,_trustedForwarder)
+          }
```

## [G‑12] The result of function calls should be cached rather than re-calling the function

### Details

The instances below point to the second+ call of the function within a single function

there are **38** instances of this issue

### Github Permalinks

```solidity
File: /src/core/permission/PermissionManager.sol

328        if (!isGranted(address(this), msg.sender, _permissionId, msg.data)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L328

```solidity
File: /src/core/utils/auth.sol

26    if (!_dao.hasPermission(_where, _who, _permissionId, _data))
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/auth.sol#L26

```solidity
File: /src/framework/dao/DAORegistry.sol

60             if (!isSubdomainValid(subdomain)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L60

```solidity
File: /src/framework/plugin/repo/PluginRepo.sol

134        if (!_pluginSetup.supportsInterface(type(IPluginSetup).interfaceId)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L134

```solidity
File: /src/framework/plugin/repo/PluginRepoRegistry.sol

55         if (!(bytes(subdomain).length > 0)) {

59         if (!isSubdomainValid(subdomain)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

```solidity
File: /src/framework/plugin/setup/PluginSetupProcessor.sol

293            if (!repoRegistry.entries(address(pluginSetupRepo))) {

461            if (!_params.setupPayload.plugin.supportsInterface(type(IPlugin).interfaceId)) {

464            if (IPlugin(_params.setupPayload.plugin).pluginType() != IPlugin.PluginType.UUPS) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L293

```solidity
File: /src/framework/utils/InterfaceBasedRegistry.sol

67        if (!_registrant.supportsInterface(targetInterfaceId)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L67

```solidity
File: /src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

185        if (_tryEarlyExecution && _canExecute(_proposalId)) {

199        if (!_isProposalOpen(proposal_)) {

209        if (!isListedAtBlock(_account, proposal_.parameters.snapshotBlock)) {

```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

```solidity
File: /src/plugins/governance/majority-voting/token/TokenVoting.sol

91        if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {

196        if (!_isProposalOpen(proposal_)) {

206        if (votingToken.getPastVotes(_account, proposal_.parameters.snapshotBlock) == 0) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

```solidity
File: /src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

99            if (!token.isContract()) {

103            if (!_isERC20(token)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

```solidity
File: /src/plugins/governance/majority-voting/MajorityVotingBase.sol

272        if (!_canVote(_proposalId, account, _voteOption)) {

284        if (!_canExecute(_proposalId)) {

491        if (_isProposalOpen(proposal_)) {

496            if (!isSupportThresholdReachedEarly(_proposalId)) {

501            if (!isSupportThresholdReached(_proposalId)) {

505        if (!isMinParticipationReached(_proposalId)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

```solidity
File: /src/plugins/governance/multisig/Multisig.sol

216        if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {

267        if (!_canApprove(_proposalId, approver)) {

283        if (_tryExecution && _canExecute(_proposalId)) {

334        if (!_canExecute(_proposalId)) {

368        if (!_isProposalOpen(proposal_)) {

373        if (!isListedAtBlock(_account, proposal_.parameters.snapshotBlock)) {

393        if (!_isProposalOpen(proposal_)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol

```solidity
File: /src/plugins/token/MerkleDistributor.sol

72        if (isClaimed(_index)) revert TokenAlreadyClaimed({index: _index});

73        if (!_verifyBalanceOnTree(_index, _to, _amount, _proof))

89        if (isClaimed(_index)) return 0;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol

```solidity
File: /src/plugins/utils/Addresslist.sol

61            if (isListed(_newAddresses[i])) {

79            if (!isListed(_exitingAddresses[i])) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol

```solidity
File: /src/token/ERC20/governance/GovernanceERC20.sol

116        if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L116

```solidity
File: /src/token/ERC20/governance/GovernanceWrappedERC20.sol

106        if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L106

## [G‑13] Empty blocks should be removed or emit something

### Details

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be
nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when
the code is later modified (if( x ) {}else if(y){ . . . }else{ . . . } => if ( !x) {if(y) { . . . }else{ . . .}}) .
Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

there are **2** instances of this issue

### Github Permalinks

```solidity
File: /src/plugins/counter-example/v1/CounterV1.sol

44    function execute() public {
45        // In order to do this, Count needs permission on the dao (EXEC_ROLE)
46        //dao.execute(...)
47    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L44

```solidity
File: /src/plugins/counter-example/v2/CounterV2.sol

63    function execute() public {
64        // In order to do this, Count needs permission on the dao (EXEC_ROLE)
65        //dao.execute(...)
66    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L63
