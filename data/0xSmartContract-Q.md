## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]|Insufficient coverage|  |
|[L-02]|No Storage Gap for ` Admin.sol` | 1 |
|[L-03]|Project Upgrade and Stop Scenario should be |  |
|[L-04]|Use Fuzzing Test for complicated code bases | |
|[L-05]|Add to Event-Emit for critical functions| 6 |
|[L-06]|`ProposoalCreated` events are missing parameters | 2 |
|[L-07]|Keccak Constant values should used to immutable rather than constant| 14 |
|[L-08]|Draft Openzeppelin Dependencies| 1 |
|[L-09]|Missing Event for initialize | 15 |
|[L-10]|Exact number of days in a year| 2 |

Total 10 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01] |Omissions in Events|1|
| [N-02] |`Function writing` that does not comply with the `Solidity Style Guide` |All Contracts|
| [N-03] |For modern and more readable code; update import usages| 20 |
| [N-04] |Implement some type of version counter that will be incremented automatically for contract upgrades | 5 |
| [N-05] |Tokens accidentally sent to the contract cannot be recovered | |
| [N-06] |Repeated validation logic can be converted into a function modifier | 6 |
| [N-07] |For functions, follow Solidity standard naming conventions (internal function style rule) | 14 |
| [N-08] |Use descriptive names for Contracts and Libraries|  |
| [N-09] |Use SMTChecker | |
| [N-10] |Showing the actual values of numbers in NatSpec comments makes checking and reading code easier| 2  |
| [N-11] |Lines are too long|15 |
| [N-12] |Constants on the left are better| 2 |
| [N-13] |`constants` should be defined rather than using magic numbers| 1 |
| [N-14] |Function Calls in Loop Could Lead to Denial of Service due to Array length not being checked|  |
| [N-15] |Use a single file for all system-wide constants| 29 |
| [N-16] |Highest risk must be specified in NatSpec comments and documentation| 1 |
| [N-17] |Include proxy contracts to Audit| 1 |
| [N-18] |Use of bytes.concat() instead of abi.encodePacked()| 1 |
| [N-19] |Don't use _msgSender() if not supporting EIP-2771| 14 |
| [N-20] |Use parameters of custom Errors| 14 |

Total 20 issues


### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Generate perfect code headers every time |

### [L-01] Insufficient coverage

**Description:**
The test coverage rate of the project is ~60%. Testing all functions is best practice in terms of security criteria.
Due to its capacity, test coverage is expected to be 100%.


```js

1 result - 1 file

README.md:
  212: - What is the overall line coverage percentage provided by your tests?:  60
```



### [L-02] No Storage Gap for ` Admin.sol`

```js
src/plugins/governance/admin/Admin.sol:
  15: contract Admin is IMembership, PluginCloneable, ProposalUpgradeable {
```

### Impact
For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

If no storage gap is added, when the upgradable contract introduces new variables, 
it may override the variables in the inheriting contract.

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts.
To create a storage gap, declare a fixed-size array in the base contract with an initial number of slots. 
This can be an array of uint256 so that each element reserves a 32 byte slot. Use the naming convention __gap so that OpenZeppelin Upgrades will recognize the gap:

Classification for a similar problem:
https://code4rena.com/reports/2022-05-alchemix/#m-05-no-storage-gap-for-upgradeable-contract-might-lead-to-storage-slot-collision

```js
contract Base {
    uint256 base1;
    uint256[49] __gap;
}

contract Child is Base {
    uint256 child;
}
```

Openzeppelin Storage Gaps notification:
```js
Storage Gaps
This makes the storage layouts incompatible, as explained in Writing Upgradeable Contracts. 
The size of the __gap array is calculated so that the amount of storage used by a contract 
always adds up to the same number (in this case 50 storage slots).
```



### Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable contract
```js
uint256[50] private __gap;
```

### [L-03] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


### [L-04]  Use Fuzzing Test for complicated code bases 


I recommend fuzzing testing in complex code structures, especially Aragon, where there is an innovative code base and a lot of computation.

