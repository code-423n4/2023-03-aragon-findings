##
###  [1] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

TYPE : NON CRITICAL 

it's generally considered best practice to use named imports instead of plain import statements

With named imports, you can selectively import only the specific functions or variables that you need from a given file. This can help reduce the amount of unnecessary code that you have to load into memory when deploying or executing your smart contract

FILE : 2023-03-aragon/packages/contracts/src/utils/Proxy.sol

     5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/utils/Proxy.sol#L5)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L185)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol


    import "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165StorageUpgradeable.sol";
   import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
   import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
   import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
   import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";
   import "@openzeppelin/contracts-upgradeable/token/ERC721/IERC721ReceiverUpgradeable.sol";
   import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155Upgradeable.sol";
   import "@openzeppelin/contracts-upgradeable/token/ERC1155/IERC1155ReceiverUpgradeable.sol";
   import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";
   import "@openzeppelin/contracts/interfaces/IERC1271.sol";

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L5-L14)

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

   import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

   import "./IPermissionCondition.sol";
    import "./PermissionLib.sol";

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L5-L8)

##
### [2] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

 TYPE : NON CRITICAL 

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

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
### [3] MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES

TYPE : LOW 

In Solidity, assigning a value of address(0x0) (also known as address(0)) to an address state variable effectively sets the variable to an uninitialized state. This can lead to unexpected behavior in your smart contract and even vulnerabilities

FILE : 2023-03-aragon/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

      function __InterfaceBasedRegistry_init(
        IDAO _managingDao,                   /// @audit _managingDao
        bytes4 _targetInterfaceId
      ) internal virtual onlyInitializing {
        __DaoAuthorizableUpgradeable_init(_managingDao);

        targetInterfaceId = _targetInterfaceId;
      }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L43-L50)

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

        function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {
        __DaoAuthorizableUpgradeable_init(_managingDao);  /// @audit _managingDao

        ens = _ens;  /// @audit _ens
        node = _node;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L57-L70)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

    function __MajorityVotingBase_init(
        IDAO _dao,
        VotingSettings calldata _votingSettings
    ) internal onlyInitializing {
        __PluginUUPSUpgradeable_init(_dao);    /// @audit _dao
        _updateVotingSettings(_votingSettings);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L238-L244)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

    function initialize(
        IDAO _dao,
        VotingSettings calldata _votingSettings,
        IVotesUpgradeable _token
    ) external initializer {
        __MajorityVotingBase_init(_dao, _votingSettings); /// @audit _dao

        votingToken = _token;   /// @audit _token

        emit MembershipContractAnnounced({definingContract: address(_token)});
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L36-L46)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

      constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
        daoRegistry = _registry;   /// @audit _registry
        pluginSetupProcessor = _pluginSetupProcessor;   /// @audit _pluginSetupProcessor

        daoBase = address(new DAO());
      }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L53-L58)

FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

     function initialize(address initialOwner) external initializer {
        __PermissionManager_init(initialOwner);  /// @audit initialOwner

        _grant(address(this), initialOwner, MAINTAINER_PERMISSION_ID);   /// @audit initialOwner
        _grant(address(this), initialOwner, UPGRADE_REPO_PERMISSION_ID);  /// @audit initialOwner
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L120-L125)


Recommended Mitigation steps :

Check address(0) before assigning address values to state variables 

##

##

### [5] LINES ARE TOO LONG

TYPE : NON CRITICAL 

 Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L649)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L26)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L53)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L59)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L64)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L85)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L192)

##

### [6] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

TYPE : NON CRITICAL 

  It is bad practice to use numbers directly in code without explanation

File : 2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol

   20 :  if (char > 96 && char < 123) {

   25:  if (char > 47 && char < 58) {

   30 :  if (char == 45) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L20)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L540)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L544)
  

##

### [7] Shorter inheritance list

TYPE : NON CRITICAL 

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

    abstract contract MajorityVotingBase is
    IMajorityVoting,
    Initializable,
    ERC165Upgradeable,
    PluginUUPSUpgradeable,
    ProposalUpgradeable

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L97-L102)

2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

     contract Multisig is
    IMultisig,
    IMembership,
    PluginUUPSUpgradeable,
    ProposalUpgradeable,
    Addresslist
    {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L18-L24)

FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol


    contract PluginRepo is
    Initializable,
    ERC165Upgradeable,
    IPluginRepo,
    UUPSUpgradeable,
    PermissionManager
    {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L19-L25)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

   contract DAO is
    IEIP4824,
    Initializable,
    IERC1271,
    ERC165StorageUpgradeable,
    IDAO,
    UUPSUpgradeable,
    PermissionManager,
    CallbackHandler
{

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L26-L35)

##

### [8] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

TYPE : NON CRITICAL 

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

       function getProposal(
        uint256 _proposalId
       )
        public
        view
        virtual
        returns (
            bool open,
            bool executed,
            ProposalParameters memory parameters,
            Tally memory tally,
            IDAO.Action[] memory actions,
            uint256 allowFailureMap
          )
       {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L392-L406)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L564-L567)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L77-L80)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L141)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L141)

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L286-L289

##

### [9] immutable variables should be written in capital letters

TYPE : NON CRITICAL 

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L30-L36)



##

### [10] Use OpenZeppelin safeCast library instead of normal casting method

TYPE : LOW 

he SafeCast library provides a set of functions for safely casting between integer types in Solidity. These functions perform various checks to prevent integer overflows and underflows that can occur when performing type conversions

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

   175:   uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L175)
 
   414:  uint16 addresslistLength_ = uint16(addresslistLength());

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L414)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

   192 :   if (!hasBit(_allowFailureMap, uint8(i))) {

   198 :  failureMap = flipBit(failureMap, uint8(i));

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L198)

##

### [11] Upgradeable contract is missing a __gap[50] storage variable

TYPE : LOW

Reference: [Storage_gaps](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)

You may notice that every contract includes a state variable named __gap. This is empty reserved space in storage that is put in place in Upgradeable contracts. It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments.

FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

    contract PluginRepo is
    Initializable,
    ERC165Upgradeable,
    IPluginRepo,
    UUPSUpgradeable,
    PermissionManager
{

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L19-L25)

Recommended Mitigation Steps

Consider adding a storage gap at the end of the upgradeable contract

uint256[50] private __gap;

##

### [12] FOR FUNCTIONS, FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

TYPE : NON CRITICAL 

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

  251 : function tagHash(Tag memory _tag) internal pure returns (bytes32) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L251)

https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L122-L124

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

    function emitPrepareUpdateEvent(
        address _dao,
        bytes32 _preparedSetupId,
        PrepareUpdateParams calldata _params,
        IPluginSetup.PreparedSetupData memory _preparedSetupData,
        bytes memory _initData
       ) private {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L719-L725)

##

### [13] Hardcode the address causes no future updates

TYPE : LOW

2023-03-aragon/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

        44:  bytes32 private constant ZERO_BYTES_HASH =
             0x290decd9548b62a8d60345a988386fc84ba6bc95484008f6362f93160ef3e563;

        39 :   bytes32 private constant EMPTY_ARRAY_HASH =
        0x569e75fc77c1a856f6daaf9e69d8a9566ca34aa47f9133711ce065a571af0cfd;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L39-L45) 

Recommended Mitigation Steps :

    So it is recommended to add the update option with the onlyOwner modifier

##

### [14] FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE

TYPE : NON CRITICAL 

Context: All Contracts

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

(https://docs.soliditylang.org/en/v0.8.17/style-guide.html)

Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

Functions should be grouped according to their visibility and ordered:

constructor receive function (if exists) fallback function (if exists) external public internal private within a grouping, place the view and pure functions last

##

### [15]  Use a single file for all system-wide constants

TYPE : NON CRITICAL 

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L40-L58)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49-L52)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27-L45)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L15-L24)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L23-L28)

##

### [16] Empty Receive method can remove safely. This may lock the ether 

TYPE : LOW

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

    receive() external payable {
        emit NativeTokenDeposited(msg.sender, msg.value);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L263-L265)

## 

### [17] GENERATE PERFECT CODE HEADERS EVERY TIME

TYPE : NON CRITICAL (NC)

Description
I recommend using header for Solidity code layout and readability

(https://github.com/transmissions11/headers)

##

### [18] Use of Block.timestamp

TYPE : LOW FINDING

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

   221: _startDate = block.timestamp.toUint64();

   222 : } else if (_startDate < block.timestamp.toUint64()) {
   
   223 : revert DateOutOfBounds({limit: block.timestamp.toUint64(), actual: _startDate});

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L221)
    
    404 :  uint64 currentTimestamp64 = block.timestamp.toUint64();

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L404)

##

## [19] Implement some type of version counter that will be incremented automatically for contract upgrades

