Title * 
Missing check that _newTrustedForwarder address is already set or no

Links to affected code *
https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L139

## Impact
When we call the setTrustedForwarder function in the DAO contract, we need first to check that the new value for trustedForwarder is not equal to the previous value and then make the change.

## Tools Used
manually

## Recommended Mitigation Steps
Use if block to check new trustedForwarder value with previous value.

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

in the DAO.sol.initialize function, first we make call to the _setDaoURI and then we make call to the __PermissionManager_init.

as we see in the https://eips.ethereum.org/EIPS/eip-4824, in the initialize function, first we MUST set the initial owner or DAO address, and then make call to the _setDaoURI.

## Tools Used
manually

## Recommended Mitigation Steps
follow https://eips.ethereum.org/EIPS/eip-4824 doc.

############################################################################################