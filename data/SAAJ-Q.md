
# Low Risk and Non-Critical Issues

Some of the opportunities identified for improving low severity and non critical issues throughout the codebase of Aragon protocol are categorised into 03 main areas; with further multiple instances in each of the category.


# [L-01] Lacks address(0) check (02 Instances)

Zero-address check should be used to avoid the risk of setting a storage variable at address(0).

Link to the code:
1.	https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L68
2.	https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L41


# [N-01] Empty blocks should be removed (02 Instances)

Avoid using code blocks or use them for emitting events.

Link to the code:

1.	https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L56
2.	https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L76


# [N-02] Remove file from main directory (01 Instance)

If file is intended to be used for test should not be in the main directory.

Link to the code:

1.	https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSMigration.sol

