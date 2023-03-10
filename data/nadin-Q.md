## Low Risk Issues List
Issue 
### [L-01] USING VULNERABLE DEPENDENCY OF OPENZEPPELIN
Total: 01 issues

### [L-01] USING VULNERABLE DEPENDENCY OF OPENZEPPELIN
The package.json configuration file says that the project is using ^4.1.0  of OZ which has a not last update version:
```
109:    "@openzeppelin/contracts" "^4.1.0"
```
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/yarn.lock#L109
Recommended Mitigation Steps
Use patched versions.
Latest non vulnerable version 4.8.2.

## Non-Critical Issues List
Issue 
### [N-01] It's better to emit after all processing is done
### [N-02] MISSING EVENT FOR CRITICAL PARAMETERS INIT AND CHANGE
### [N-03] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
### [N-04] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)

Total: 04 issues

### [N-01] It's better to emit after all processing is done
File: packages/contracts/src/framework/utils/TokenFactory.sol
```
106:            emit WrappedToken(GovernanceWrappedERC20(token));
```
File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol
```
175:        emit VoteCast({
```
File: packages/contracts/src/plugins/governance/multisig/Multisig.sol
```
281:        emit Approved({proposalId: _proposalId, approver: approver});
```

### [N-02] MISSING EVENT FOR CRITICAL PARAMETERS INIT AND CHANGE
Description:
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
Recommendation:
Add Event-Emit
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol
```
100:    function setDefaultResolver(
```

### [N-03] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values):

This will help with readability and easier maintenance for future changes.
constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution

### [N-04] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)
Rather than using abi.encodePacked for appending bytes, since version 0.8.4, bytes.concat() is enabled
Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,).
File: packages/contracts/src/framework/plugin/repo/PluginRepo.sol
```
252:        return keccak256(abi.encodePacked(_tag.release, _tag.build));
```
File: packages/contracts/src/core/permission/PermissionManager.sol
```
347:        return keccak256(abi.encodePacked("PERMISSION", _who, _where, _permissionId));
```

### [S-01] GENERATE PERFECT CODE HEADERS EVERY TIME
Description:
I recommend using header for Solidity code layout and readability
https://github.com/transmissions11/headers
```
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```