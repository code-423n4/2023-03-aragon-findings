### Gas Optimization Template
| Count | Title | Instances |
|:--:|:-------|:--:|
| [G-01] | Use nested `if` statements to avoid multiple check combinations using `&&` | 13 |
| [G-02] | `internal/private` functions only called once can be inlined to save gas  | 12 |
| [G-03] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (same with `-=`) | 1 |
| [G-04] | Multiple accesses of a mapping/array should use a local variable cache | 2 |
| [G-05] | Initialize stack variables outside loop| 4 |
| [G-06] | Increments can be unchecked | 2 |
| [G-07] | State variables only set in the constructor should be declared `immutable` | 5 |

| Total Gas-Optimization Issues | 7 |
|:--:|:--:|

### [G-01] Use nested `if` statements to avoid multiple check combinations using `&&`
Context:
```solidity
13 results - 9 files

/RegistryUtils.sol
20: if (char > 96 && char < 123) 
25: if (char > 47 && char < 58)

/AddresslistVoting.sol
97: if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock))
185: if (_tryEarlyExecution && _canExecute(_proposalId))

214:        if (
215:            proposal_.voters[_account] != VoteOption.None &&
216:            proposal_.parameters.votingMode != VotingMode.VoteReplacement
217:        )

/TokenVoting.sol
182: if (_tryEarlyExecution && _canExecute(_proposalId))

211:        if (
212:            proposal_.voters[_account] != VoteOption.None &&
213:            proposal_.parameters.votingMode != VotingMode.VoteReplacement
214:        )

/Multisig.sol
216: if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock))
283: if (_tryExecution && _canExecute(_proposalId))

/PluginRepo.sol
157: if (version.tag.release != 0 && version.tag.release != _release)

/PluginSetupProcessor.sol
700:        if (
701:            msg.sender != _dao &&
702:            !DAO(payable(_dao)).hasPermission(address(this), msg.sender, _permissionId, bytes(""))
703:        )

/PermissionManager.sol
236: if (_where == ANY_ADDR && _who == ANY_ADDR)

/GovernanceWrappedERC20.sol
106:if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0))

/GovernanceERC20.sol
116: if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0))
```

Recommendation:
Replace `&&` used within `if` and `else if` statements with nested `if` statements

### [G-02] `internal` functions only called once can be inlined to save gas

Description:
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Context:
Consider inlining less complex functions for the `internal` functions instances below:

```solidity
4 results - 4 files

/DAOFactory.sol
141: function _createDAO(DAOSettings calldata _daoSettings) internal returns (DAO dao)

/PermissionManager.sol
212: function _initializePermissionManager(address _initialOwner) internal

/ProposalUpgradeable.sol
33: function _createProposalId() internal returns (uint256 proposalId)

/Proposal.sol
33: function _createProposalId() internal returns (uint256 proposalId)
```

- `_createDAO()` only used in `createDAO()` within the same contract
<br/>
- `_initializePermissionManager()` only used in `__PermissionManager_init()` within the same contract
<br/>
- `_createProposalId()` only used in `_createProposal()` within the same contract
<br/>
-  `_createProposalId()` only used in `_createProposal()` within the same contract
  
For more complex functions listed below, consider inlining if it does not result in core functions being too complex:
```solidity
2 results - 2 files

/DAOFactory.sol
156: function _setDAOPermissions(DAO _dao) internal

/PluginRepoFactory.sol
65: function _setPluginRepoPermissions
```

Similarly, consider inlining the following `private` function instances if core function does not get too complex:

```solidity
6 results - 5 files

/TokenFactory.sol
144: function setupBases() private

/TokenVotingSetup.sol
276: function _isERC20(address token) private view returns (bool)

/PluginSetupProcessor.sol
699: function _canApply(address _dao, bytes32 _permissionId) private view
719: function emitPrepareUpdateEvent

/MerkleDistributor.sol
120: function _setClaimed(uint256 _index) private

/DAO.sol
290: function _registerTokenInterfaces() private
```
- `setupBases()` only used in `constructor()` within the same contract
<br/>
- `_isERC20()` only used in `prepareInstallation()` within the same contract
<br/>
- `_canApply()` only used in `canApply()` within the same contract
<br/>
- `emitPrepareUpdateEvent()` only used in `prepareUpdate` within the same contract
<br/>
- `_setClaimed()` only used in `claim()` within the same contract
 <br/>
- `_registerTokenInterfaces()` only used in `initialize()` within the same contract

- `_setDAOPermissions()` only used in `createDAO()` within the same contract
<br/>
- `_setPluginRepoPermissions` only used in `createPluginRepoWithFirstVersion()` within the same contract

### [G-03] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (same with `-=`)
Context:
```solidity
1 result - 1 file

/Multisig.sol
276: proposal_.approvals += 1;
```

Recommendation:
Replace `<x> += <y>` with `<x> = <x> + <y>` for state variables (same for `-=`)

### [G-04] Multiple accesses of a mapping/array should use a local variable cache
Description:
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory

Recommendation:
Cache variable like this
`SomeValue storage someValue = someMap[someIndex]`

#### MajorityVotingBase.sol
Cache `proposals[_proposalId]` in local storage
```solidity
1 result

457:    function _execute(uint256 _proposalId) internal virtual {
458:        proposals[_proposalId].executed = true;
459:
460:        _executeProposal(
461:            dao(),
462:            _proposalId,
463:            proposals[_proposalId].actions,
464:            proposals[_proposalId].allowFailureMap
465:        );
466:    }
```

#### MerkleDistributor.sol
Cache `claimedBitMap[claimedWord_index]` in local storage
```solidity
1 result

120:    function _setClaimed(uint256 _index) private
123:        claimedBitMap[claimedWord_index] =
124:            claimedBitMap[claimedWord_index]
```

### [G-05] Initialize stack variables outside loop
```solidity
4 results - 3 files

/RegistryUtils.sol
16:    for (uint256 i; i < nameLength; i++) {
17:        uint8 char = uint8(nameBytes[i]);

/DAOFactory.sol
95:        for (uint256 i; i < _pluginSettings.length; ++i) {
96:            // Prepare plugin.
97:            (
98:                address plugin,
99:                IPluginSetup.PreparedSetupData memory preparedSetupData

/DAO.sol
184:        for (uint256 i = 0; i < _actions.length; ) {
185:            address to = _actions[i].to;
```

Description:
Consider initializing the stack variables before the loop to avoid reinitialization on every loop

Recommendation:
Consider initializing the stack variables before the loop to save gas 

### [G-06] Increments can be unchecked
```solidity
2 results - 2 files

/RegistryUtils.sol
16: for (uint256 i; i < nameLength; i++)

/DAOFactory.sol
95: for (uint256 i; i < _pluginSettings.length; ++i)
```

Description:
In Solidity 0.8.0+, there is a default overflow check on unsigned integers. It is possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Recommendation:
Code goes from:
```solidity
for (uint256 i; i < numIterations; ++i) {  
 // ...  
}  
```

to 

```solidity
for (uint256 i; i < numIterations;) {  
 // ...  
 unchecked { ++i; }  
}  
```

### [G-07] State variables only set in the constructor should be declared `immutable`
```solidity
5 results - 2 files

/TokenFactory.sol
27: address public governanceERC20Base;
30: address public governanceWrappedERC20Base;
33: address public merkleMinterBase;
36: MerkleDistributor public distributorBase;

/PluginSetupProcessor.sol
128: PluginRepoRegistry public repoRegistry;
```

Description:
This avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

Recommendation:
Declare state variables only assigned in the constructor to be `immutable`