**Recommendation:**

Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:

_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._



https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [L-05] Add to Event-Emit for critical functions


```solidity
6 result


src/framework/utils/TokenFactory.sol:
  144:     function setupBases() private {
  145:         distributorBase = new MerkleDistributor();
  146:         governanceERC20Base = address(
  147:             new GovernanceERC20(
  148:                 IDAO(address(0)),
  149:                 "baseName",
  150:                 "baseSymbol",
  151:                 GovernanceERC20.MintSettings(new address[](0), new uint256[](0))
  152:             )
  153:         );
  154:         governanceWrappedERC20Base = address(
  155:             new GovernanceWrappedERC20(IERC20Upgradeable(address(0)), "baseName", "baseSymbol")
  156:         );
  157:         merkleMinterBase = address(new MerkleMinter());
  158:     }

src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  100:     function setDefaultResolver(
  101:         address _resolver
  102:     ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
  103:         if (_resolver == address(0)) {
  104:             revert InvalidResolver({node: node, resolver: _resolver});
  105:         }
  106: 
  107:         resolver = _resolver;
  108:     }


src/framework/utils/TokenFactory.sol:
  144:     function setupBases() private {
  145:         distributorBase = new MerkleDistributor();
  146:         governanceERC20Base = address(
  147:             new GovernanceERC20(
  148:                 IDAO(address(0)),
  149:                 "baseName",
  150:                 "baseSymbol",
  151:                 GovernanceERC20.MintSettings(new address[](0), new uint256[](0))
  152:             )
  153:         );
  154:         governanceWrappedERC20Base = address(
  155:             new GovernanceWrappedERC20(IERC20Upgradeable(address(0)), "baseName", "baseSymbol")
  156:         );
  157:         merkleMinterBase = address(new MerkleMinter());
  158:     }


src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  82:     function registerSubnode(
  83:         bytes32 _label,
  84:         address _targetAddress
  85:     ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
  86:         bytes32 subnode = keccak256(abi.encodePacked(node, _label));
  87:         address currentOwner = ens.owner(subnode);
  88: 
  89:         if (currentOwner != address(0)) {
  90:             revert AlreadyRegistered(subnode, currentOwner);
  91:         }
  92: 
  93:         ens.setSubnodeOwner(node, _label, address(this));
  94:         ens.setResolver(subnode, resolver);
  95:         Resolver(resolver).setAddr(subnode, _targetAddress);
  96:     }


src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol:
  20:     function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {
  21:         dao_ = _dao;
  22:     }


src/plugins/governance/multisig/MultisigSetup.sol:
  24:     function prepareInstallation(
  25:         address _dao,
  26:         bytes calldata _data
  27:     ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {
  28:         // Decode `_data` to extract the params needed for deploying and initializing `Multisig` plugin.
  29:         (address[] memory members, Multisig.MultisigSettings memory multisigSettings) = abi.decode(
  30:             _data,
  31:             (address[], Multisig.MultisigSettings)
  32:         );
  33: 
  34:         // Prepare and Deploy the plugin proxy.
  35:         plugin = createERC1967Proxy(
  36:             address(multisigBase),
  37:             abi.encodeWithSelector(Multisig.initialize.selector, _dao, members, multisigSettings)
  38:         );
  39: 
  40:         // Prepare permissions
  41:         PermissionLib.MultiTargetPermission[]
  42:             memory permissions = new PermissionLib.MultiTargetPermission[](3);
  43: 
  44:         // Set permissions to be granted.
  45:         // Grant the list of prmissions of the plugin to the DAO.
  46:         permissions[0] = PermissionLib.MultiTargetPermission(
  47:             PermissionLib.Operation.Grant,
  48:             plugin,
  49:             _dao,
  50:             PermissionLib.NO_CONDITION,
  51:             multisigBase.UPDATE_MULTISIG_SETTINGS_PERMISSION_ID()
  52:         );
  53: 
  54:         permissions[1] = PermissionLib.MultiTargetPermission(
  55:             PermissionLib.Operation.Grant,
  56:             plugin,
  57:             _dao,
  58:             PermissionLib.NO_CONDITION,
  59:             multisigBase.UPGRADE_PLUGIN_PERMISSION_ID()
  60:         );
  61: 
  62:         // Grant `EXECUTE_PERMISSION` of the DAO to the plugin.
  63:         permissions[2] = PermissionLib.MultiTargetPermission(
  64:             PermissionLib.Operation.Grant,
  65:             _dao,
  66:             plugin,
  67:             PermissionLib.NO_CONDITION,
  68:             DAO(payable(_dao)).EXECUTE_PERMISSION_ID()
  69:         );
  70: 
  71:         preparedSetupData.permissions = permissions;
  72:     }
```


