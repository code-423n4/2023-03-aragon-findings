## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

```solidity
// modifier coming after functions
32:    modifier auth(bytes32 _permissionId) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol

```solidity
// modifier coming after functions
31:    modifier auth(bytes32 _permissionId) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol

```solidity
// events coming in the middle of functions, should come before
38:    event MetadataSet(bytes metadata);
59:    event Executed(
71:    event StandardCallbackRegistered(
88:    event Deposited(
99:    event NativeTokenDeposited(address sender, uint256 amount);
111:    event TrustedForwarderSet(address forwarder);
119:    event SignatureValidatorSet(address signatureValidator);
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/MajorityVotingMock.sol

```solidity
// external function coming after external one
24:    function totalVotingPower(uint256 /* _blockNumber */) public pure override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol

```solidity
// external functions coming before a public one
41:    function initialize(
53:    function changeDistributorBase(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol

```solidity
// external functions coming before public ones
46:    function initialize(
66:    function claim(

// internal functions coming before a public one
99:    function _verifyBalanceOnTree(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol

```solidity
// these external functions are coming in the middle of public ones,  should come after
125:    function initialize(
153:    function addAddresses(
172:    function removeAddresses(
192:    function updateMultisigSettings(
205:    function createProposal(
289:    function canApprove(uint256 _proposalId, address _account) external view returns (bool) {
294:    function canExecute(uint256 _proposalId) external view returns (bool) {
342:    function isMember(address _account) external view returns (bool) {
```


https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

```solidity
// external fn coming before public ones
36:    function initialize(
71:    function createProposal(
137:    function isMember(address _account) external view returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

```solidity
// external fn coming before public ones
36:    function initialize(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

```solidity
// public functions coming after external ones
48:    function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
78:    function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol

```solidity
// place this view function after all the non-view external ones
48:    function isMember(address _account) external view returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

```solidity
// place this view function after all the non-view external ones
101:    function prepareUpdate(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

```solidity
// public functions coming after external one, also move this view fn after all the public ones
58:    function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {

// public function coming after external one
63:    function execute() public {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

```solidity
// this external function is coming before public functions
26:    function initialize(

// place this public view function after all the public non view ones 
39:    function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

```solidity
// internal functions should come last
74:    function _authorizeUpgrade(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

```solidity
// place this private view function after all the non view private functions
699:    function _canApply(address _dao, bytes32 _permissionId) private view {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol

```solidity
//  public function coming after internal one
43:    function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

```solidity
  external, public and internal functions are mixed. order them accordinly.
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

```solidity
// internal function coming before a public ones
39:    function __PluginUUPSUpgradeable_init(IDAO _dao) internal virtual onlyInitializing {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol

```solidity
// internal function coming before a public ones
22:    function __PluginCloneable_init(IDAO _dao) internal virtual onlyInitializing {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol

```solidity
// internal function coming before a public one
20:    function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol

```solidity
// public functions coming after external ones
198:    function isGranted(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol

```solidity
// internal function coming before external ones
95:    function __PermissionManager_init(address _initialOwner) internal onlyInitializing {

// public functions should come before external ones
198:    function isGranted(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IDAO.sol

```solidity
// view and pure functions should come last
25:    function hasPermission(
107:    function getTrustedForwarder() external view returns (address);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol

```solidity
// receive and fallback functions should come before all the other functions
263:    receive() external payable {
270:    fallback(bytes calldata _input) external returns (bytes memory) {

// internal and private functions coming in the middle of external ones
122:    function isPermissionRestrictedForAnyAddr(
136:    function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}
277:    function _setMetadata(bytes calldata _metadata) internal {
283:    function _setTrustedForwarder(address _trustedForwarder) internal {
290:    function _registerTokenInterfaces() private {
```

---

### natSpec missing [3]

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/IGovernanceWrappedERC20.sol

```solidity
9:    interface IGovernanceWrappedERC20 {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/utils/RegistryUtilsTest.sol

```solidity
7:    contract RegistryUtils {

8:    function isSubdomainValid(string calldata subdomain) external pure returns (bool) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/utils/RatioTest.sol

```solidity
7:    contract RatioTest {

8:    function getRatioBase() public pure returns (uint256) {

12:    function applyRatioCeiled(uint256 _value, uint256 _ratio) public pure returns (uint256) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/utils/InterfaceBasedRegistryMock.sol

```solidity
7:    contract InterfaceBasedRegistryMock is InterfaceBasedRegistry {

12:    function initialize(IDAO _dao) external initializer {

16:    function register(address registrant) external auth(REGISTER_PERMISSION_ID) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/token/TestERC721.sol

```solidity
7:    contract TestERC721 is ERC721 {

8:    constructor(string memory name_, string memory symbol_) ERC721(name_, symbol_) {}

10:    function mint(address account, uint256 tokenId) public {

14:    function burn(address /* account */, uint256 tokenId) public {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/token/TestERC20.sol

```solidity
7:    contract TestERC20 is ERC20 {

8:    constructor(

18:    function setBalance(address to, uint256 amount) public {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/token/TestERC1155.sol

```solidity
7:    contract TestERC1155 is ERC1155 {

8:    constructor(string memory URI_) ERC1155(URI_) {}

10:    function mint(address account, uint256 tokenId, uint256 amount) public {

14:    function burn(address account, uint256 tokenId, uint256 amount) public {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/token/GovernanceERC20Mock.sol

```solidity
8:    contract GovernanceERC20Mock is GovernanceERC20 {

9:    constructor(

25:    function setBalance(address to, uint256 amount) public {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/SharedPluginTest.sol

```solidity
23:    modifier sharedAuth(uint256 _id, bytes32 _permissionId) {

40:    function initialize(IDAO _dao) external initializer {

65:    constructor(uint256 _id) {

71:    function isGranted(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/PluginTest.sol

```solidity
8:    contract TestPlugin is PluginUUPSUpgradeable {

11:    function initialize(IDAO _dao) external initializer {

15:    function addPermissioned(

22:    function subPermissioned(

29:    function msgSender() external view returns (address) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/PluginMockData.sol

```solidity
10:    function mockPermissions(

29:   function mockHelpers(uint160 amount) pure returns (address[] memory helpers) {

37:   function mockPluginProxy(address _pluginBase, address _dao) returns (address) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/MajorityVotingMock.sol

```solidity
7:    contract MajorityVotingMock is MajorityVotingBase {

8:    function initializeMock(IDAO _dao, VotingSettings calldata _votingSettings) public initializer {

12:    function createProposal(

24:    function totalVotingPower(uint256 /* _blockNumber */) public pure override returns (uint256) {

28:    function _vote(

35:    function _canVote(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/AdminCloneFactory.sol

```solidity
9:    contract AdminCloneFactory {

14:    constructor() {

18:    function deployClone() external returns (address clone) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/AddresslistMock.sol

```solidity
7:    contract AddresslistMock is Addresslist {

8:    function addAddresses(address[] calldata _newAddresses) external {

12:    function removeAddresses(address[] calldata _exitingAddresses) external {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol

```solidity
12:   contract PluginUUPSUpgradeableSetupV1Mock is PluginSetup {

15:    constructor() {

44:   contract PluginUUPSUpgradeableSetupV1MockBad is PluginUUPSUpgradeableSetupV1Mock {

45:    function prepareInstallation(

56:   contract PluginUUPSUpgradeableSetupV2Mock is PluginUUPSUpgradeableSetupV1Mock {

57:    constructor() {

71:    function prepareUpdate(

94:   contract PluginUUPSUpgradeableSetupV3Mock is PluginUUPSUpgradeableSetupV2Mock {

95:    constructor() {

109:    function prepareUpdate(

161:    function prepareUpdate(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableMock.sol

```solidity
8:    contract PluginUUPSUpgradeableV1Mock is PluginUUPSUpgradeable {

11:    function initialize(IDAO _dao) external initializer {

17:   contract PluginUUPSUpgradeableV2Mock is PluginUUPSUpgradeable {

21:    function initialize(IDAO _dao) external reinitializer(2) {

27:    function initializeV1toV2() external reinitializer(2) {

32:   contract PluginUUPSUpgradeableV3Mock is PluginUUPSUpgradeable {

37:    function initialize(IDAO _dao) external reinitializer(3) {

44:    function initializeV1toV3() external reinitializer(3) {

49:    function initializeV2toV3() external reinitializer(3) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/Clonable/PluginCloneableSetupMock.sol

```solidity
12:   contract PluginCloneableSetupV1Mock is PluginSetup {

15:    constructor() {

44:   contract PluginCloneableSetupV1MockBad is PluginCloneableSetupV1Mock {

45:    constructor() {

50:   contract PluginCloneableSetupV2Mock is PluginCloneableSetupV1Mock {

51:    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/Clonable/PluginCloneableMock.sol

```solidity
8:  contract PluginCloneableV1Mock is PluginCloneable {

11:    function initialize(IDAO _dao) external initializer {

18:   contract PluginCloneableV1MockBad {

21:    function initialize(IDAO _dao) external {

27:   contract PluginCloneableV2Mock is PluginCloneable {

31:    function initialize(IDAO _dao) external initializer {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/permission/PermissionManagerTest.sol

```solidity
9:  contract PermissionManagerTest is PermissionManager {

14:    function init(address _who) public initializer {

18:    function getAuthPermission(

26:    function getPermissionHash(

34:    function getAnyAddr() public pure returns (address) {

38:    function hasPermission(

47:    function isPermissionRestrictedForAnyAddr(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/permission/PermissionConditionMock.sol

```solidity
7:    contract PermissionConditionMock is IPermissionCondition {

10:    function isGranted(

19:    function setWillPerform(bool _result) external {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/permission/ParameterScopingPermissionConditionTest.sol

```solidity
8:   contract TestParameterScopingPermissionCondition is IPermissionCondition {

11:    function getSelector(bytes memory _data) public pure returns (bytes4 sig) {

17:    function isGranted(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/dao/ERC1271Mock.sol

```solidity
5:   contract ERC1271Mock {

6:    function isValidSignature(bytes32, bytes memory) public pure returns (bytes4) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/dao/DAOMock.sol

```solidity
10:   contract DAOMock is IDAO, PermissionManager {

14:    constructor(address initialOwner) initializer {

18:    function hasPermission(

27:    function getTrustedForwarder() public pure override returns (address) {

31:    function setTrustedForwarder(address /* _trustedForwarder */) external override {}

33:    function setMetadata(bytes calldata /* _metadata */) external override {}

35:    function execute(

44:    function deposit(

50:    function setSignatureValidator(address /* _signatureValidator */) external override {}

52:    function isValidSignature(

59:    function registerStandardCallback(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/dao/ActionExecute.sol

```solidity
9:    function setTest(uint newNum) public returns (uint) {

14:    function fail() public pure {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

```solidity
24:    constructor(MultiplyHelper _helper) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

```solidity
23:    constructor() {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol

```solidity
56:    struct TokenConfig {

62:    struct MintConfig {
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/AdminCloneFactory.sol

```solidity
//  private and internal `variables` should preppend with `underline`
12:    address private immutable implementation;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/plugin/UUPSUpgradeable/PluginUUPSUpgradeableSetupMock.sol

```solidity
//  private and internal `variables` should preppend with `underline`
13:    address internal pluginBase;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/test/permission/PermissionManagerTest.sol

```solidity
// private and internal `functions` should preppend with `underline`
47:    function isPermissionRestrictedForAnyAddr(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol

```solidity
//  private and internal `variables` should preppend with `underline`
29:    mapping(uint256 => uint256) private claimedBitMap;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol

```solidity
//  private and internal `variables` should preppend with `underline`
16:    Multisig private immutable multisigBase;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol

```solidity
// private and internal `variables` should preppend with `underline`
75:    mapping(uint256 => Proposal) internal proposals;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

```solidity
// private and internal `variables` should preppend with `underline`
187:    mapping(uint256 => Proposal) internal proposals;
190:    VotingSettings private votingSettings;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

```solidity
// private and internal `variables` should preppend with `underline`
30:    TokenVoting private immutable tokenVotingBase;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

```solidity
// private and internal `variables` should preppend with `underline`
26:    IVotesUpgradeable private votingToken;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

```solidity
// private and internal `variables` should preppend with `underline`
30:    TokenVoting private immutable tokenVotingBase;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

```solidity
// private and internal `variables` should preppend with `underline`
26:    IVotesUpgradeable private votingToken;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

```solidity
//  private and internal `variables` should preppend with `underline`
17:    AddresslistVoting private immutable addresslistVotingBase;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol

```solidity
//  private and internal `functions` should preppend with `underline`
144:    function setupBases() private {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

```solidity
// private and internal `functions` should preppend with `underline`
719:    function emitPrepareUpdateEvent(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol

```solidity
// private and internal `functions` should preppend with `underline`
33:    function createERC1967Proxy(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

```solidity
// private and internal `variables` should preppend with `underline`
251:    function tagHash(Tag memory _tag) internal pure returns (bytes32) {
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

```solidity
// private and internal `functions` should preppend with `underline`
55:    mapping(uint8 => uint16) internal buildsPerRelease;
58:    mapping(bytes32 => Version) internal versions;
61:    mapping(address => bytes32) internal latestTagHashForPluginSetup;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol

```solidity
//  private and internal `variables` should preppend with `underline`
11:    mapping(bytes4 => bytes4) internal callbackMagicNumbers;
```


https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol

```solidity
// private and internal `variables` should preppend with `underline`
17:    CountersUpgradeable.Counter private proposalCounter;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol

```solidity
// private and internal `variables` should preppend with `underline`
17:    Counters.Counter private proposalCounter;
```


https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol

```solidity
// private and internal `variables` should preppend with `underline`
27:    mapping(bytes32 => address) internal permissionsHashed;

// private and internal `functions` should preppend with `underline`
342:    function permissionHash(
354:    function isPermissionRestrictedForAnyAddr(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol

```solidity
// private and internal `functions` should preppend with `underline`
342:    function permissionHash(
354:    function isPermissionRestrictedForAnyAddr(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol

```solidity
// private and internal `variables` should preppend with `underline`
27:    mapping(bytes32 => address) internal permissionsHashed;

// private and internal `functions` should preppend with `underline`
342:    function permissionHash(
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol

```solidity
// private and internal `variables` should preppend with `underline`
67:    address private trustedForwarder;

// private and internal `functions` should preppend with `underline`
122:    function isPermissionRestrictedForAnyAddr(
```


