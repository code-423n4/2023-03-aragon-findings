## GAS-1: ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow

### Description

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

### Affected file

* DAOFactory.sol (Line: 95)


## GAS-2: <X> <= <Y> costs more gas than <X> < <Y> + 1

### Description

In Solididy, the opcode 'less or equal' doesn't exist. So the EVM will translate this by two distinct operation, first the inferior, and then the equal which cost more gas then a strict less.

### Affected file

* Multisig.sol (Line: 397)
* PluginSetupProcessor.sol (Line: 655)

## GAS-3: Empty blocks should be removed or emit something

### Description

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### Affected file

* DAO.sol (Line: 136)
* ENSSubdomainRegistrar.sol (Line: 74)
* PluginRepo.sol (Line: 257)

## GAS-4: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

### Affected file

* AddresslistVoting.sol (Line: 148, 191)
* DAO.sol (Line: 122, 136)
* ENSSubdomainRegistrar.sol (Line: 74)
* PluginRepo.sol (Line: 257)
* TokenVoting.sol (Line: 143, 188)

## GAS-5: Internal functions only called once can be inlined to save gas

### Description

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

### Affected file

* DAOFactory.sol (Line: 141, 156)
* GovernanceERC20.sol (Line: 112)
* GovernanceWrappedERC20.sol (Line: 98, 112, 120)
* PluginRepoFactory.sol (Line: 65)

## GAS-6: Not using the named return variables when a function returns, wastes deployment gas

### Description

It is not necessary to have both a named return and a return statement.

### Affected file

* CounterV1PluginSetup.sol (Line: 37)
* CounterV2PluginSetup.sol (Line: 38)
* MerkleMinter.sol (Line: 79)
* PluginSetupProcessor.sol (Line: 289, 402)

## GAS-7: Public functions not called by the contract should be declared external instead

### Description

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

### Affected file

* CounterV1.sol (Line: 44)
* CounterV2.sol (Line: 63)
* MerkleDistributor.sol (Line: 83)
* Multisig.sol (Line: 305, 328)
* PluginRepo.sol (Line: 209, 217, 244)
* TokenVoting.sol (Line: 61)

## GAS-8: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* PluginSetupProcessor.sol (Line: 270)

## GAS-9: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas.

### Affected file

* DAO.sol (Line: 272)