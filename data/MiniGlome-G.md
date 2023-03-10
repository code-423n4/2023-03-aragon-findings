## Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [GAS-01] | Don't Initialize Variables with Default Value | 2 | 
| [GAS-02] | Setting the `constructor` to `payable` | 21 | 
| [GAS-03] | Usage of uint/int smaller than 32 bytes | 43 | 

### [GAS-01] Don't Initialize Variables with Default Value
Uninitialized variables are assigned with the types default value.<br>Explicitly initializing a variable with it's default value costs unnecessary gas.

*Instances (2)*:
```solidity
File: Aragon\core\permission\PermissionLib.sol
10:    address public constant NO_CONDITION = address(0);

```

```solidity
File: Aragon\core\permission\PermissionManager.sol
21:    address internal constant UNSET_FLAG = address(0);

```

### [GAS-02] Setting the `constructor` to `payable`
Saves ~13 gas per instance

*Instances (21)*:
```solidity
File: Aragon\core\dao\DAO.sol
92:    constructor() {

```

```solidity
File: Aragon\core\plugin\Plugin.sol
17:    constructor(IDAO _dao) DaoAuthorizable(_dao) {}

```

```solidity
File: Aragon\core\plugin\PluginCloneable.sol
16:    constructor() {

```

```solidity
File: Aragon\core\plugin\PluginUUPSUpgradeable.sol
25:    constructor() {

```

```solidity
File: Aragon\core\plugin\dao-authorizable\DaoAuthorizable.sol
19:    constructor(IDAO _dao) {

```

```solidity
File: Aragon\framework\dao\DAOFactory.sol
53:    constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {

```

```solidity
File: Aragon\framework\dao\DAORegistry.sol
30:    constructor() {

```

```solidity
File: Aragon\framework\plugin\repo\PluginRepo.sol
112:    constructor() {

```

```solidity
File: Aragon\framework\plugin\repo\PluginRepoFactory.sol
22:    constructor(PluginRepoRegistry _pluginRepoRegistry) {

```

```solidity
File: Aragon\framework\plugin\repo\PluginRepoRegistry.sol
34:    constructor() {

```

```solidity
File: Aragon\framework\plugin\setup\PluginSetupProcessor.sol
277:    constructor(PluginRepoRegistry _repoRegistry) {

```

```solidity
File: Aragon\framework\utils\TokenFactory.sol
68:    constructor() {

```

```solidity
File: Aragon\framework\utils\ens\ENSSubdomainRegistrar.sol
45:    constructor() {

```

```solidity
File: Aragon\plugins\counter-example\v1\CounterV1PluginSetup.sol
23:    constructor() {

```

```solidity
File: Aragon\plugins\counter-example\v2\CounterV2PluginSetup.sol
24:    constructor(MultiplyHelper _helper) {

```

```solidity
File: Aragon\plugins\governance\admin\AdminSetup.sol
27:    constructor() {

```

```solidity
File: Aragon\plugins\governance\majority-voting\addresslist\AddresslistVotingSetup.sol
20:    constructor() {

```

```solidity
File: Aragon\plugins\governance\majority-voting\token\TokenVotingSetup.sol
61:    constructor() {

```

```solidity
File: Aragon\plugins\governance\multisig\MultisigSetup.sol
19:    constructor() {

```

```solidity
File: Aragon\token\ERC20\governance\GovernanceERC20.sol
49:    constructor(
        IDAO _dao,
        string memory _name,
        string memory _symbol,
        MintSettings memory _mintSettings
    ) {

```

```solidity
File: Aragon\token\ERC20\governance\GovernanceWrappedERC20.sol
38:    constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

```

### [GAS-03] Usage of uint/int smaller than 32 bytes
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html<br>Use a larger size then downcast where needed.

*Instances (43)*:
```solidity
File: Aragon\core\dao\DAO.sol
192:                if (!hasBit(_allowFailureMap, uint8(i))) {

198:                failureMap = flipBit(failureMap, uint8(i));

```

```solidity
File: Aragon\core\utils\BitMap.sol
8:function hasBit(uint256 bitmap, uint8 index) pure returns (bool) {

16:function flipBit(uint256 bitmap, uint8 index) pure returns (uint256) {

```

```solidity
File: Aragon\framework\plugin\repo\IPluginRepo.sol
12:    function updateReleaseMetadata(uint8 _release, bytes calldata _releaseMetadata) external;

20:        uint8 _release,

```

```solidity
File: Aragon\framework\plugin\repo\PluginRepo.sol
34:        uint8 release;

35:        uint16 build;

55:    mapping(uint8 => uint16) internal buildsPerRelease;

55:    mapping(uint8 => uint16) internal buildsPerRelease;

65:    uint8 public latestRelease;

80:    error InvalidReleaseIncrement(uint8 latestRelease, uint8 newRelease);

80:    error InvalidReleaseIncrement(uint8 latestRelease, uint8 newRelease);

86:    error PluginSetupAlreadyInPreviousRelease(uint8 release, uint16 build, address pluginSetup);

86:    error PluginSetupAlreadyInPreviousRelease(uint8 release, uint16 build, address pluginSetup);

100:        uint8 release,

101:        uint16 build,

109:    event ReleaseMetadataUpdated(uint8 release, bytes releaseMetadata);

129:        uint8 _release,

165:        uint16 build = ++buildsPerRelease[_release];

188:        uint8 _release,

209:    function getLatestVersion(uint8 _release) public view returns (Version memory) {

210:        uint16 latestBuild = uint16(buildsPerRelease[_release]);

210:        uint16 latestBuild = uint16(buildsPerRelease[_release]);

244:    function buildCount(uint8 _release) public view returns (uint256) {

```

```solidity
File: Aragon\framework\plugin\setup\IPluginSetup.sol
48:        uint16 _currentBuild,

```

```solidity
File: Aragon\framework\plugin\setup\PluginSetup.sol
20:        uint16 _currentBuild,

```

```solidity
File: Aragon\framework\utils\RegistryUtils.sol
17:        uint8 char = uint8(nameBytes[i]);

17:        uint8 char = uint8(nameBytes[i]);

```

```solidity
File: Aragon\plugins\counter-example\v2\CounterV2PluginSetup.sol
103:        uint16 _currentBuild,

```

```solidity
File: Aragon\plugins\governance\multisig\Multisig.sol
36:        uint16 approvals;

49:        uint16 minApprovals;

60:        uint16 minApprovals;

99:    error MinApprovalsOutOfBounds(uint16 limit, uint16 actual);

99:    error MinApprovalsOutOfBounds(uint16 limit, uint16 actual);

104:    error AddresslistLengthOutOfBounds(uint16 limit, uint256 actual);

119:    event MultisigSettingsUpdated(bool onlyListed, uint16 indexed minApprovals);

175:        uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

175:        uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

312:            uint16 approvals,

414:        uint16 addresslistLength_ = uint16(addresslistLength());

414:        uint16 addresslistLength_ = uint16(addresslistLength());

```

```solidity
File: Aragon\token\ERC20\governance\GovernanceWrappedERC20.sol
75:        returns (uint8)

```
