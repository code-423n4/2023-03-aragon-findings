##
###[1] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

it's generally considered best practice to use named imports instead of plain import statements

With named imports, you can selectively import only the specific functions or variables that you need from a given file. This can help reduce the amount of unnecessary code that you have to load into memory when deploying or executing your smart contract

FILE : 2023-03-aragon/packages/contracts/src/utils/Proxy.sol

    5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/utils/Proxy.sol#L5)

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

Recommended Mitigation steps :

Check address(0) before assigning values to address state variables 

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




NC-1	Missing checks for address(0) when assigning values to address state variables	1
NC-2	Return values of approve() not checked	1
NC-3	Event is missing indexed fields	26
NC-4	Functions not used internally could be marked external	22
NC-5	Typos	167

L-1	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	4
L-2	Empty Function Body - Consider commenting why	11
L-3	Initializers could be front-run	71




