|  | Issue | Instances |
| --- | --- | --- |
| [G-01] | Use constants instead of type(uintx).max | 3 |
| [G-02] | Use nested if and, avoid multiple check combinations | 11 |
| [G-03] | Setting the constructor to payable |  |
| [G-04] | Use assembly to write address storage values | 12 |

### [G-01] Use constants instead of type(uintx).max

Using type(uint16).max and similar operations consume more gas during both the distribution process and for each transaction compared to using constants.

```solidity
File: packages\contracts\src\core\permission\PermissionManager.sol

address internal constant ANY_ADDR = address(type(uint160).max);
```

```solidity
File: packages\contracts\src\plugins\governance\multisig\Multisig.sol

function addAddresses(address[] calldata _members) external auth(UPDATE_MULTISIG_SETTINGS_PERMISSION_ID) {
    ...
		if (newAddresslistLength > type(uint16).max) {
        revert AddresslistLengthOutOfBounds({
            limit: type(uint16).max,
            actual: newAddresslistLength
        });
    ...
}
```

### [G-02] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier-to-read code and better coverage reports.

```solidity
File: packages\contracts\src\core\permission\PermissionManager.sol

function _grantWithCondition(address _where,address _who,bytes32 _permissionId,IPermissionCondition _condition) internal virtual {
    if (_where == ANY_ADDR && _who == ANY_ADDR) {
    ...
}
```

```solidity
File: packages\contracts\src\framework\plugin\repo\PluginRepo.sol

function createVersion(uint8 _release,address _pluginSetup, bytes calldata _buildMetadata,bytes calldata _releaseMetadata) external auth(MAINTAINER_PERMISSION_ID) {
    ...
    if (version.tag.release != 0 && version.tag.release != _release) {
    ...
}
```

```solidity
File: packages\contracts\src\framework\utils\RegistryUtils.sol

function isSubdomainValid(string calldata subDomain) pure returns (bool) {
    ...
    if (char > 96 && char < 123) 
		...
    if (char > 47 && char < 58) 
		...
}
```

```solidity
File: packages\contracts\src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol

function createProposal(bytes calldata _metadata,IDAO.Action[] calldata _actions,uint256 _allowFailureMap,uint64 _startDate,uint64 _endDate,VoteOption _voteOption,bool _tryEarlyExecution) external override returns (uint256 proposalId) {
    ...
    if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
    ...
}

function _vote(uint256 _proposalId,VoteOption _voteOption,address _voter,bool _tryEarlyExecution) internal override {
    ...
    if (_tryEarlyExecution && _canExecute(_proposalId)) {
    ...
}
```

```solidity
File: packages\contracts\src\plugins\governance\majority-voting\token\TokenVoting.sol

function _vote(uint256 _proposalId,VoteOption _voteOption,address _voter,bool _tryEarlyExecution) internal override {
    ...
    if (_tryEarlyExecution && _canExecute(_proposalId)) {
    ...
}
```

```solidity
File: packages\contracts\src\plugins\governance\multisig\Multisig.sol

function createProposal(bytes calldata _metadata,IDAO.Action[] calldata _actions,uint256 _allowFailureMap,bool _approveProposal,bool _tryExecution,uint64 _startDate,uint64 _endDate) external returns (uint256 proposalId) {
    ...
    if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {
    ...
}

function approve(uint256 _proposalId, bool _tryExecution) public {
    ...
    if (_tryExecution && _canExecute(_proposalId)) {
    ...
}
```

```solidity
File: packages\contracts\src\token\ERC20\governance\GovernanceERC20.sol

function _afterTokenTransfer(address from, address to, uint256 amount) internal override {
    ...
    if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {
    ...
}
```

```solidity
File: packages\contracts\src\token\ERC20\governance\GovernanceWrappedERC20.sol

function _afterTokenTransfer(address from,address to,uint256 amount) internal override(ERC20VotesUpgradeable, ERC20Upgradeable) {
    ...
		if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {
    ...
}
```

### [G-03] ****Setting the *constructor* to `payable`**

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves `13 gas` on deployment with no security risks.

****Context: all contracts with constructor****

### [G-04] Use `assembly` to write *address storage values*

```solidity
File: packages\contracts\src\core\dao\DAO.sol

function _setTrustedForwarder(address _trustedForwarder) internal {
    trustedForwarder = _trustedForwarder;
		...
}
```

```solidity
File: packages\contracts\src\framework\dao\DAOFactory.sol

constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
    ...
    daoBase = address(new DAO());
}
```

```solidity
File: packages\contracts\src\framework\plugin\repo\PluginRepoFactory.sol

constructor(PluginRepoRegistry _pluginRepoRegistry) {
		...
    pluginRepoBase = address(new PluginRepo());
}
```

```solidity
File: packages\contracts\src\framework\utils\TokenFactory.sol

function setupBases() private {
    distributorBase = new MerkleDistributor();
    governanceERC20Base = address(
        new GovernanceERC20(
            IDAO(address(0)),
            "baseName",
            "baseSymbol",
            GovernanceERC20.MintSettings(new address[](0), new uint256[](0))
        )
    );
    governanceWrappedERC20Base = address(new GovernanceWrappedERC20(IERC20Upgradeable(address(0)), "baseName", "baseSymbol");
    merkleMinterBase = address(new MerkleMinter());
}
```

```solidity
File: packages\contracts\src\framework\utils\ens\ENSSubdomainRegistrar.sol

function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {
    ...
    resolver = nodeResolver;
}
```

```solidity
File: packages\contracts\src\plugins\governance\admin\AdminSetup.sol

constructor() {
    implementation_ = address(new Admin());
}
```

```solidity
File: packages\contracts\src\plugins\governance\majority-voting\token\TokenVotingSetup.sol

constructor() {
    governanceERC20Base = address(
        new GovernanceERC20(
            IDAO(address(0)),
            "",
            "",
            GovernanceERC20.MintSettings(new address[](0), new uint256[](0))
        )
    );
    governanceWrappedERC20Base = address(new GovernanceWrappedERC20(IERC20Upgradeable(address(0)), "", ""));
    tokenVotingBase = new TokenVoting();
}
```