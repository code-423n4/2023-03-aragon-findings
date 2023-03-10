## Summary

### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Add constructor initializers | 2 |
|[L-02]| `initialize()` function can be called by anybody | 8 |
|[L-03]| Lack of Input Validation | 1 |
|[L-04]| Loss of precision due to rounding | 3 |

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[N-01]|NatSpec comments should be increased in contracts|All Contracts|
|[N-02]|Function writing that does not comply with the Solidity Style Guide|All Contracts|
|[N-03]|Include return parameters in NatSpec comments|All Contracts|
|[N-04]|Use a single file for all system-wide constants|-|

### [L-01] Add constructor initializers

Note that this behaviour is also incorporated the OZ Wizard since the UUPS vulnerability discovery: “Additionally, we modified the code generated by the Wizard 19 to include a constructor that automatically initializes the implementation when deployed.” Furthermore, this thwarts any attempts to frontrun the initialization tx of these contracts:

```solidity
token\ERC20\governance\GovernanceWrappedERC20.sol
46:     function initialize(
47:         IERC20Upgradeable _token,
48:         string memory _name,
49:         string memory _symbol
50:     ) public initializer {
51:         __ERC20_init(_name, _symbol);
52:         __ERC20Permit_init(_name);
53:         __ERC20Wrapper_init(_token);
54:     }

token\ERC20\governance\GovernanceERC20.sol
63:     function initialize(
64:         IDAO _dao,
65:         string memory _name,
66:         string memory _symbol,
67:         MintSettings memory _mintSettings
68:     ) public initializer {
69:         // Check mint settings
70:         if (_mintSettings.receivers.length != _mintSettings.amounts.length) {
71:             revert MintSettingsArrayLengthMismatch({
72:                 receiversArrayLength: _mintSettings.receivers.length,
73:                 amountsArrayLength: _mintSettings.amounts.length
74:             });
75:         }
76: 
77:         __ERC20_init(_name, _symbol);
78:         __ERC20Permit_init(_name);
79:         __DaoAuthorizableUpgradeable_init(_dao);
80: 
81:         for (uint256 i; i < _mintSettings.receivers.length; ) {
82:             _mint(_mintSettings.receivers[i], _mintSettings.amounts[i]);
83: 
84:             unchecked {
85:                 ++i;
86:             }
87:         }
88:     }

```


### [L-02] `initialize()` function can be called by anybody
The `initialize()` function is a common function used in smart contracts to initialize the state variables of the contract. It is typically called only once during the deployment of the contract and is meant to set the initial values for the contract's state variables.

However, if the `initialize()` function is not properly secured, anyone can call it even after the contract has already been initialized. This can cause unexpected behavior or even create security vulnerabilities in the contract.

Furthermore, if the `initialize()` function modifies the state variable owner, then anyone who calls the function will become the new owner of the contract, which can give them full control over the contract. This is because the owner state variable typically has special permissions or privileges within the contract.

Therefore, it is important to ensure that the `initialize()` function is properly secured and only callable by authorized parties, such as the contract owner or a specific set of authorized addresses. This can help prevent unauthorized modifications to the contract's state variables and protect against potential security vulnerabilities.


```solidity

plugins\token\MerkleMinter.sol
41:     function initialize(
42:         IDAO _dao,
43:         IERC20MintableUpgradeable _token,
44:         IMerkleDistributor _distributorBase
45:     ) external initializer {
46:         __PluginUUPSUpgradeable_init(_dao);
47: 
48:         token = _token;
49:         distributorBase = _distributorBase;
50:     }
51: 


plugins\token\MerkleDistributor.sol
46:     function initialize(
47:         IDAO _dao,
48:         IERC20Upgradeable _token,
49:         bytes32 _merkleRoot
50:     ) external initializer {
51:         __PluginUUPSUpgradeable_init(_dao);
52:         token = _token;
53:         merkleRoot = _merkleRoot;
54:     }
55: 


plugins\governance\multisig\Multisig.sol
125:     function initialize(
126:         IDAO _dao,
127:         address[] calldata _members,
128:         MultisigSettings calldata _multisigSettings
129:     ) external initializer {
130:         __PluginUUPSUpgradeable_init(_dao);
131: 
132:         _addAddresses(_members);
133:         emit MembersAdded({members: _members});
134: 
135:         _updateMultisigSettings(_multisigSettings);
136:     }
137: 


plugins\governance\majority-voting\token\TokenVoting.sol
36:     function initialize(
37:         IDAO _dao,
38:         VotingSettings calldata _votingSettings,
39:         IVotesUpgradeable _token
40:     ) external initializer {
41:         __MajorityVotingBase_init(_dao, _votingSettings);
42: 
43:         votingToken = _token;
44: 
45:         emit MembershipContractAnnounced({definingContract: address(_token)});
46:     }
47: 


plugins\governance\majority-voting\addresslist\AddresslistVoting.sol
34:     function initialize(
35:         IDAO _dao,
36:         VotingSettings calldata _votingSettings,
37:         address[] calldata _members
38:     ) external initializer {
39:         __MajorityVotingBase_init(_dao, _votingSettings);
40: 
41:         _addAddresses(_members);
42:         emit MembersAdded({members: _members});
43:     }


plugins\governance\admin\Admin.sol
29:     function initialize(IDAO _dao) external initializer {
30:         __PluginCloneable_init(_dao);
31: 
32:         emit MembershipContractAnnounced({definingContract: address(_dao)});
33:     }
34: 


plugins\counter-example\v2\CounterV2.sol
32:     function initialize(
33:         IDAO _dao,
34:         MultiplyHelper _multiplyHelper,
35:         uint256 _count,
36:         uint256 _newVariable
37:     ) external reinitializer(2) {
38:         __PluginUUPSUpgradeable_init(_dao);
39: 
40:         count = _count;
41: 
42:         // Since this is V2 version, and some daos might want to install this right away
43:         // without installing CountV1, dev decides to also include setting newVariable
44:         newVariable = _newVariable;
45: 
46:         multiplyHelper = _multiplyHelper;
47:     }


plugins\counter-example\v1\CounterV1.sol
26:     function initialize(
27:         IDAO _dao,
28:         MultiplyHelper _multiplyHelper,
29:         uint256 _count
30:     ) external initializer {
31:         __PluginUUPSUpgradeable_init(_dao);
32: 
33:         count = _count;
34:         multiplyHelper = _multiplyHelper;
35:     }
36: 
```

