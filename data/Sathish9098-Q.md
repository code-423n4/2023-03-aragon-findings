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

     71: entries[_registrant] = true;

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L61-L71)

Recommended Mitigation steps :

Check address(0) before assigning values to address state variables 

##

### [4] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

  It is bad practice to use numbers directly in code without explanation

File : 2023-03-aragon/packages/contracts/src/framework/utils/RegistryUtils.sol

   20 :  if (char > 96 && char < 123) {

   25:  if (char > 47 && char < 58) {

   30 :  if (char == 45) {

(https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L20)






NC-1	Missing checks for address(0) when assigning values to address state variables	1
NC-2	Return values of approve() not checked	1
NC-3	Event is missing indexed fields	26
NC-4	Functions not used internally could be marked external	22
NC-5	Typos	167

L-1	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	4
L-2	Empty Function Body - Consider commenting why	11
L-3	Initializers could be front-run	71