### [L-06] `ProposoalCreated` events are missing parameters


The `Proposal.sol` and `ProposolUpgradable.sol` contracts have very important function; `_createProposal` 


However, only amounts are published in emits, whereas msg.sender must be specified in every transaction, a contract or web page listening to events cannot react to users, emit does not serve the purpose.
Basically, this event cannot be used


```solidity
2 results - 2 files

src/core/plugin/proposal/Proposal.sol:
  45:     function _createProposal(
  46:         address _creator,
  47:         bytes calldata _metadata,
  48:         uint64 _startDate,
  49:         uint64 _endDate,
  50:         IDAO.Action[] calldata _actions,
  51:         uint256 _allowFailureMap
  52:     ) internal virtual returns (uint256 proposalId) {
  53:         proposalId = _createProposalId();
  54: 
  55:         emit ProposalCreated({
  56:             proposalId: proposalId,
  57:             creator: _creator,
  58:             metadata: _metadata,
  59:             startDate: _startDate,
  60:             endDate: _endDate,
  61:             actions: _actions,
  62:             allowFailureMap: _allowFailureMap
  63:         });
  64:     }

src/core/plugin/proposal/ProposalUpgradeable.sol:
  45:     function _createProposal(
  46:         address _creator,
  47:         bytes calldata _metadata,
  48:         uint64 _startDate,
  49:         uint64 _endDate,
  50:         IDAO.Action[] calldata _actions,
  51:         uint256 _allowFailureMap
  52:     ) internal virtual returns (uint256 proposalId) {
  53:         proposalId = _createProposalId();
  54: 
  55:         emit ProposalCreated({
  56:             proposalId: proposalId,
  57:             creator: _creator,
  58:             metadata: _metadata,
  59:             startDate: _startDate,
  60:             endDate: _endDate,
  61:             actions: _actions,
  62:             allowFailureMap: _allowFailureMap
  63:         });
  64:     }


```



### Recommended Mitigation Steps
add `msg.sender` parameter in event-emit

### [L-07] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.



```solidity
14 results 

src/core/dao/DAO.sol:
  40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
  43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
  46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");


src/core/permission/PermissionManager.sol:
  15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

src/core/plugin/PluginUUPSUpgradeable.sol:
  35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

src/framework/dao/DAORegistry.sol:
  15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

src/framework/plugin/repo/PluginRepo.sol:
  49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
  52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

src/framework/plugin/setup/PluginSetupProcessor.sol:
  31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

src/plugins/counter-example/MultiplyHelper.sol:
  12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src/plugins/counter-example/v1/CounterV1.sol:
  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src/plugins/counter-example/v2/CounterV2.sol:
  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src/plugins/token/MerkleMinter.sol:
  24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

src/token/ERC20/governance/GovernanceERC20.sol:
  28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```
### [L-08] Draft Openzeppelin Dependencies

The `PluginUUPSUpgradeable.sol` contracts utilised `draft-IERC1822Upgradeable.sol` an OpenZeppelin contract. This contract is still a draft and are not considered ready for mainnet use. OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.


```solidity
1 result - 1 file

src/core/plugin/PluginUUPSUpgradeable.sol:
  6: import {IERC1822ProxiableUpgradeable} from "@openzeppelin/contracts-upgradeable/interfaces/draft-IERC1822Upgradeable.sol";

```

