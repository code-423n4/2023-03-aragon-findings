# 1: FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGE

Vulnerability details

## Context:

For modern and more readable code; update import usage

## Proof of Concept

> ***File ENSMigration.sol***

https://github.com/jauvany/2023-03-aragon/blob/4a2618ed1d3583379c29340bd99034bd277d012d/packages/contracts/src/framework/utils/ens/ENSMigration.sol#L7-L8 
> ***File ENSSubdomainRegistrar.sol*** 

https://github.com/jauvany/2023-03-aragon/blob/4a2618ed1d3583379c29340bd99034bd277d012d/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L5-L6-L8-L10-L11 

> ***File Proxy.sol***

https://github.com/jauvany/2023-03-aragon/blob/4a2618ed1d3583379c29340bd99034bd277d012d/packages/contracts/src/utils/Proxy.sol#L5 

> ***File DAO.sol***

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/core/dao/DAO.sol#L5-L14  

> ***File PermissionManager.sol*** 

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/core/permission/PermissionManager.sol#L5-L8  

> ***File ProposalUpgradeable.sol***

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L8   

> ***File Proposal.sol***

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/core/plugin/proposal/Proposal.sol#L8  

## Tools Used

Manual Analysis  
  
# 2: GENERATE PERFECT CODE HEADERS EVERY TIME					

Vulnerability details

## Context:

We recommend using a header for Solidity code layout and readability

For reference, see https://github.com/transmissions11/headers

## Proof of Concept

> ***All Contracts***

  /*//////////////////////////////////////////////////////////////
                           TESTING 123
 //////////////////////////////////////////////////////////////*/


## Tools Used

Manual Analysis

# 3: USE CONSTANTS FOR NUMBERS

Vulnerability details

## Context:

In a few locations in the code, numbers like 0x20  are used. The same goes for values like: type(uint16).max, and type(uint160).max It is quite easy to make a mistake somewhere, also when comparing values.

## Proof of Concept

#### 0x20 Numbers

> ***File src/framework/utils/TokenFactory.sol.sol***

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/framework/utils/TokenFactory.sol#L92 

> ***File src/plugins/governance/majority-voting/token/TokenVotingSetup.sol***

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L280 


#### .max Values

> ***src/plugins/governance/multisig/Multisig.sol***

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L159 

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L161  

> ***src/core/permission/PermissionManager.sol***

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/core/permission/PermissionManager.sol#L18 

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

We recommend defining constants for the above types of numbers used throughout the code.

# 4: ADD A LIMIT FOR THE MAXIMUM NUMBER OF CHARACTERS PER LINE					

Vulnerability details

## Impact:

The solidity documentation recommends a maximum of 120 characters.

For reference, see https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length

## Proof of Concept

3 INSTANCES 3 FILES

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L6 

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/framework/utils/TokenFactory.sol#L8 

https://github.com/jauvany/2023-03-aragon/blob/8d3f26196a772248e417fc66c6d995e805a54f25/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L5 



## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Consider adding a limit of 120 characters or less to prevent large lines.