## Recommended Mitigation Steps
Add a control that makes initialize() only call the Deployer Contract or EOA
EG.
``` solidity
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
}
```



### [L-03] Lack of Input Validation
Lack of input validation is the process of checking input data to ensure that it is valid and safe to process. Therefore, it is important for contracts to consider the lack of input validation to ensure that what is produced is secure and protected from potential security vulnerabilities. This can be achieved by ensuring that every user input is thoroughly checked and validated at every level of data processing, from user interface to database. By doing so, the application can be reliable and protected from security threats that may arise due to lack of input validation.

## Recommended Mitigation Steps

```diff
plugins\governance\admin\AdminSetup.sol
76:    function prepareUninstallation(
77:         address _dao,
78:         SetupPayload calldata _payload 
79:     ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {
+             require(_payload.plugin != address(0), "Plugin address cannot be zero.");  
80:         // Prepare permissions
81:         permissions = new PermissionLib.MultiTargetPermission[](1);
82: 
83:         permissions[0] = PermissionLib.MultiTargetPermission(
84:             PermissionLib.Operation.Revoke,
85:             _dao,
86:             _payload.plugin,
87:             PermissionLib.NO_CONDITION,
88:             DAO(payable(_dao)).EXECUTE_PERMISSION_ID()
89:         );
90:     }

```

### [L-04] Loss of precision due to rounding
``` solidity
plugins\utils\Ratio.sol
17: function _applyRatioCeiled(uint256 _value, uint256 _ratio) pure returns (uint256 result) {
18:     if (_ratio > RATIO_BASE) {
19:         revert RatioOutOfBounds({limit: RATIO_BASE, actual: _ratio});
20:     }
21: 
22:     _value = _value * _ratio;
23:     uint256 remainder = _value % RATIO_BASE;
24:     result = _value / RATIO_BASE;
25: 
26:     // Check if ceiling is needed
27:     if (remainder != 0) {
28:         ++result;
29:     }
30: }
31: 

plugins\token\MerkleDistributor.sol
110:   function isClaimed(uint256 _index) public view override returns (bool) {
111:         uint256 claimedWord_index = _index / 256;
112:         uint256 claimedBit_index = _index % 256;
113:         uint256 claimedWord = claimedBitMap[claimedWord_index];
114:         uint256 mask = (1 << claimedBit_index);
115:         return claimedWord & mask == mask;
116:     }
118:     /// @notice Sets an index in the merkle tree to be claimed.
119:     /// @param _index The index in the balance tree to be claimed.
120:     function _setClaimed(uint256 _index) private {
121:         uint256 claimedWord_index = _index / 256;
122:         uint256 claimedBit_index = _index % 256;
123:         claimedBitMap[claimedWord_index] =
124:             claimedBitMap[claimedWord_index] |
125:             (1 << claimedBit_index);
126:     }


plugins\utils\Ratio.sol
17: function _applyRatioCeiled(uint256 _value, uint256 _ratio) pure returns (uint256 result) {
18:     if (_ratio > RATIO_BASE) {
19:         revert RatioOutOfBounds({limit: RATIO_BASE, actual: _ratio});
20:     }
21: 
22:     _value = _value * _ratio;
23:     uint256 remainder = _value % RATIO_BASE;
24:     result = _value / RATIO_BASE;
25: 
26:     // Check if ceiling is needed
27:     if (remainder != 0) {
28:         ++result;
29:     }
30: }
```

