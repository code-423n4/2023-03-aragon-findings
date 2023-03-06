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

We recommend using header for Solidity code layout and readability

For reference, see https://github.com/transmissions11/headers

## Proof of Concept

> ***All Contracts***

  /*//////////////////////////////////////////////////////////////
                           TESTING 123
 //////////////////////////////////////////////////////////////*/


## Tools Used

Manual Analysis
