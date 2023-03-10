## 

### [G-1] State variables only set in the constructor should be declared immutable

Instances (2):

Approximate Gas Saved : 40000 gas 

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

    43: votingToken = _token;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L43)

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

    278 : repoRegistry = _repoRegistry;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L278)

##

### [G-2] MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

Instances (3):

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

    55: mapping(uint8 => uint16) internal buildsPerRelease;

    /// @notice The mapping between the version hash and the corresponding version information.
    58: mapping(bytes32 => Version) internal versions;

    /// @notice The mapping between the plugin setup address and its corresponding version hash.
    61: mapping(address => bytes32) internal latestTagHashForPluginSetup;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L61)

##

### [G-3] Optimize names to save gas

public/external function names and public member variable names can be optimized to save gas. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per [sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

public/external functions

     /// @Audit initialize(), registerSubnode() setDefaultResolver()
     16: contract ENSSubdomainRegistrar is UUPSUpgradeable, DaoAuthorizableUpgradeable {

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

     /// @audit prepareInstallation() ,implementation(), prepareUninstallation()
     15 : contract AddresslistVotingSetup is PluginSetup {

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

    /// @audit initialize(), addAddresses(), removeAddresses() , isMember()
    19 :contract AddresslistVoting is IMembership, Addresslist, MajorityVotingBase {

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

     /// @audit initialize(), getVotingToken(),isMember()
     18: contract TokenVoting is IMembership, MajorityVotingBase {

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

     /// @audit prepareInstallation(),prepareUninstallation(),
     24: contract TokenVotingSetup is PluginSetup {

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

      /// @audit initialize(), 
    addAddresses(),removeAddresses(),updateMultisigSettings(),createProposal(),approve(),canApprove(),canExecute(),getProposal(),hasApproved(),execute(),isMember(),
     18 : contract Multisig is

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

   
     /// @audit  prepareInstallation(), applyInstallation(),prepareUpdate(),applyUpdate(),prepareUninstallation(),applyUninstallation(),validatePreparedSetupId(),
     23 : contract PluginSetupProcessor {

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

    /// @audit  initialize() , hasPermission() , registerStandardCallback(), daoURI(), setDaoURI()
    26 : contract DAO is

##  

### [G-4]  ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

Instances (3):

Approximate Gas Saved : 120 gas 

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

FILE : 2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol

     16: for (uint256 i; i < nameLength; i++) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L16)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

     95: for (uint256 i; i < _pluginSettings.length; ++i) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L95)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

     95: for (uint256 i; i < _pluginSettings.length; ++i) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L95)

##

### [GAS-5] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

Using immutable for constant expressions such as a call to keccak256() means that the expression is evaluated at deployment time and the result is stored in the contract's storage. This results in a smaller bytecode size and lower deployment costs, as the compiler does not have to evaluate the expression every time the contract is compiled

When a constant expression is evaluated at runtime, it takes up more gas than when the same expression is evaluated at deployment time and the result is simply read from the contract's storage

using immutable for constant expressions such as a call to keccak256() can save gas and reduce the deployment costs of your smart contract

FILE : 2023-03-aragon/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

   18 :  bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
        keccak256("UPGRADE_REGISTRY_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18-L19)

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol
   
    18 : bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
        keccak256("UPGRADE_REGISTRAR_PERMISSION");

   22: bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
        keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18-L23)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

    183: bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
        keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183-L184)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

   27:  bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
        keccak256("UPDATE_ADDRESSES_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27-L28)

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49-L52)

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
        keccak256("APPLY_INSTALLATION_PERMISSION");

    /// @notice The ID of the permission required to call the `applyUpdate` function.
    bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

    /// @notice The ID of the permission required to call the `applyUninstallation` function.
    bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
        keccak256("APPLY_UNINSTALLATION_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27-L35)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

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

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L40-L58)


##

### [G-6] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS

Instances (3):

Approximate Gas Saved : 27 gas 

In Solidity, declaring a variable inside a loop can result in higher gas costs compared to declaring it outside the loop. This is because every time the loop runs, a new instance of the variable is created, which can lead to unnecessary memory allocation and increased gas costs

On the other hand, if you declare a variable outside the loop, the variable is only initialized once, and you can reuse the same instance of the variable inside the loop. This approach can save gas and optimize the execution of your code

GAS SAMPLE TEST IN remix ide(Without gas optimizations) :

Before Mitigation :

     function testGas() public view {

        for(uint256 i = 0; i < 10; i++) {
          address collateral = msg.sender;
          address  collateral1 = msg.sender;
            
        }

The execution Cost : 2176 

After Mitigation :

       function testGas() public view {
      address collateral; address  collateral1;
        for(uint256 i = 0; i < 10; i++) {
         collateral = msg.sender;
         collateral1 = msg.sender;
            
        }

The execution Cost : 2086 . 

>  Hence clearly after mitigation the gas cost is reduced. Approximately its possible to save 9 gas for every iterations 

File : 2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol

      16:  for (uint256 i; i < nameLength; i++) {
      17:  uint8 char = uint8(nameBytes[i]);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L16-L17)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

      for (uint256 i = 0; i < _actions.length; ) {
            address to = _actions[i].to;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L184-L185)

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

         for (uint256 i; i < items.length; ) {
            PermissionLib.SingleTargetPermission memory item = items[i];

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L150-L151)

##

### [G-7] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

Instances (4):

Approximate Gas Saved : 112 gas 

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size

(https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)

Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

File : 2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol

      18: uint8 char = uint8(nameBytes[i]);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L17)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

      568 : uint64 currentTimestamp = block.timestamp.toUint64();

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L568)

      580 : uint64 earliestEndDate = startDate + votingSettings.minDuration; 

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L580)

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

     65 : uint8 public latestRelease;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L65)


##

### [G-8] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

Instances (1):

Approximate Gas Saved : 15 gas 

External functions do not require a context switch to the EVM, while public functions do.

Its possible to save 10-15 gas using external instead public for every function call


FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

        function vote(
        uint256 _proposalId,
        VoteOption _voteOption,
        bool _tryEarlyExecution
       ) public virtual {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L265-L269)

##

### [G-9] INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Instances (5):

Approximate Gas Saved : 200 gas 

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

       function _vote(
        uint256 _proposalId,
        VoteOption _voteOption,
        address _voter,
        bool _tryEarlyExecution
        ) internal virtual;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L448-L453)

      457: function _execute(uint256 _proposalId) internal virtual {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L457)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

      141: function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L141)

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

      156: function _setDAOPermissions(DAO _dao) internal {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L156)



     699 : function _canApply(address _dao, bytes32 _permissionId) private view {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L699)

