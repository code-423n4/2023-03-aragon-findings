## 

### [G-1] State variables only set in the constructor should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

While strings are not value types, and therefore cannot be immutable/constant if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract abstract with virtual functions for the string accessors, and having a child contract override the functions with the hard-coded implementation-specific values

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

    43: votingToken = _token;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L43)

##

### [G-2] MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

    55: mapping(uint8 => uint16) internal buildsPerRelease;

    /// @notice The mapping between the version hash and the corresponding version information.
    58: mapping(bytes32 => Version) internal versions;

    /// @notice The mapping between the plugin setup address and its corresponding version hash.
    61: mapping(address => bytes32) internal latestTagHashForPluginSetup;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55-L61)


### [G-1] Optimize names to save gas

public/external function names and public member variable names can be optimized to save gas. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per [sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

FILE :2023-03-aragon/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

public/external functions

   @Audit initialize(), registerSubnode() setDefaultResolver()
   16: contract ENSSubdomainRegistrar is UUPSUpgradeable, DaoAuthorizableUpgradeable {

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol

   @audit prepareInstallation() ,implementation(), prepareUninstallation()
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

      /// @audit initialize(), addAddresses(),removeAddresses(),updateMultisigSettings(),createProposal(),approve(),canApprove(),canExecute(),getProposal(),hasApproved(),execute(),isMember(),
     18 : contract Multisig is

 

##  

### [G-2]  ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

FILE : 2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol

   16: for (uint256 i; i < nameLength; i++) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L16)

FILE : 2023-03-aragon/packages/contracts/src/framework/dao/DAOFactory.sol

   95: for (uint256 i; i < _pluginSettings.length; ++i) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L95)

##

### [GAS-3] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

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

##

### [G-4] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS

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

##

### [G-5] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

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

### [G-6] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

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

### [G-7] INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

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



##

### [G-8] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT

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


2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

    71: bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
        keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71-L72)

##


### [G-9] Use nested if and, avoid multiple check combinations

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
   


##

### [G-10] Caching _msgSender() function instead of recalling multiple times to saves the gas

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

### [G-11] SETTING THE CONSTRUCTOR TO PAYABLE

Saves ~13 gas per instance

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

##

### [G-12] Use assembly to write address storage values

FILE : 2023-03-aragon/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol

   73:  tokenVotingBase = new TokenVoting();

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L73)

##

### [G-13] Use constants instead of type(uintx).max

type(uint16).max . it uses more gas in the distribution process and also for each transaction than constant usage.


2023-03-aragon/packages/contracts/src/plugins/governance/multisig/Multisig.sol

   159 :  if (newAddresslistLength > type(uint16).max) {

   161 :  limit: type(uint16).max,

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L159-L161)

##

### [G-14] USE ++X/--X when need to increase or decrease value by 1 instead of X+=1 or X-=1. 

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

### [15] ]  DUPLICATED REQUIRE()/REVERT()/IF CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

FILE : FILE : 2023-03-aragon/packages/contracts/src/framework/plugin/repo/PluginRepo.sol

     138: if (_release == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L138)

   191: if (_release == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L191)

   150 : if (_releaseMetadata.length == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L150)

    199: if (_releaseMetadata.length == 0) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L199)










GAS-1	Use assembly to check for address(0)	22
GAS-2	array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants)	1
GAS-3	Using bools for storage incurs overhead	2
GAS-4	Cache array length outside of loop	10
GAS-5	State variables should be cached in stack variables rather than re-reading them from storage	1
GAS-6	Use calldata instead of memory for function arguments that do not get mutated	20
GAS-7	Don't initialize variables with default value	1
GAS-8	Functions guaranteed to revert when called by normal users can be marked payable	4
GAS-9	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	1
GAS-10	Using private rather than public for constants, saves gas	29
GAS-11	Use shift Right/Left instead of division/multiplication if possible	2
GAS-12	Use != 0 instead of > 0 for unsigned integer comparison	9







