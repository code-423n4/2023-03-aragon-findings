[G-01]In all files use a more recent version of solidity
In 0.8.17 the conditions necessary for inlining are relaxed. 
Benchmarks show that the change significantly decreases the bytecode size 
(which impacts the deployment cost) while the effect on the runtime gas usage is smaller.
Latest pragma solidity is 0.8.19


[G-02] x = x - y; costs less gas than x -= y;, same for gathering You can replace all -= and += encounters to save gas.

File: packages/contracts/src/plugins/governance/multisig/Multisig.sol
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276


G-03]Using unchecked blocks to save gas - Increments in for loop can be unchecked ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0.
 These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256
 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let’s work with a sample loop below.

for(uint256 i; i < 10; i++){
//doSomething
}
can be written as shown below.

for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
File: packages/contracts/src/framework/dao/DAOFactory.sol
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L95

File: packages/contracts/src/framework/utils/RegistryUtils.sol
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L16