##

### [G-10] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT

FILE : 2023-03-aragon/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

     18 :  bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
        keccak256("UPGRADE_REGISTRY_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18-L19)

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol
   
      18 : bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
        keccak256("UPGRADE_REGISTRAR_PERMISSION");

     22: bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
        keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18-L23)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

      183: bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
        keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L183-L184)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

     27:  bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
        keccak256("UPDATE_ADDRESSES_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27-L28)

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49-L52)

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
        keccak256("APPLY_INSTALLATION_PERMISSION");

    /// @notice The ID of the permission required to call the `applyUpdate` function.
    bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

    /// @notice The ID of the permission required to call the `applyUninstallation` function.
    bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
        keccak256("APPLY_UNINSTALLATION_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27-L35)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

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

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L40-L58)


##


### [G-11] Use nested if and, avoid multiple check combinations

Per && split its possible to save 30 gas 

Instances (9):

Approximate Gas Saved : 270 gas 

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

       97 : if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97)
 
      185: if (_tryEarlyExecution && _canExecute(_proposalId)) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L185)

         if (
            proposal_.voters[_account] != VoteOption.None &&
            proposal_.parameters.votingMode != VotingMode.VoteReplacement
        ) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L215-L217)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

             if (
                // If token supports none of them
                // it's simply ERC20 which gets checked by _isERC20
                // Currently, not a satisfiable check.
                (!supportedIds[0] && !supportedIds[1] && !supportedIds[2]) ||
                // If token supports IERC20, but neither
                // IVotes nor IGovernanceWrappedERC20, it needs wrapping.
                (supportedIds[0] && !supportedIds[1] && !supportedIds[2])
            ) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L110-L118)


FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

     216: if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

      283:    if (_tryExecution && _canExecute(_proposalId)) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L283)

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

      157: if (version.tag.release != 0 && version.tag.release != _release) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L157)

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

           if (
            msg.sender != _dao &&
            !DAO(payable(_dao)).hasPermission(address(this), msg.sender, _permissionId, bytes(""))
           ) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L700-L703)

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

     236 : if (_where == ANY_ADDR && _who == ANY_ADDR) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L236)

   
##

### [G-12] Caching _msgSender() function instead of recalling multiple times to saves the gas

When you call _msgSender() multiple times within a function or contract, Solidity generates multiple CALLER opcodes in the compiled bytecode, which results in more gas consumption. However, if you cache the result of _msgSender() in a local variable, you can avoid these extra opcodes and reduce the gas consumption

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

