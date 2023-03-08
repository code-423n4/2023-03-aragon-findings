# 1. Potential Re-entrancy attack vulnerability in TokenFactory contract 

### Link : https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L78

### Summary: 

The `TokenFactory` contract creates governance tokens and may be vulnerable to re-entrancy attacks through the `createToken()` function, which can allow an attacker to re-enter the function and repeatedly execute malicious code. The vulnerability exists because the `MerkleMinter.mintFor()` function, which is called within the `createToken()` function, can call back into the `TokenFactory` contract and execute code within the context of the initial function call.

### Impact: 

An attacker could exploit this vulnerability to cause the function to become stuck in an infinite loop, drain tokens or Ether from the contract, or cause other unintended consequences. Additionally, if the `createToken()` function is called by another contract, the attacker could potentially exploit the vulnerability to take control of the calling contract.

### Recommendation:

To mitigate this vulnerability, the `createToken()` function should be modified to prevent re-entrancy attacks. One solution would be to use the OpenZeppelin `ReentrancyGuard` contract, which provides protection against re-entrancy attacks by using a mutex lock to prevent a function from being called again before the previous call has completed. Alternatively, the `MerkleMinter.mintFor()` function could be modified to prevent any re-entry into the `TokenFactory` contract, such as by using the OpenZeppelin `nonReentrant` modifier.
# 2. Incomplete validation of contract interface in `InterfaceBasedRegistry`

### Link : https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L37

### Summary: 

The` InterfaceBasedRegistry` contract has a vulnerability that can allow an attacker to register a contract that does not fully support the required ERC165 interface, by exploiting a missing check on the contract's ERC165 support. This can lead to issues with the reliability and security of the registry, as well as the contracts that depend on it.

### Impact: 

An attacker can register a contract with the `InterfaceBasedRegistry` contract without it having full support for the required ERC165 interface, as the code does not explicitly check for it. This can cause issues for contracts that depend on this registry, as they will not receive the expected functionality from the registered contract. This vulnerability can also result in false positives, where the registry indicates that a contract is supported when it actually is not.

### Recommendation: 

To mitigate this vulnerability, a check for full support of the required ERC165 interface should be added in the `_register` function. This can be done by adding a check for the ERC165 support of the contract before checking for the target interface support. If the contract does not support ERC165, it should revert with the `ContractERC165SupportInvalid` error. This will ensure that the registry only registers contracts that fully support the required ERC165 and target interface.

### Example: 

Here is an example of the modified `_register` function that includes the recommended check:

```
function _register(address _registrant) internal {
    if (entries[_registrant]) {
        revert ContractAlreadyRegistered({registrant: _registrant});
    }

    // Check for ERC165 support first
    if (!_registrant.supportsInterface(type(IERC165).interfaceId)) {
        revert ContractERC165SupportInvalid(_registrant);
    }

    // Check for target interface support
    if (!_registrant.supportsInterface(targetInterfaceId)) {
        revert ContractInterfaceInvalid(_registrant);
    }

    entries[_registrant] = true;
}
```
# 3. Potential vulnerability in `isSubdomainValid` function due to missing visibility and return type

### Link : https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/src/framework/utils/RegistryUtils.sol#L13

### Summary: 

The `isSubdomainValid` function in the provided code is missing a visibility modifier and a return type. The lack of a visibility modifier means that the function can be accessed by any contract, and the missing return type could result in unexpected behavior or errors. Additionally, the lack of input validation could lead to vulnerabilities.

### Impact: 

An attacker could call the `isSubdomainValid` function from a contract that has access to sensitive information or functions, potentially resulting in a loss of funds or unauthorized access. The lack of a return type could lead to errors in contract execution, while the lack of input validation could allow attackers to pass invalid or malicious inputs to the function.

### Recommendation: 