TYPE : LOW 

I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

Recommendation:

uint256 public authorizeUpgradeCounter;

 function upgradeTo(address _newImplementation) external onlyOwner {
        _setPendingImplementation(_newImplementation);
       authorizeUpgradeCounter+=1;

    }

##

### [20] Include return parameters in NatSpec comments

TYPE : NON CRITICAL 

Description: It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with "/// @return".

Some code analysis programs do analysis by reading NatSpec details, if they can't see the "@return" tag, they do incomplete analysis


(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L60-L66)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L139-L141)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L262-L265)
(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L273-L276)

Recommendation Code Style:

   /// @notice information about what a function does
   /// @param pageId The id of the page to get the URI for.
   /// @return Returns a page's URI if it has been minted 

##

### [21]  Lack of event emission after critical initialize() function

TYPE : LOW 

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the initialize() functions

FILE : 2023-03-aragon/packages/contracts/src/core/permission/PermissionManager.sol

     function __PermissionManager_init(address _initialOwner) internal onlyInitializing {
        _initializePermissionManager(_initialOwner);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L95-L97)

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

   function initialize(
        bytes calldata _metadata,
        address _initialOwner,
        address _trustedForwarder,
        string calldata daoURI_
    ) external initializer {
        _registerInterface(type(IDAO).interfaceId);
        _registerInterface(type(IERC1271).interfaceId);
        _registerInterface(type(IEIP4824).interfaceId);
        _registerTokenInterfaces();

        _setMetadata(_metadata);
        _setTrustedForwarder(_trustedForwarder);
        _setDaoURI(daoURI_);
        __PermissionManager_init(_initialOwner);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L104-L119)

FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

   function initialize(address initialOwner) external initializer {
        __PermissionManager_init(initialOwner);

        _grant(address(this), initialOwner, MAINTAINER_PERMISSION_ID);
        _grant(address(this), initialOwner, UPGRADE_REPO_PERMISSION_ID);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L120-L125)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

    function __MajorityVotingBase_init(
        IDAO _dao,
        VotingSettings calldata _votingSettings
    ) internal onlyInitializing {
        __PluginUUPSUpgradeable_init(_dao);
        _updateVotingSettings(_votingSettings);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L238-L244)

##

### [22]  Wrong NatSpec tag added for allowFailureMap . Tag should be @return instead of @param

TYPE : NON CRITICAL 

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L298-L315)

##

### [23] EVENTS SHOULD EMIT OLD AND NEW VALUES WHEN ANY CRITICAL CHANGES HAPPEN 

TYPE : LOW

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

Now any changes daoURI_ only new daoURI_ is emitted 

     function _setDaoURI(string calldata daoURI_) internal {
        _daoURI = daoURI_;

        emit NewURI(daoURI_);
     }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L332-L336)

##

### [24] Add a timelock to critical function

TYPE : LOW 

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). Consider adding a timelock to.

When ever we change DAOuri need some times to react

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

Now any changes daoURI_ only new daoURI_ is emitted 

     function _setDaoURI(string calldata daoURI_) internal {
        _daoURI = daoURI_;

        emit NewURI(daoURI_);
     }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L332-L336)

##

### [25] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

TYPE : NON CRITICAL 

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

       function vote(
        uint256 _proposalId,
        VoteOption _voteOption,
        bool _tryEarlyExecution
       ) public virtual {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L265-L269)

##

### [26] USE A MORE RECENT VERSION OF SOLIDITY

TYPE : NON CRITICAL 

Context : 
ALL CONTRACTS

All contracts using 0.8.17

Recommanded Mitigation :

Use latest solidity version is 0.8.19 

##

### [27] NO SAME VALUE INPUT CONTROL

TYPE : NON CRITICAL 

FILE : 2023-03-aragon/packages/contracts/src/core/dao/DAO.sol

Now any changes daoURI_ only new daoURI_ is emitted 

     function _setDaoURI(string calldata daoURI_) internal {
        _daoURI = daoURI_;

        emit NewURI(daoURI_);
     }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L332-L336)

Recommended Mitigation :

 require(_daoURI != daoURI_, "Same value");

##

### [28] Use @openzeppelin/contracts and @openzeppelin/contracts-upgradeable latest version 

TYPE : LOW

latest openzeppelin version is 4.8.2 . But Protocol uses 0.8.1.

Recommended Mitigation:

 Consider latest openzeppelin version 4.8.2







  


  







  
   

   


















