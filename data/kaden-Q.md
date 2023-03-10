#### Lack of `address(0)` check

##### Severity: Low

##### Context:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L118

##### Description

On deployment of a DAO contract, there exist no checks to ensure that the address being set as `_initalOwner`, which is granted `ROOT_PERMISSION_ID`, is not `address(0)`. As a result, it's possible to initially grant `ROOT_PERMISSION_ID` only to `address(0)`, thereby effectively bricking the entire DAO.


#### `merkleRoot` immutability can cause tokens to be permanently locked in `MerkleDistributor`

##### Severity: Low

##### Context: 

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/token/MerkleMinter.sol#L90

##### Description

`MerkleDistributor` uses a `merkleRoot` to allow users to claim appropriate amounts of tokens. This value can only be set in the `initialize` method which can only be executed once. 

`MerkleMinter.merkleMint` creates a `MerkleDistributor` contract and immediately mints tokens to the new address. There exist no checks that the `_merkleRoot` being passed to the `initialize` method is valid, i.e. not `bytes32(0)`. As a result, it's possible that the tokens being sent to the distributor could be permanently locked in the contract.


#### Lack of ENS subdomain validation

##### Severity: Low

##### Context:

- https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/RegistryUtils.sol#L13

##### Description

Before registering a DAO or plugin's subdomain, `isSubdomainValid` is called to validate the provided string. This method fails to validate the length of the subdomain being provided, allowing creation of subdomains with 0 characters and >255 characters, with the latter breaking DNS and [NameWrapper](https://github.com/ensdomains/ens-contracts/tree/master/contracts/wrapper) compatibility.