To address this issue, the function should have a visibility modifier and a return type specified. Additionally, input validation should be added to ensure that only valid inputs are accepted. For example, the function could be updated as follows:
```
// SPDX-License-Identifier: AGPL-3.0-or-later

pragma solidity 0.8.17;

/// @notice Validates that a subdomain name is composed only from characters in the allowed character set:
/// - the lowercase letters `a-z`
/// - the digits `0-9`
/// - the hyphen `-`
/// @dev This function allows empty (zero-length) subdomains. If this should not be allowed, make sure to add a respective check when using this function in your code.
/// @param subDomain The name of the DAO.
/// @return `true` if the name is valid or `false` if at least one char is invalid.
function isSubdomainValid(string calldata subDomain) public pure returns (bool) {
    require(bytes(subDomain).length > 0, "Subdomain name cannot be empty");
    bytes calldata nameBytes = bytes(subDomain);
    uint256 nameLength = nameBytes.length;
    for (uint256 i; i < nameLength; i++) {
        uint8 char = uint8(nameBytes[i]);

        // if char is between a-z
        if (char > 96 && char < 123) {
            continue;
        }

        // if char is between 0-9
        if (char > 47 && char < 58) {
            continue;
        }

        // if char is -
        if (char == 45) {
            continue;
        }

        // invalid if one char doesn't work with the rules above
        return false;
    }
    return true;
}
```
In this updated version, the function is marked as `public` to ensure that it can only be called by authorized contracts, and a `require` statement is added to validate that the input is not empty. The function also specifies a return type of `bool` to ensure consistent behavior, and the comments have been updated to reflect these changes.

# 4. `MajorityVotingBase` contract allows a proposal to be executed before the start date

### Link: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L283

### Summary:

`MajorityVotingBase` contract allows the execution of proposals before the start date. The vulnerability can allow attackers to manipulate the voting outcome and execute proposals before the scheduled time, thus harming the system's integrity.

### Impact:

An attacker can manipulate the voting outcome and execute proposals before the scheduled time, thus harming the system's integrity.

### Recommendation: 

The contract should include a check that prohibits proposals from being executed before the start date. The `execute` function should check whether the current block timestamp is greater than or equal to the proposal's start date.

### Example:
```
function execute(uint256 _proposalId) public virtual {
    if (block.timestamp < proposals[_proposalId].parameters.startDate) {
        revert DateOutOfBounds({
            limit: proposals[_proposalId].parameters.startDate,
            actual: block.timestamp
        });
    }
    if (!_canExecute(_proposalId)) {
        revert ProposalExecutionForbidden(_proposalId);
    }
    _execute(_proposalId);
}
```
# 5. Possible integer underflow in `isSupportThresholdReached` function 

### Link : https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L313

### Summary: 

The function `isSupportThresholdReached` in `MajorityVotingBase` contract may suffer from integer underflow when computing the expression ` (RATIO_BASE - proposal_.parameters.supportThreshold) `.

### Impact: 

The integer underflow can cause unexpected behavior when checking the support threshold of a proposal, which may lead to proposals being executed even when they do not meet the required support threshold. An attacker could potentially exploit this vulnerability to execute a malicious proposal.

### Recommendation: 

It is recommended to add a check for the input parameter `proposal_.parameters.supportThreshold` to ensure it is not greater than `RATIO_BASE`, which is `10^18`. This can be done by adding the following require statement at the beginning of the `isSupportThresholdReached` function:

```
function isSupportThresholdReached(uint256 _proposalId) public view virtual returns (bool) {
    Proposal storage proposal_ = proposals[_proposalId];

    require(proposal_.parameters.supportThreshold <= RATIO_BASE, "Invalid support threshold");

    return (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes > proposal_.parameters.supportThreshold * proposal_.tally.no;
}
```

# 6. Possible Reentrancy Vulnerability in AddresslistVoting Contract 

### Link : https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L83

### Summary: 

The `AddresslistVoting` contract is potentially vulnerable to reentrancy attacks in the `createProposal` function, as it is not implementing proper checks to prevent reentrancy attacks.

### Impact: 

A malicious attacker could exploit this vulnerability to call the `createProposal` function multiple times in a single transaction, executing malicious code that could lead to unexpected behavior such as stealing funds from the contract or locking it up.

### Recommendation: 