_msgSender() should be cached instead of calling multiple times 

        if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) { /// audit 
       _msgSender()
            revert ProposalCreationForbidden(_msgSender());  /// audit _msgSender()
        }

         proposalId = _createProposal({
        _creator: _msgSender(),  /// audit _msgSender()

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97-L102)

##

### [G-13] SETTING THE CONSTRUCTOR TO PAYABLE

Saves ~13 gas per instance

CONTEXT :

ALL CONTRACTS (55)

Instances (4):

Approximate Gas Saved : 715 gas 

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

      20 : constructor() {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L20)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol
 
      61 : constructor() {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

      53 : constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L53)

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

      112 : constructor() {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L112)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol
 
      53 : constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L53)

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

     277:  constructor(PluginRepoRegistry _repoRegistry) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

     92 :  constructor() {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L92)

##

### [G-14] Use assembly to write address storage values

Instances (5):

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

     73:  tokenVotingBase = new TokenVoting();

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L73)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

       constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
        daoRegistry = _registry;
        pluginSetupProcessor = _pluginSetupProcessor;

        daoBase = address(new DAO());
       }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L53-L58)

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

       278 : repoRegistry = _repoRegistry;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L278)

##

### [G-15] Use constants instead of type(uintx).max

Instances (3):

type(uint16).max,type(uint160).max) . it uses more gas in the distribution process and also for each transaction than constant usage.


2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

     159 :  if (newAddresslistLength > type(uint16).max) {

     161 :  limit: type(uint16).max,

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L159-L161)

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

     18 :  address internal constant ANY_ADDR = address(type(uint160).max);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L18)

##

### [G-16] USE ++X/--X when need to increase or decrease value by 1 instead of X+=1 or X-=1. 

Instances (1):

Approximate Gas Saved : 126 gas 

++X and X+=1 the final out put is same . When we using ++X instead of X+=1 possible to save 126 gas. The gas value is more when applying this to state variables.

Proof of Concept n remix :

pragma solidity ^0.8.18;
contract EtherGame {    
// Execution Cost 580    
function test1() public pure returns(int){
int x;
x+=1;
return x;
    }

    // Execution Cost  454   
    function test2() public pure returns (int){
     int x;
    ++x;
    return x;
    }

    }

So as per poc possible to reduce 126 gas every time.

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

     276:  proposal_.approvals += 1;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276)

##

### [17]  DUPLICATED REQUIRE()/REVERT()/IF CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

Instances (4):

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

       138: if (_release == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L138)

     191: if (_release == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L191)

     150 : if (_releaseMetadata.length == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L150)

      199: if (_releaseMetadata.length == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L199)

##

### [18] Use Functions instead of modifiers 

Instances (2):

FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol
 

       modifier canApply(address _dao, bytes32 _permissionId) {
        _canApply(_dao, _permissionId);
        _;
      }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L270-L273)

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

     modifier auth(bytes32 _permissionId) {
        _auth(_permissionId);
        _;
     }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L87-L90)

##

### [19] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

Instances (2):

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

     function prepareInstallation(
        address _dao,
        PrepareInstallationParams calldata _params
     ) external returns (address plugin, IPluginSetup.PreparedSetupData memory preparedSetupData) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L286-L289)

       function prepareUpdate(
        address _dao,
        PrepareUpdateParams calldata _params
      )
        external
        returns (bytes memory initData, IPluginSetup.PreparedSetupData memory preparedSetupData)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L396-L401)

##

### [20] Removing empty blocks can saves the over all deployment cost 

Instances (1):

When you compile your contract, the Solidity compiler needs to parse and analyze every line of code. Removing empty blocks can help to reduce the compilation time, which can save you time and potentially reduce the cost of your development process

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

     136 : function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L136)

File: packages/contracts/src/core/dao/DAO.sol

     136: function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}


File: packages/contracts/src/core/permission/PermissionManager.sol

       320:  } catch {}


File: packages/contracts/src/core/plugin/Plugin.sol

      17: constructor(IDAO _dao) DaoAuthorizable(_dao) {}


File: packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol

      63: ) internal virtual override auth(UPGRADE_PLUGIN_PERMISSION_ID) {}


File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol

      259: ) internal virtual override auth(UPGRADE_REPO_PERMISSION_ID) {}


File: packages/contracts/src/framework/plugin/setup/PluginSetup.sol

       27: {}


