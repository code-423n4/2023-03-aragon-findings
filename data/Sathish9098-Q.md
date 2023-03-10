##
###[1] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

it's generally considered best practice to use named imports instead of plain import statements

With named imports, you can selectively import only the specific functions or variables that you need from a given file. This can help reduce the amount of unnecessary code that you have to load into memory when deploying or executing your smart contract

FILE : 2023-03-aragon/packages/contracts/src/utils/Proxy.sol

    5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/utils/Proxy.sol#L5)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L185)

##
### [2] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

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

##
### [3] MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES

In Solidity, assigning a value of address(0x0) (also known as address(0)) to an address state variable effectively sets the variable to an uninitialized state. This can lead to unexpected behavior in your smart contract and even vulnerabilities

FILE : 2023-03-aragon/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol

      function __InterfaceBasedRegistry_init(
        IDAO _managingDao,
        bytes4 _targetInterfaceId
      ) internal virtual onlyInitializing {
        __DaoAuthorizableUpgradeable_init(_managingDao);

        targetInterfaceId = _targetInterfaceId;
      }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L43-L50)

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

        function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {
        __DaoAuthorizableUpgradeable_init(_managingDao);

        ens = _ens;
        node = _node;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L57-L70)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

    function __MajorityVotingBase_init(
        IDAO _dao,
        VotingSettings calldata _votingSettings
    ) internal onlyInitializing {
        __PluginUUPSUpgradeable_init(_dao);
        _updateVotingSettings(_votingSettings);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L238-L244)

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

    function initialize(
        IDAO _dao,
        VotingSettings calldata _votingSettings,
        IVotesUpgradeable _token
    ) external initializer {
        __MajorityVotingBase_init(_dao, _votingSettings);

        votingToken = _token;

        emit MembershipContractAnnounced({definingContract: address(_token)});
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L36-L46)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

      constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
        daoRegistry = _registry;
        pluginSetupProcessor = _pluginSetupProcessor;

        daoBase = address(new DAO());
      }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L53-L58)

FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

     function initialize(address initialOwner) external initializer {
        __PermissionManager_init(initialOwner);

        _grant(address(this), initialOwner, MAINTAINER_PERMISSION_ID);
        _grant(address(this), initialOwner, UPGRADE_REPO_PERMISSION_ID);
    }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L120-L125)

Recommended Mitigation steps :

Check address(0) before assigning address values to state variables 

##

### [4] OMISSIONS IN EVENTS

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters. 

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

Use event in initialize function

     function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {
        __DaoAuthorizableUpgradeable_init(_managingDao);

        ens = _ens;
        node = _node;

        address nodeResolver = ens.resolver(_node);

        if (nodeResolver == address(0)) {
            revert InvalidResolver({node: _node, resolver: nodeResolver});
        }

        resolver = nodeResolver;
     }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L57-L70)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

      constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {
        daoRegistry = _registry;
        pluginSetupProcessor = _pluginSetupProcessor;

        daoBase = address(new DAO());
      }

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L53-L58)

##

### [5] LINES ARE TOO LONG

 Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L72)

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L110)

##

### [6] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

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

##

### [8] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

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

##

### [9] immutable variables should be write in capital letters

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L30-L36)

##

### [10] Use OpenZeppelin safeCast library instead of normal casting 

he SafeCast library provides a set of functions for safely casting between integer types in Solidity. These functions perform various checks to prevent integer overflows and underflows that can occur when performing type conversions

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

   175:   uint16 newAddresslistLength = uint16(addresslistLength() - _members.length);

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L175)
 
   414:  uint16 addresslistLength_ = uint16(addresslistLength());

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L414)

##

### [11] Upgradeable contract is missing a __gap[50] storage variable

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







NC-1	Missing checks for address(0) when assigning values to address state variables	1
NC-2	Return values of approve() not checked	1
NC-3	Event is missing indexed fields	26
NC-4	Functions not used internally could be marked external	22
NC-5	Typos	167

L-1	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	4
L-2	Empty Function Body - Consider commenting why	11
L-3	Initializers could be front-run	71




