# Gas Optimizations list

| Number | Details                                                                         | Instances |
| ------ | ------------------------------------------------------------------------------- | --------- |
| 1      | ```x += y``` COSTS MORE GAS THAN ```x = x + y``` FOR STATE VARIABLES            | 1         |
| 2      | CAN DECLARE VARIABLE OUTSIDE LOOP TO SAVE GAS                                   | 6         |
| 3      | ++i/i++ CAN BE UNCHECKED                                                        | 1         |
| 4      | PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD | 46        |
| 5      | REMOVE UNUSED FUNCTIONS                                                         | 1         |
| 6      | USE ```bytes32``` INSTEAD OF ```string``` WHEREVER POSSIBLE                     | 11        |
| 7      | OPTIMIZE NAMES TO SAVE GAS                                                      | 62        |
| 8      | INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS                  | 24        |
| 9      | ```abi.encode()``` IS LESS EFFICIENT THAN ```abi.encodePacked()```              | 1         |
| 10     | BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE     | 1         |
| 11     | STACK VARIABLE USED AS A CHEAPER CACHE FOR A STATE VARIABLE IS ONLY USED ONCE   | 6         |
| 12     | USAGE OF UINT/INT SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD              | 12        |
| 13     | USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS                    | 5         |


# Gas Optimizations
## [G-01] ```x += y``` COSTS MORE GAS THAN ```x = x + y``` FOR STATE VARIABLES
Using the addition operator instead of plus-equals saves some gas for state variables.

packages\contracts\src\plugins\governance\multisig\Multisig.sol:
```js
  276:             proposal_.approvals += 1;
```

## [G-02] CAN DECLARE VARIABLE OUTSIDE LOOP TO SAVE GAS
Declaring the stack variable outside the loop will save gas that would otherwise be spent on declaring the variable over and over again.     

packages\contracts\src\core\dao\DAO.sol
```js
185:             address to = _actions[i].to;
186:             (bool success, bytes memory response) = to.call{value: _actions[i].value}(
187:                 _actions[i].data
188:             );
```

packages\contracts\src\core\permission\PermissionManager.sol:
```js
151              PermissionLib.SingleTargetPermission memory item = items[i];
171              PermissionLib.MultiTargetPermission memory item = _items[i];
```

packages\contracts\src\framework\dao\DAOFactory.sol
```js
098:                 address plugin,
099:                 IPluginSetup.PreparedSetupData memory preparedSetupData
100:             ) = pluginSetupProcessor.prepareInstallation(
101:                     address(createdDao),
102:                     PluginSetupProcessor.PrepareInstallationParams(
103:                         _pluginSettings[i].pluginSetupRef,
104:                         _pluginSettings[i].data
105:                     )
106:                 );
```

packages\contracts\src\framework\utils\RegistryUtils.sol
```js
17:         uint8 char = uint8(nameBytes[i]);
```

## [G-03] ++i/i++ CAN BE UNCHECKED 
++i/i++ can be done in an unchecked block in for loops where the operation would not cause an overflow
packages\contracts\src\framework\dao\DAOFactory.sol
```js
95:         for (uint256 i; i < _pluginSettings.length; ++i) {
```


## [G-04] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD
Contracts are allowed to override their parents’ functions and change the visibility from public to external and can save gas by doing so.