### [N-01] NatSpec comments should be increased in contracts
Context: All Contracts

Description: It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.17/natspec-format.html

Recommendation: NatSpec comments should be increased in contracts

### [N-02] Function writing that does not comply with the Solidity Style Guide

Context: All Contracts

Description: Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

constructor receive function (if exists) fallback function (if exists) external public internal private within a grouping, place the view and pure functions last


### [N-03] Include return parameters in NatSpec comments

Context: All Contracts

Description: It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

## Recommendation
Include return parameters in NatSpec comments

``` diff
+/// @return add comment for return parameters
plugins\token\MerkleDistributor.sol
82:   /// @inheritdoc IMerkleDistributor
83:     function unclaimedBalance(
84:         uint256 _index,
85:         address _to,
86:         uint256 _amount,
87:         bytes32[] memory _proof
88:     ) public view override returns (uint256) {
89:         if (isClaimed(_index)) return 0;
90:         return _verifyBalanceOnTree(_index, _to, _amount, _proof) ? _amount : 0;
91:     }
```

### [N-04] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.left Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

``` solidity
core\dao\DAO.sol
127:         return
128:             _permissionId == EXECUTE_PERMISSION_ID ||
129:             _permissionId == UPGRADE_DAO_PERMISSION_ID ||
130:             _permissionId == SET_METADATA_PERMISSION_ID ||
131:             _permissionId == SET_TRUSTED_FORWARDER_PERMISSION_ID ||
132:             _permissionId == SET_SIGNATURE_VALIDATOR_PERMISSION_ID ||
133:             _permissionId == REGISTER_STANDARD_CALLBACK_PERMISSION_ID;

  /// @notice The ID of the permission required to call the `execute` function.
    bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");

    /// @notice The ID of the permission required to call the `_authorizeUpgrade` function.
    bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");

    /// @notice The ID of the permission required to call the `setMetadata` function.
    bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");

    /// @notice The ID of the permission required to call the `setTrustedForwarder` function.
    bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
        keccak256("SET_TRUSTED_FORWARDER_PERMISSION");

    /// @notice The ID of the permission required to call the `setSignatureValidator` function.
    bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
        keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");

    /// @notice The ID of the permission required to call the `registerStandardCallback` function.
    bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
        keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");

    /// @notice The internal constant storing the maximal action array length.
    uint256 internal constant MAX_ACTIONS = 256;

PermissionLib.sol
10:     address public constant NO_CONDITION = address(0);
PermissionManager.sol
15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");
PluginUUPSUpgradeable.sol
35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");
framework\dao\DAORegistry.sol
15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");
framework\plugin\repo\PluginRepo.sol
48:     /// @notice The ID of the permission required to call the `createVersion` function.
49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
50: 
51:     /// @notice The ID of the permission required to call the `createVersion` function.
52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");
framework\plugin\repo\PluginRepoRegistry.sol
16:     bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
17:         keccak256("REGISTER_PLUGIN_REPO_PERMISSION");
framework\plugin\setup\PluginSetupProcessor.sol
26:     /// @notice The ID of the permission required to call the `applyInstallation` function.
27:     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
28:         keccak256("APPLY_INSTALLATION_PERMISSION");
29: 
30:     /// @notice The ID of the permission required to call the `applyUpdate` function.
31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");
32: 
33:     /// @notice The ID of the permission required to call the `applyUninstallation` function.
34:     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
35:         keccak256("APPLY_UNINSTALLATION_PERMISSION");
framework\utils\InterfaceBasedRegistry.sol
18:     bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRY_PERMISSION");
framework\utils\ens\ENSSubdomainRegistrar.sol
17:     /// @notice The ID of the permission required to call the `_authorizeUpgrade` function.
18:     bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
19:         keccak256("UPGRADE_REGISTRAR_PERMISSION");
20: 
21:     /// @notice The ID of the permission required to call the `registerSubnode` and `setDefaultResolver` function.
22:     bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
23:         keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");
24: 
plugins\counter-example\MultiplyHelper.sol
12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
plugins\counter-example\v1\CounterV1.sol
14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
plugins\counter-example\v2\CounterV2.sol
14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");
plugins\governance\admin\Admin.sol
23:     bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
24:         keccak256("EXECUTE_PROPOSAL_PERMISSION");
plugins\governance\majority-voting\MajorityVotingBase.sol
183:     bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
184:         keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");
plugins\governance\majority-voting\addresslist\AddresslistVoting.sol
27:     bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
28:         keccak256("UPDATE_ADDRESSES_PERMISSION");
plugins\governance\multisig\Multisig.sol
71:     bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
72:         keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");
plugins\token\MerkleMinter.sol
23:     /// @notice The ID of the permission required to call the `merkleMint` function.
24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");
25: 
26:     /// @notice The ID of the permission required to call the `changeDistributor` function.
27:     bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
28:         keccak256("CHANGE_DISTRIBUTOR_PERMISSION");
token\ERC20\governance\GovernanceERC20.sol
28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```