### [L-09] Missing Event for initialize

**Context:**
```solidity
15 results - 15 files

src/core/dao/DAO.sol:
  104:     function initialize(

src/framework/dao/DAORegistry.sol:
  37:     function initialize(

src/framework/plugin/repo/PluginRepo.sol:
  120:     function initialize(address initialOwner) external initializer {

src/framework/plugin/repo/PluginRepoRegistry.sol:
  41:     function initialize(IDAO _dao, ENSSubdomainRegistrar _subdomainRegistrar) external initializer {

src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  57:     function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {

src/plugins/counter-example/v1/CounterV1.sol:
  26:     function initialize(

src/plugins/counter-example/v2/CounterV2.sol:
  32:     function initialize(

src/plugins/governance/admin/Admin.sol:
  29:     function initialize(IDAO _dao) external initializer {

src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol:
  34:     function initialize(

src/plugins/governance/majority-voting/token/TokenVoting.sol:
  36:     function initialize(

src/plugins/governance/multisig/Multisig.sol:
  125:     function initialize(

src/plugins/token/MerkleDistributor.sol:
  46:     function initialize(

src/plugins/token/MerkleMinter.sol:
  41:     function initialize(

src/token/ERC20/governance/GovernanceERC20.sol:
  63:     function initialize(

src/token/ERC20/governance/GovernanceWrappedERC20.sol:
  46:     function initialize(

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
Issuing event-emit during initialization is a detail that many projects skip

**Recommendation:**
Add Event-Emit

### [L-10] Exact number of days in a year

The true length of a year on Earth is 365.2422 days, or about 365.25 days
We keep our calendar in sync with the seasons by having most years 365 days long but making just under 1/4 of all years 366-day "leap" years

```solidity
2 results - 1 file

src/plugins/governance/majority-voting/MajorityVotingBase.sol:
  543  
- 544:         if (_votingSettings.minDuration > 365 days) {
+ 544:         if (_votingSettings.minDuration > 365.2422 days) {

- 545:             revert MinDurationOutOfBounds({limit: 365 days, actual: _votingSettings.minDuration});
+ 545:             revert MinDurationOutOfBounds({limit: 365.2422 days, actual: _votingSettings.minDuration});
  546          }

```


### [N-01] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

```solidity
src/core/dao/DAO.sol:
  325      /// @param newDaoURI The new DAO uri to be set.
  326:     function setDaoURI(string calldata newDaoURI) external auth(SET_METADATA_PERMISSION_ID) {
  327:         _setDaoURI(newDaoURI);
  328:     }
  329: 
  330:     /// @notice Sets the new DAO uri and emits the associated event.
  331:     /// @param daoURI_ The new DAO uri.
  332:     function _setDaoURI(string calldata daoURI_) internal {
  333:         _daoURI = daoURI_;
  334: 
  335:         emit NewURI(daoURI_);
  336:     }

```


### [N-02] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-03] For modern and more readable code; update import usages

**Context:**
```solidity
20 results - 7 files

src/core/dao/DAO.sol:
  
   5: import "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165StorageUpgradeable.sol";
   6: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
   7: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
   8: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
   9: import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";
  10: import "@openzeppelin/contracts-upgradeable/token/ERC721/IERC721ReceiverUpgradeable.sol";
  11: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";
  12: import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155ReceiverUpgradeable.sol";
  13: import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";
  14: import "@openzeppelin/contracts/interfaces/IERC1271.sol";
  

src/core/permission/PermissionManager.sol:
  
  5: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
  7: import "./IPermissionCondition.sol";
  8: import "./PermissionLib.sol";

src/core/plugin/proposal/Proposal.sol:
  8: import "./IProposal.sol";

src/core/plugin/proposal/ProposalUpgradeable.sol:
  8: import "./IProposal.sol";

src/framework/utils/ens/ENSMigration.sol:
  7: import "@ensdomains/ens-contracts/contracts/registry/ENSRegistry.sol";
  8: import "@ensdomains/ens-contracts/contracts/resolvers/PublicResolver.sol";

src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  5: import "@ensdomains/ens-contracts/contracts/registry/ENS.sol";
  6: import "@ensdomains/ens-contracts/contracts/resolvers/Resolver.sol";
 
src/utils/Proxy.sol:
  5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";


```
**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-04] Implement some type of version counter that will be incremented automatically for contract upgrades

As part of the upgradeability of  Proxies , the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

```js
5 results - 5 files

src/core/dao/DAO.sol:
  135      /// @dev The caller must have the `UPGRADE_DAO_PERMISSION_ID` permission.
  136:     function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}
  137  