File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

      672: {} catch Error(string memory reason) {

     682: try PluginUUPSUpgradeable(_proxy).upgradeTo(_implementation) {} catch Error(


File: packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

     56: ) internal virtual override auth(UPGRADE_REGISTRY_PERMISSION_ID) {}


File: packages/contracts/src/framework/utils/ens/ENSMigration.sol

      10: contract Dummy {}


File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

     76: ) internal virtual override auth(UPGRADE_REGISTRAR_PERMISSION_ID) {}


##

### [G-21] Internal functions not called by contract can be removed 

internal functions that are not called by the contract can be safely removed without affecting the functionality of the contract. Removing these functions can help to reduce the size of the contract, which can result in lower gas costs

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

      function isPermissionRestrictedForAnyAddr(
        bytes32 _permissionId
      ) internal pure override returns (bool) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L122)

##

### [22] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

Instances (11):

Approximate Gas Saved : 23000 gas 

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

      151 : PermissionLib.SingleTargetPermission memory item = items[i];

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L151)

FILE : 2023-03-aragon/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

      PermissionLib.MultiTargetPermission[]
            memory permissions = new PermissionLib.MultiTargetPermission[](
                _multiplyHelper == address(0) ? 3 : 2
            );
        address[] memory helpers = new address[](1);
  
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L55-L59)

       PermissionLib.MultiTargetPermission[]
            memory permissions = new PermissionLib.MultiTargetPermission[](1);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L121-L122)

     132: address[] memory activeHelpers = new address[](1);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L132)

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

     151 : PermissionLib.SingleTargetPermission memory item = items[i];

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L151)

     171 :  PermissionLib.MultiTargetPermission memory item = _items[i];
 
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L171)

 FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

     96: address[] memory helpers = new address[](1);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L96)


      PermissionLib.MultiTargetPermission[]
            memory permissions = new PermissionLib.MultiTargetPermission[](
                tokenSettings.addr != address(0) ? 3 : 4
            );

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L149-L152)

     266: bytes4[] memory interfaceIds = new bytes4[](3);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L266)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

     45:   PermissionLib.MultiTargetPermission[]
            memory permissions = new PermissionLib.MultiTargetPermission[](4);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L45-L46)

##

### [23] ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT . This saves 200-210 gas as per remix 

Instances (1):

Approximate Gas Saved : 210 gas 

The unchecked keyword was introduced in Solidity version 0.8.0. 

PROOF OF CONCEPT (remix without optimizations):

pragma solidity ^0.8.7;
contract MappingTest {

// Execution Cost 696
function Subtraction() public pure {
int a=20; int b=10; int c=10; int d=30;
    if(a>b){
        a=a-b;
    }
    if(c<d){
        c=d-c;
    }
   
}
//Execution Cost 276
function uncheckedSubtraction() public pure {
int a=20; int b=10; int c=10; int d=30;
    if(a>b){
        unchecked{a=a-b;}
        
    }
    if(c<d){
        unchecked{c=d-c;}
    }
   
}


}

So clearly for each unchecked possible to save 210 gas 

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

There is no possibility to overflow. We just reduce block.number value by 1 

     214:  uint64 snapshotBlock = block.number.toUint64() - 1;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L214)


##

### [G-24] Avoid contract existence checks by using low level calls

Instances (2):

Approximate Gas Saved : 200 gas 

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

     232:   IERC20Upgradeable(_token).safeTransferFrom(msg.sender, address(this), _amount);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L232)

     187: (bool success, bytes memory response) = to.call{value: _actions[i].value}( //@audit Call 
                _actions[i].data
            );

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L186-L188)

##

### [G-25] Use more recent version of solidity 

Context:
ALL CONTRACTS

All contract using 0.8.17

Latest solidity version is 0.8.19

Benefits for using 0.8.19 instead of 0.8.17

> Language Features

Allow defining custom operators for user-defined value types via using {f as +} for T global syntax.

> Compiler Features

SMTChecker:

New trusted mode that assumes that any compile-time available code is the actual used code, even in external calls. This can be used via the CLI option --model-checker-ext-calls trusted or the JSON field settings.modelChecker.extCalls: "trusted".

> Bugfixes

Assembler: 

Avoid duplicating subassembly bytecode where possible.
Code Generator: Avoid including references to the deployed label of referenced functions if they are called right away.

ContractLevelChecker: 

Properly distinguish the case of missing base constructor arguments from having an unimplemented base function.

SMTChecker: 

Fix internal error caused by unhandled z3 expressions that come from the solver when bitwise operators are used.

SMTChecker: 

Fix internal error when using the custom NatSpec annotation to abstract free functions.

TypeChecker: Also allow external library functions in using for.

   

   
 