packages\contracts\src\core\plugin\Plugin.sol
```js
20:     function pluginType() public pure override returns (PluginType) {

27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

packages\contracts\src\core\plugin\PluginCloneable.sol
```js
27:     function pluginType() public pure override returns (PluginType) {
28:         return PluginType.Cloneable;

34:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

packages\contracts\src\core\plugin\PluginUUPSUpgradeable.sol
```js
30:     function pluginType() public pure override returns (PluginType) {
31:         return PluginType.UUPS;

46:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

55:     function implementation() public view returns (address) {
```

packages\contracts\src\core\plugin\dao-authorizable\DaoAuthorizable.sol
```js
25:     function dao() public view returns (IDAO) {
```

packages\contracts\src\core\plugin\dao-authorizable\DaoAuthorizableUpgradeable.sol
```js
26:     function dao() public view returns (IDAO) {
```

packages\contracts\src\core\plugin\proposal\Proposal.sol
```js
27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

packages\contracts\src\core\plugin\proposal\ProposalUpgradeable.sol
```js
27:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

packages\contracts\src\framework\plugin\repo\PluginRepo.sol
```js
244:     function buildCount(uint8 _release) public view returns (uint256) {
```

packages\contracts\src\framework\plugin\setup\PluginSetup.sol
```
43:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

packages\contracts\src\plugins\counter-example\v1\CounterV1.sol
```js
39:     function multiply(uint256 _a) public view auth(MULTIPLY_PERMISSION_ID) returns (uint256) {
```

packages\contracts\src\plugins\governance\admin\Admin.sol
```js
38:     function supportsInterface(
39:         bytes4 _interfaceId
40:     ) public view override(PluginCloneable, ProposalUpgradeable) returns (bool) {
```

packages\contracts\src\plugins\governance\majority-voting\MajorityVotingBase.sol
```js
392:     function getProposal(
393:         uint256 _proposalId
394:     )
395:         public
396:         view
397:         virtual
398:         returns (

249:     function supportsInterface(
250:         bytes4 _interfaceId
251:     )
252:         public
253:         view
254:         virtual
255:         override(ERC165Upgradeable, PluginUUPSUpgradeable, ProposalUpgradeable)
256:         returns (bool)

375:     function votingMode() public view virtual returns (VotingMode) {

369:     function minProposerVotingPower() public view virtual returns (uint256) {

363:     function minDuration() public view virtual returns (uint64) {

357:     function minParticipation() public view virtual returns (uint32) {

357:     function minParticipation() public view virtual returns (uint32) {

352:     function supportThreshold() public view virtual returns (uint32) {

308:     function canExecute(uint256 _proposalId) public view virtual returns (bool) {

299:     function canVote(
300:         uint256 _proposalId,
301:         address _voter,
302:         VoteOption _voteOption
303:     ) public view virtual returns (bool) {

291:     function getVoteOption(
292:         uint256 _proposalId,
293:         address _voter
294:     ) public view virtual returns (VoteOption) {

283:     function execute(uint256 _proposalId) public virtual {

265:     function vote(
266:         uint256 _proposalId,
267:         VoteOption _voteOption,
268:         bool _tryEarlyExecution
269:     ) public virtual {
```

packages\contracts\src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol
```js
48:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

51:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

61:     function getVotingToken() public view returns (IVotesUpgradeable) {
```

packages\contracts\src\plugins\governance\multisig\Multisig.sol
```js
141:     function supportsInterface(
142:         bytes4 _interfaceId
143:     ) public view virtual override(PluginUUPSUpgradeable, ProposalUpgradeable) returns (bool) {

305:     function getProposal(
306:         uint256 _proposalId
307:     )
308:         public
309:         view
310:         returns (

328:     function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {

333:     function execute(uint256 _proposalId) public {
```

packages\contracts\src\plugins\token\MerkleDistributor.sol
```js
59:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

83:     function unclaimedBalance(
84:         uint256 _index,
85:         address _to,
86:         uint256 _amount,
87:         bytes32[] memory _proof
88:     ) public view override returns (uint256) {
```

packages\contracts\src\plugins\token\MerkleMinter.sol
```js
62:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

packages\contracts\src\plugins\utils\Addresslist.sol
```js
30:     function isListedAtBlock(
31:         address _account,
32:         uint256 _blockNumber
33:     ) public view virtual returns (bool) {

47:     function addresslistLengthAtBlock(uint256 _blockNumber) public view virtual returns (uint256) {

53:     function addresslistLength() public view virtual returns (uint256) {
```

packages\contracts\src\token\ERC20\governance\GovernanceERC20.sol
```js
93:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {
```

packages\contracts\src\token\ERC20\governance\GovernanceWrappedERC20.sol
```js
59:     function supportsInterface(bytes4 _interfaceId) public view virtual override returns (bool) {

71:     function decimals()
72:         public
73:         view
74:         override(ERC20Upgradeable, ERC20WrapperUpgradeable)
75:         returns (uint8)

81:     function depositFor(
82:         address account,
83:         uint256 amount
84:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {

89:     function withdrawTo(
90:         address account,
91:         uint256 amount
92:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {
```

## [G-05] REMOVE UNUSED FUNCTIONS
packages\contracts\src\plugins\counter-example\v1\CounterV1.sol
```js
44:     function execute() public {
```

## [G-06] USE ```bytes32``` INSTEAD OF ```string``` WHEREVER POSSIBLE
Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

packages\contracts\src\framework\utils\TokenFactory.sol
```js
58:         string name;
59:         string symbol;
```

packages\contracts\src\plugins\governance\majority-voting\token\TokenVotingSetup.sol
```js
44:         string name;
45:         string symbol;
```

packages\contracts\src\token\ERC20\governance\GovernanceERC20.sol
```js
51:         string memory _name,
52:         string memory _symbol,

65:         string memory _name,
66:         string memory _symbol,
```

packages\contracts\src\token\ERC20\governance\GovernanceWrappedERC20.sol
```js
38:     constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

48:         string memory _name,
49:         string memory _symbol
```

## [G-07] OPTIMIZE NAMES TO SAVE GAS
Function hashes that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted. Prioritize the most called functions and sort and rename them according to the function hashes/method IDs. For a better understanding please refer to [this link](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
The method IDs in the DAO.sol will be the entry point for most contracts. A lower method ID may be given to the most frequently used functions. [This](https://emn178.github.io/solidity-optimize-name/) is a useful tool that can be used for the same, if required.

```json
Sighash   |   Function Signature
========================
eafb8b06  =>  initialize(bytes,address,address,string)
552b1863  =>  isPermissionRestrictedForAnyAddr(bytes32)
5ec29272  =>  _authorizeUpgrade(address)
da742228  =>  setTrustedForwarder(address)
ce1b815f  =>  getTrustedForwarder()
fdef9106  =>  hasPermission(address,address,bytes32,bytes)
ee57e36f  =>  setMetadata(bytes)
f01de670  =>  execute(bytes32,Action[],uint256)
bfe07da6  =>  deposit(address,uint256,string)
3e2ab0d9  =>  setSignatureValidator(address)
1626ba7e  =>  isValidSignature(bytes32,bytes)
4146c61e  =>  _setMetadata(bytes)
d292f98a  =>  _setTrustedForwarder(address)
f3e54f30  =>  _registerTokenInterfaces()
c4a50145  =>  registerStandardCallback(bytes4,bytes4,bytes4)
7034731b  =>  daoURI()
1080f99b  =>  setDaoURI(string)
f392bdc0  =>  _setDaoURI(string)
```

The methods in PluginSetupProcessor.sol can also be optimized for better gas savingd.

```json
Sighash   |   Function Signature
========================
59974292  =>  _canApply(address,bytes32)
f5e318c8  =>  prepareInstallation(address,PrepareInstallationParams)
7bdc74cc  =>  applyInstallation(address,ApplyInstallationParams)
b67a4db3  =>  prepareUpdate(address,PrepareUpdateParams)
40c555df  =>  applyUpdate(address,ApplyUpdateParams)
814d47eb  =>  prepareUninstallation(address,PrepareUninstallationParams)
0907c8e9  =>  applyUninstallation(address,ApplyUninstallationParams)
fafc79da  =>  validatePreparedSetupId(bytes32,bytes32)
51395af1  =>  _upgradeProxy(address,address,bytes)
12aadc6b  =>  emitPrepareUpdateEvent(address,bytes32,PrepareUpdateParams,IPluginSetup.PreparedSetupData,bytes)
```

## [G-08] INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS
Not inlining costs 20 to 40 gas because of two extra ```JUMP``` instructions and additional stack operations needed for function calls.

packages\contracts\src\core\permission\PermissionManager.sol
```js
212:     function _initializePermissionManager(address _initialOwner) internal {
```

packages\contracts\src\framework\dao\DAOFactory.sol
```js
141:     function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {

156:     function _setDAOPermissions(DAO _dao) internal {
```

packages\contracts\src\framework\plugin\repo\PluginRepoFactory.sol
```js
65:     function _setPluginRepoPermissions(PluginRepo pluginRepo, address maintainer) internal {
```

### [G-09] ```abi.encode()``` IS LESS EFFICIENT THAN ```abi.encodePacked()``` 
Use abi.encodePacked() where possible to save gas

packages\contracts\src\core\dao\DAO.sol
```js
272:         return abi.encode(magicNumber);
```


## [G-10] BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE

Before transfer, we should check for amount being 0 so the function doesnt run when its not gonna do anything.

packages\contracts\src\plugins\token\MerkleDistributor.sol
```js
77:         token.safeTransfer(_to, _amount);
```

## [G-11] STACK VARIABLE USED AS A CHEAPER CACHE FOR A STATE VARIABLE IS ONLY USED ONCE
Caching a state variable and only using it once is unnecessary, using the state variable directly instead saves some gas. 

packages\contracts\src\core\dao\DAO.sol
```js
185:             address to = _actions[i].to;
```

packages\contracts\src\framework\dao\DAORegistry.sol
```js
64:             bytes32 labelhash = keccak256(bytes(subdomain));
```

packages\contracts\src\plugins\token\MerkleDistributor.sol
```js
105:         bytes32 node = keccak256(abi.encodePacked(_index, _to, _amount));
```

packages\contracts\src\framework\plugin\repo\PluginRepo.sol
```js
210:         uint16 latestBuild = uint16(buildsPerRelease[_release]);
```

packages\contracts\src\framework\plugin\repo\PluginRepoRegistry.sol
```js
42:         bytes4 pluginRepoInterfaceId = type(IPluginRepo).interfaceId;

63:         bytes32 labelhash = keccak256(bytes(subdomain));
```

## [G-12] USAGE OF UINT/INT SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

packages\contracts\src\framework\plugin\repo\PluginRepo.sol
```js
65:     uint8 public latestRelease;

165:         uint16 build = ++buildsPerRelease[_release];

210:         uint16 latestBuild = uint16(buildsPerRelease[_release]);
```

packages\contracts\src\framework\utils\RegistryUtils.sol
```js
17:         uint8 char = uint8(nameBytes[i]);
```

packages\contracts\src\plugins\governance\multisig\Multisig.sol
```js
175:         uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

414:         uint16 addresslistLength_ = uint16(addresslistLength());

404:         uint64 currentTimestamp64 = block.timestamp.toUint64();

214:         uint64 snapshotBlock = block.number.toUint64() - 1;
```

packages\contracts\src\plugins\governance\majority-voting\MajorityVotingBase.sol
```js
516:         uint64 currentTime = block.timestamp.toUint64();

568:         uint64 currentTimestamp = block.timestamp.toUint64();

580:         uint64 earliestEndDate = startDate + votingSettings.minDuration; // Since `minDuration` is limited to 1 year, `startDate + minDuration` can only overflow if the `startDate` is after `type(uint64).max - minDuration`. In this case, the proposal creation will revert and another date can be picked.
```

packages\contracts\src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol
```js
92:         uint64 snapshotBlock;
```

## [G-13] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

packages\contracts\src\framework\plugin\setup\PluginSetupProcessor.sol
```js
298:         PluginRepo.Version memory version = pluginSetupRepo.getVersion(
299:             _params.pluginSetupRef.versionTag
300:         );

433:         PluginRepo.Version memory currentVersion = _params.pluginSetupRepo.getVersion(
434              _params.currentVersionTag

437:         PluginRepo.Version memory newVersion = _params.pluginSetupRepo.getVersion(
438              _params.newVersionTag

523:         PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(
524              _params.pluginSetupRef.versionTag

572:         PluginRepo.Version memory version = _params.pluginSetupRef.pluginSetupRepo.getVersion(
573              _params.pluginSetupRef.versionTag
```