src/core/plugin/PluginUUPSUpgradeable.sol:
  60      /// @dev The caller must have the `UPGRADE_PLUGIN_PERMISSION_ID` permission.
  61:     function _authorizeUpgrade(
  62          address

src/framework/plugin/repo/PluginRepo.sol:
  256      /// @dev The caller must have the `UPGRADE_REPO_PERMISSION_ID` permission.
  257:     function _authorizeUpgrade(
  258          address

src/framework/utils/InterfaceBasedRegistry.sol:
  53      /// @dev The caller must have the `UPGRADE_REGISTRY_PERMISSION_ID` permission.
  54:     function _authorizeUpgrade(
  55          address

src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  73      /// @dev The caller must have the `UPGRADE_REGISTRAR_PERMISSION_ID` permission.
  74:     function _authorizeUpgrade(
  75          address


```
I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```diff

Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol:

+	uint256 public authorizeUpgradeCounter;

  189:     function _authorizeUpgrade(address) internal override {
  190:         _atLeastRole(DEFAULT_ADMIN_ROLE);
  191:         require(
  192:             upgradeProposalTime + UPGRADE_TIMELOCK < block.timestamp,
  193:             "Upgrade cooldown not initiated or still ongoing"
  194:         );
+	authorizeUpgradeCounter+=1;
  195:         clearUpgradeCooldown();
  196:     }

```

### [N-05] Tokens accidentally sent to the contract cannot be recovered


**Contex**
packages/contracts/src/framework/utils/TokenFactory.sol


It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```
### [N-06] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code
(!= address(0))

```solidity

6 results - 5 files

src/framework/utils/TokenFactory.sol:
  85          // deploy token
  86:         if (token != address(0)) {
  87              // Validate if token is ERC20

src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  88  
  89:         if (currentOwner != address(0)) {
  90              revert AlreadyRegistered(subnode, currentOwner);

src/plugins/governance/majority-voting/token/TokenVotingSetup.sol:
   97  
   98:         if (token != address(0)) {
   99              if (!token.isContract()) {

  150              memory permissions = new PermissionLib.MultiTargetPermission[](
  151:                 tokenSettings.addr != address(0) ? 3 : 4
  152              );

src/token/ERC20/governance/GovernanceERC20.sol:
  115          // Automatically turn on delegation on mint/transfer but only for the first time.
  116:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {
  117              _delegate(to, to);

src/token/ERC20/governance/GovernanceWrappedERC20.sol:
  105          // Automatically turn on delegation on mint/transfer but only for the first time.
  106:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {
  107              _delegate(to, to);

```



### [N-07] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity

14 results - 6 files

src/core/dao/DAO.sol:
   61:     uint256 internal constant MAX_ACTIONS = 256;

src/core/permission/PermissionManager.sol:
   18:     address internal constant ANY_ADDR = address(type(uint160).max);
   21:     address internal constant UNSET_FLAG = address(0);
   24:     address internal constant ALLOW_FLAG = address(2);
   27:     mapping(bytes32 => address) internal permissionsHashed;

src/core/utils/CallbackHandler.sol:
  11:     mapping(bytes4 => bytes4) internal callbackMagicNumbers;
  14:     bytes4 internal constant UNREGISTERED_CALLBACK = bytes4(0);

src/framework/plugin/repo/PluginRepo.sol:
   55:     mapping(uint8 => uint16) internal buildsPerRelease;
   58:     mapping(bytes32 => Version) internal versions;
   61:     mapping(address => bytes32) internal latestTagHashForPluginSetup;
  251:     function tagHash(Tag memory _tag) internal pure returns (bytes32) {


src/plugins/governance/admin/Admin.sol:
  19:     bytes4 internal constant ADMIN_INTERFACE_ID =

src/plugins/governance/majority-voting/MajorityVotingBase.sol:
  173:     bytes4 internal constant MAJORITY_VOTING_BASE_INTERFACE_ID =
  187:     mapping(uint256 => Proposal) internal proposals;
```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [N-08] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.

### [N-09] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

### [N-10] Showing the actual values of numbers in NatSpec comments makes checking and reading code easier


```diff

2 results - 2 files

src/plugins/governance/majority-voting/MajorityVotingBase.sol:
- 540:         if (_votingSettings.minDuration < 60 minutes) {
+ 540:         if (_votingSettings.minDuration < 60 minutes) { // 40 minutes ( 40 * 60 ) = 2_400
  541:             revert MinDurationOutOfBounds({limit: 60 minutes, actual: _votingSettings.minDuration});
  542:         }
  543: 
- 544:         if (_votingSettings.minDuration > 365 days) {
+ 544:         if (_votingSettings.minDuration > 365 days) { // 365 days ( 365*24*60*60 ) = 31_536_000
  545:             revert MinDurationOutOfBounds({limit: 365 days, actual: _votingSettings.minDuration});
  546:         }


```

### [N-11] Lines are too long

[MajorityVotingBase.sol#L27](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L27)

[MajorityVotingBase.sol#L31](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L31)

[MajorityVotingBase.sol#L33-L34](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L33-L34)

[MajorityVotingBase.sol#L38](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L38)

[MajorityVotingBase.sol#L62-L63](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L62-L63)

[MajorityVotingBase.sol#L108](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L108)

[MajorityVotingBase.sol#L136](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L136)

[MajorityVotingBase.sol#L428](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L428)

[MajorityVotingBase.sol#L527](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L527)

[MajorityVotingBase.sol#L593](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L593)

[Multisig.sol#L33](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L33)

[Multisig.sol#L201](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L201)




[Multisig.sol#L304](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L304)

[PluginRepo.sol#L255](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L255)

[PermissionManager.sol#L26](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L26)



**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that.The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]

**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction


### [N-12] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns). 

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

```solidity
2 results - 2 files

src/token/ERC20/governance/GovernanceERC20.sol:
  116:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

src/token/ERC20/governance/GovernanceWrappedERC20.sol:
  106:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {
```



### [N-13] `constants` should be defined rather than using magic numbers

A magic number is a numeric literal that is used in the code without any explanation of its meaning. The use of magic numbers makes programs less readable and hence more difficult to maintain and update.
Even assembly can benefit from using readable constants instead of hex/numeric literals

```solidity
1 result - 1 file

src/plugins/utils/Ratio.sol:
  24:     result = _value / RATIO_BASE;
```


### [N-14] Function Calls in Loop Could Lead to Denial of Service due to Array length not being checked


```diff
src/framework/dao/DAOFactory.sol#63:

 function createDao(
        DAOSettings calldata _daoSettings,
        PluginSettings[] calldata _pluginSettings
    ) external returns (DAO createdDao) {
        // Check if no plugin is provided.
        if (_pluginSettings.length == 0) {
            revert NoPluginProvided();
        }
+      if(_pluginSettings.length > maxArrayLength) {
+          revert maxArrayLengt();
+       }

        // Create DAO.
        createdDao = _createDAO(_daoSettings);

        // Register DAO.
        daoRegistry.register(createdDao, msg.sender, _daoSettings.subdomain);

        // Get Permission IDs
        bytes32 rootPermissionID = createdDao.ROOT_PERMISSION_ID();
        bytes32 applyInstallationPermissionID = pluginSetupProcessor
            .APPLY_INSTALLATION_PERMISSION_ID();

        // Grant the temporary permissions.
        // Grant Temporarly `ROOT_PERMISSION` to `pluginSetupProcessor`.
        createdDao.grant(address(createdDao), address(pluginSetupProcessor), rootPermissionID);

        // Grant Temporarly `APPLY_INSTALLATION_PERMISSION` on `pluginSetupProcessor` to this `DAOFactory`.
        createdDao.grant(
            address(pluginSetupProcessor),
            address(this),
            applyInstallationPermissionID
        );

        // Install plugins on the newly created DAO.
        for (uint256 i; i < _pluginSettings.length; ++i) {
            // Prepare plugin.
            (
                address plugin,
                IPluginSetup.PreparedSetupData memory preparedSetupData
            ) = pluginSetupProcessor.prepareInstallation(
                    address(createdDao),
                    PluginSetupProcessor.PrepareInstallationParams(
                        _pluginSettings[i].pluginSetupRef,
                        _pluginSettings[i].data
                    )
                );

            // Apply plugin.
            pluginSetupProcessor.applyInstallation(
                address(createdDao),
                PluginSetupProcessor.ApplyInstallationParams(
                    _pluginSettings[i].pluginSetupRef,
                    plugin,
                    preparedSetupData.permissions,
                    hashHelpers(preparedSetupData.helpers)
                )
            );
        }

        // Set the rest of DAO's permissions.
        _setDAOPermissions(createdDao);

        // Revoke the temporarly granted permissions.
        // Revoke Temporarly `ROOT_PERMISSION` from `pluginSetupProcessor`.
        createdDao.revoke(address(createdDao), address(pluginSetupProcessor), rootPermissionID);

        // Revoke `APPLY_INSTALLATION_PERMISSION` on `pluginSetupProcessor` from this `DAOFactory` .
        createdDao.revoke(
            address(pluginSetupProcessor),
            address(this),
            applyInstallationPermissionID
        );

        // Revoke Temporarly `ROOT_PERMISSION_ID` from `pluginSetupProcessor` that implecitly granted to this `DaoFactory`
        // at the create dao step `address(this)` being the initial owner of the new created DAO.
        createdDao.revoke(address(createdDao), address(this), rootPermissionID);
    }

```

**Recommendation:**
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to gas limitations or failed transactions. Consider bounding the loop length if the array is expected to be growing and/or handling a huge list of elements to avoid unnecessary gas wastage and denial of service.


### [N-15] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity
29 results - 19 files

src/core/dao/DAO.sol:
  40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
  43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
  46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");
  49:     bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
  53:     bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
  57:     bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =

src/core/permission/PermissionLib.sol:
  10:     address public constant NO_CONDITION = address(0);

src/core/permission/PermissionManager.sol:
  15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

src/core/plugin/PluginUUPSUpgradeable.sol:
  35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

src/framework/dao/DAORegistry.sol:
  15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

src/framework/plugin/repo/PluginRepo.sol:
  49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
  52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

src/framework/plugin/repo/PluginRepoRegistry.sol:
  16:     bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =

src/framework/plugin/setup/PluginSetupProcessor.sol:
  27:     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
  31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");
  34:     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =

src/framework/utils/InterfaceBasedRegistry.sol:
  18:     bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =

src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  18:     bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
  22:     bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =

src/plugins/counter-example/MultiplyHelper.sol:
  12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src/plugins/counter-example/v1/CounterV1.sol:
  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src/plugins/counter-example/v2/CounterV2.sol:
  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src/plugins/governance/admin/Admin.sol:
  23:     bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =

src/plugins/governance/majority-voting/MajorityVotingBase.sol:
  183:     bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =

src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol:
  27:     bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =

src/plugins/governance/multisig/Multisig.sol:
  71:     bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =

src/plugins/token/MerkleMinter.sol:
  24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");
  27:     bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =

src/token/ERC20/governance/GovernanceERC20.sol:
  28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```

### [N-16] Highest risk must be specified in NatSpec comments and documentation


```solidity

Ethos-Vault/contracts/abstract/ReaperBaseStrategyv4.sol:
  189:     function _authorizeUpgrade(address) internal override {
  190:         _atLeastRole(DEFAULT_ADMIN_ROLE);
  191:         require(
  192:             upgradeProposalTime + UPGRADE_TIMELOCK < block.timestamp,
  193:             "Upgrade cooldown not initiated or still ongoing"
  194:         );
  195:         clearUpgradeCooldown();
  196:     }

```

**Description:**
Although the UUPS model has many advantages and the proposed proxy model is currently the UUPS model, there are some caveats we should be aware of before applying it to a real-world project.

One of the main attention: Since upgrades are done through the implementation contract with the help of the 
``` _authorizeUpgrade ``` method, there is a high risk of new implementations such as excluding the ``` _authorizeUpgrade ``` method, which can permanently kill the smart contract's ability to upgrade. Also, this pattern is somewhat complicated to implement compared to other proxy patterns.

**Recommendation:**
Therefore, this highest risk must be found in NatSpec comments and documents.

## [N-17] Include proxy contracts to Audit

The Proxy contract could not be seen in the checklist because it is an upgradable contract, only the implementation contracts are visible, I recommend including the Proxy contract in the audit for integrity in the audit.

### [N-18] Use of bytes.concat() instead of abi.encodePacked()

```solidity
src/framework/utils/ens/ENSSubdomainRegistrar.sol:
  82:     function registerSubnode(
  83:         bytes32 _label,
  84:         address _targetAddress
  85:     ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
  86:         bytes32 subnode = keccak256(abi.encodePacked(node, _label));


```
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, bytes.concat() is enabled

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)

### [N-19] Don't use _msgSender() if not supporting EIP-2771

Use ``msg.sender`` if the code does not implement EIP-2771 trusted forwarder support.

Reference: https://eips.ethereum.org/EIPS/eip-2771


```solidity
14 results - 7 files

src/core/plugin/dao-authorizable/DaoAuthorizable.sol:
  32:         _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol:
  33:         _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

src/plugins/governance/admin/Admin.sol:
  70:             _creator: _msgSender(),

src/plugins/governance/majority-voting/MajorityVotingBase.sol:
  270:         address account = _msgSender();

src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol:
   97:         if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
   98:             revert ProposalCreationForbidden(_msgSender());
  102:             _creator: _msgSender(),

src/plugins/governance/majority-voting/token/TokenVoting.sol:
  91:         if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {
  92:             revert ProposalCreationForbidden(_msgSender());
  96:             _creator: _msgSender(),

src/plugins/governance/multisig/Multisig.sol:
  216:         if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {
  217:             revert ProposalCreationForbidden(_msgSender());
  231:             _creator: _msgSender(),
  266:         address approver = _msgSender();

```

### [N-20] Use parameters of custom Errors

Custom errors can be parameterized to better reflect their respective error messages if need be.
For instance, the custom error instance below may be refactored as follows:



```solidity
14 results - 7 files

src/core/dao/DAO.sol:
  73:     error TooManyActions();
  80:     error ZeroAmount();

src/core/permission/PermissionManager.sol:
  51:     error ConditionNotPresentForAnyAddress();
  54:     error PermissionsForAnyAddressDisallowed();
  57:     error AnyAddressDisallowedForWhoAndWhere();

src/framework/dao/DAOFactory.sol:
  48:     error NoPluginProvided();

src/framework/plugin/repo/PluginRepo.sol:
  72:     error InvalidPluginSetupInterface();
  75:     error ReleaseZeroNotAllowed();
  89:     error EmptyReleaseMetadata();
  92:     error ReleaseDoesNotExist();

src/framework/plugin/repo/PluginRepoRegistry.sol:
  31:     error EmptyPluginRepoSubdomain();

src/framework/plugin/setup/PluginSetupProcessor.sol:
  139:     error PluginNonupgradeable(address plugin);
  168:     error PluginAlreadyInstalled();

src/plugins/governance/majority-voting/token/TokenVoting.sol:
  29:     error NoVotingPower();
```

### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
