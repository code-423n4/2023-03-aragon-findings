Title * 
Missing check that _newTrustedForwarder address is already set or no

Links to affected code *
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L139

## Impact
When we call the setTrustedForwarder function in the DAO contract, we need first to check that the new value for trustedForwarder is not equal to the previous value and then make the change.

## Tools Used
manually

############################################################################################

Title * 
Omissions in Events

Links to affected code *
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L286

## Impact
Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.The events should include the new value and old value where possible.

## Tools Used
manually

## Recommended Mitigation Steps
save old value and after makes change, emit old value with new value.

############################################################################################

Title * 
Pattern https://eips.ethereum.org/EIPS/eip-4824 is not fully observed

Links to affected code *
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L118

## Impact
based on https://eips.ethereum.org/EIPS/eip-4824 document, A DAO MAY inherit the above interface above or it MAY create an external registration contract that is compliant with this EIP. The external registration contract MUST store the DAO’s primary address.

in the DAO.sol.initialize function, first we make call to the _setDaoURI without initial owner or DAO address or set role for SET_METADATA_PERMISSION.

as we see in the https://eips.ethereum.org/EIPS/eip-4824, in the initialize function, first we MUST set the initial owner or DAO address, and then make call to the _setDaoURI. in the aragon, first we should set role for SET_METADATA_PERMISSION.

## Tools Used
manually

## Recommended Mitigation Steps
follow https://eips.ethereum.org/EIPS/eip-4824 doc.

############################################################################################

Title * 
There is not any function to cover any underlyingTokens that would have been transferred by mistake in the GovernanceWrappedERC20.sol

Links to affected code *
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L32
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/dd8ca8adc47624c5c5e2f4d412f5f421951dcc25/contracts/token/ERC20/extensions/ERC20WrapperUpgradeable.sol#L72

## Impact
There is not any function in the GovernanceWrappedERC20.sol.sol to Mint wrapped token to cover any underlyingTokens that would have been transferred by mistake.

function 

    function _recover(address account) internal virtual returns (uint256) {
        uint256 value = _underlying.balanceOf(address(this)) - totalSupply();
        _mint(account, value);
        return value;
    }

is implemented in the ERC20WrapperUpgradeable.sol#L72 from openzeppelin-contracts-upgradeable as internally but  this function is not used in the GovernanceWrappedERC20. This is not necessary but it can be a solution to solve unexpected problems.

## Tools Used
manually

############################################################################################

Title * 
DAOFactory.createDao function is front-runnable and can cause DoS

Links to affected code *
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L76
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAOFactory.sol#L35
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/dao/DAORegistry.sol#L60
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L87

## Impact
DAOFactory.createDao function is a public function and anyone who wants to create dao needs to call this function with input data. one of the inputs is _daoSettings.subdomain. in ENSSubdomainRegistrar.sol#L87 we check the _daoSettings.subdomain, whether it is already registered or not? if already Registered, the call will get reverted AlreadyRegistered(subnode, currentOwner. if not, we will register the _daoSettings.subdomain for the new DAO.

as you know malicious users can listen to the mempool and create new transactions with higher gas feefeesransactions with higher gas feefeesll get mined faster.

## Proof of Concept
USERA will call DAOFactory.createDao with _daoSettings.subdomain = "code4rena", the transaction will get created and sent to the mempool. now malicious user see the transaction of USERA  in mempool and create new transaction with _daoSettings.subdomain = "code4rena" and higher gas fee and send it to the blockchain. transaction from malicious user will get mine faster because of higher gas fee and now _daoSettings.subdomain = "code4rena" is already Registered. and transfaction from USERA will get revert AlreadyRegistered(subnode, currentOwner).

## Tools Used
Manually

## Recommended Mitigation Steps
make subdomain more unique in aragon protocol.