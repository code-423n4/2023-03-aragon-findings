
# Gas Optimizations Report

This report focuses on Aragon Protocol contest, in context of various improvements that can be made in terms of gas cost.
Some of the opportunities identified for improving gas efficiency throughout codebase of Aragon Protocol are categorised into 05 main areas; with further multiple instances in each of the category.


# Summary

[G-01] Immutable has more gas efficiency than constant (15 Instances)
[G-02] Uint/Int lower than 32 bytes incurs overhead (31 Instances)
[G-03] Functions with public visibility, if not called within the contract needed to be changed external (11 Instances)
[G-04] Multiple mappings can be combine into a single one (03 Instances)
[G-05] 0perator assignment is more gas efficient than compound assignment (01 Instance)
 

 
# [G-01] Immutable has more gas efficiency than constant (15 Instances)

Using immutable instead of constant, save more gas due to avoiding storage access for state variables.

Variable values are set through constructor when using immutable, which also eliminates the need of assigning initial values to state variable making them more efficient in terms of gas cost.

Link to the Code:

1.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18
2.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18
3.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L22
4.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L182
5.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L23
6.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L27
7.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L22
8.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L71
9.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/core/dao/DAO.sol#L40
10.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/core/dao/DAO.sol#L43
11.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/core/dao/DAO.sol#L46
12.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/core/dao/DAO.sol#L49
13.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/core/dao/DAO.sol#L53
14.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/core/dao/DAO.sol#L57
15.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/core/dao/DAO.sol#L61


# [G-02] Uint/int lower than 32 bytes consumes incurs overhead (31 Instances)

Contract gas usage increases as EVM standard operation are of 32 bytes. If any element is smaller than 32 bytes (i.e.; 256 bits) it will cause EVM to consume more gas which can be around 12 gas depending on size for reducing the size to given output like uint8.

Link to the Code:

1.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/utils/RegistryUtils.sol#L17
2.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L37
3.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/IMajorityVoting.sol#L41
4.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L194
5.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L199
6.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L351
7.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L356
8.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L362
9.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L437
10.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L438
11.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L515
12.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L564
13.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L565
14.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L566
15.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L579
16.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L87
17.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L88
18.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L92
19.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L94
20.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L111
21.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L99
22.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L104
23.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L109
24.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L119
25.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L175
26.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L211
27.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L212
28.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L214
29.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L312
30.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L404
31.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L414


# [G-03] Functions with public visibility, if not called within the contract needed to be changed external (11 Instances)

Functions with public visibility, if not called within the contract needed to be changed external.

Link to the Code:

1.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L264
2.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L282
3.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L290
4.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L298
5.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L307
6.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L351
7.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L356
8.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L362
9.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L368
10.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L374
11.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L391


# [G-04] Multiple mappings can be combine into a single one (03 Instances)

When multiple mappings are used in same function, it’s better to combined them into a single mapping using a struct.
Combined mapping reduces storage slot per mapping and also are cheaper in terms of associated atack operations calculation carried out.

Link to the Code:

1.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L55
2.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L58
3.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L61


# [G-05] 0perator assignment is more gas efficient than compound assignment (01 Instance)

Compound assignment operators (+= / -=) are more expensive in terms of gas consumption and needs to be avoided.

Operator assignments (a = a + b / a - b) are preferable in terms of gas optimization.

Link to the Code:

1.	https://github.com/aragon/osx/blob/develop/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L276