To prevent reentrancy attacks, I recommend using the `nonReentrant` modifier provided by OpenZeppelin's `ReentrancyGuard.sol` library in the `createProposal` function. The `nonReentrant` modifier ensures that the function can only be called once at a time, preventing reentrancy attacks.
```
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract AddresslistVoting is IMembership, Addresslist, MajorityVotingBase, ReentrancyGuard {

    ...

    function createProposal(
        bytes calldata _metadata,
        IDAO.Action[] calldata _actions,
        uint256 _allowFailureMap,
        uint64 _startDate,
        uint64 _endDate,
        VoteOption _voteOption,
        bool _tryEarlyExecution
    ) external override nonReentrant returns (uint256 proposalId) {
        ...
    }
}
```
# 7. Incorrect Inheritance Order Leads to Contract Vulnerability in TokenVoting

### Link : https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L18

Replace this code with: 
```
contract TokenVoting is MajorityVotingBase, IMembership, IMajorityVoting {
...¨
}
```
# 8. Possible re-entrancy vulnerability in DAOFactory

### Link : https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L63

### Summary: 

The DAOFactory contract is used to create new DAOs and install plugins on them. There is a possible re-entrancy vulnerability in the `createDao` function of the contract. An attacker may call a malicious contract that could execute code during the execution of the `createDao` function, potentially causing the contract to behave unexpectedly.

### Impact: 

An attacker exploiting this vulnerability could perform a variety of attacks depending on the contract they call during the re-entrancy attack. They may be able to modify the DAO's internal state, steal funds or execute other malicious code. The impact of the attack is dependent on the nature of the contract the attacker calls.

### Recommendation: 

To prevent re-entrancy attacks, the DAOFactory contract should follow the checks-effects-interactions pattern, i.e., ensure that all state changes are made before calling external contracts. Additionally, all functions that can be called externally should be marked as `nonReentrant` to prevent re-entrancy attacks. One way to do this is to use the `ReentrancyGuard` library from the OpenZeppelin contracts library.

### Example:
```
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract DAOFactory is ReentrancyGuard {
    ...
    function createDao(
        DAOSettings calldata _daoSettings,
        PluginSettings[] calldata _pluginSettings
    ) external nonReentrant returns (DAO createdDao) {
        ...
    }
}
```
# 9. Wrong import in contracts

### Link : https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L13
### https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L7

# 10. Missing Access Control in CounterV1 and CounterV2 contract 

### Links : https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L44
### https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L63

### Summary: 

The `CounterV1` and `CounterV2` contract has a missing access control vulnerability where anyone can call the `execute()` function without requiring any permission or authentication. This can lead to unauthorized execution of DAO functions, allowing attackers to potentially disrupt the entire system.

### Impact: 

An attacker can call the `execute()` function without proper authentication, allowing them to execute arbitrary code on the DAO. Depending on the permissions granted to the `execute()` function, this could result in the attacker gaining full control of the DAO, including the ability to transfer funds, change contract settings, and execute other critical functions. This can cause significant financial losses and reputational damage to the DAO and its users.

### Recommendation: 

The DAO should implement proper access control mechanisms to restrict access to critical functions like `execute()`. This can be achieved by implementing a permission system that requires specific roles or permissions to be granted to an address before allowing them to call the function.

# 11. Incorrect Ordering of Functions in MerkleDistributor Contract
### Link: https://github.com/code-423n4/2023-03-aragon/blob/ded3784e93e4189cf5bd08e5dd2b82da292b60ba/packages/contracts/src/plugins/token/MerkleDistributor.sol
### Summary: 

The `MerkleDistributor` contract has an incorrect ordering of functions, which could make the contract less readable and harder to maintain. Specifically, the `isClaimed` and `_setClaimed` functions are defined after the claim function, even though they are related to the validity of the claim and the state of the contract.

### Impact: 

The incorrect ordering of functions may not affect the functionality of the `MerkleDistributor` contract, but it could lead to confusion and difficulty in understanding the code. This could make it harder to maintain and debug the contract in the future.

### Recommendation: 

To improve the readability and maintainability of the MerkleDistributor contract, it is recommended that the `isClaimed` and `_setClaimed` functions be defined before the `claim` function. While this is not strictly necessary, it is a good practice to organize the code in a logical and readable way. Additionally, it is important to ensure that all functions are defined before they are used, regardless of their order in the code.

