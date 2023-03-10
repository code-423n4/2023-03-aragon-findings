# QA report

## Low Risk
| L-N    |Issue|
|:------:|:----|
| [L&#x2011;01] | Missing event emission on setter function | 9 |

Total: 1 issue

## Non-critical

| N-N    |Issue|
|:------:|:----|
| [N&#x2011;01] | Contract layout and order of function
| [N&#x2011;02] | Unused import and custom error | 11 |
| [N&#x2011;03] | Typos in the comments | 19 |
| [N&#x2011;04] | Functions are missing `override` keyword | 31 |


Total: 4 issues

### [L-01] Missing event emission on setter function

File: `CounterV2.sol`

`setNewVariable` is missing event emission which might not be good for off-chain monitoring. Emit an event on state change.

### [NC-01] Contract layoyt and order of function

File: `MajorityVotingBase.sol`

The Solidity style guide recommends declaring internal functions below external functions. If possible, consider adding internal functions below external functions for the contract layout. Furthermore, it’s also recommended to declare pure and view functions at the end of a grouping. Also It is a best practice that custom errors, structs and events are declared in the interface not in the implementation contract.

### [NC-02] Unused import and custom error

`IProposal` import is not unused and can be removed

### [NC-03] Typos in the comments

File: `MajorityVotingBase.sol`

` /// @param VoteReplacment` -> ` /// @param VoteReplacement`

`proposa` -> `proposal`

`comparision` -> `comparison`

File: `PluginSetupProcessor.sol`

`inidcated` -> `indicated`

### [NC-04] Functions are missing `override` keyword

Functions from `IMajorityVotingBase.sol`  that are declared in its implementation `MajorityVotingBase.sol` are missing override keyword. Consider adding it.
