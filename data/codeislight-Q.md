# Summary
## Quality Assurance Findings List:
| Number |Issues Details|
|:--:|:-------|
|[QA-1]| Events might be susceptible to phishing attacks |
|[QA-2]| Lack of checking for setting signature validator to be an EOA |
|[QA-3]| Registered callabacks selectors are vulnerable to hashing collision |
|[QA-4]| The external execute call is vulnerable to gas griefing attacks |

### [QA-1] Events might be susceptible to phishing attacks

Events play a crucial rule in providing a mean of subscribing to the data feed being emitted from smart contracts, ensuring that the valid events that have been originated from one of the expected processes is important. in this case, most of the events are being purposefully consumed by the subgraph. so It is suggested that in the areas where it's possible for someone to spam the events being emitted to add measures to ensure that only valid deposits are allowed. there is a possibility for phishing attacks being directed at DAO.deposit(), which would lead to events being emitted, even though the `_token` is a malicious contract, that technically does nothing and only return true. also the `_reference` may be used to pass in links to scam links.

possible defending measures could be to remain the same function but without an emit, and allow another function which only accept tokens that are approved by the protocol (mapping or merkle tree) and emits the event.

### [QA-2] Lack of checking for setting signature validator to be an EOA

It is not enforced that the signature validator is an EOA which would leave the room to be passed in an address that is not the actual signature validator or even a smart contract. so it's advised that a message is signed, and the signature is passed in to the function to validate it and the signer using openzepplin ECDSA library [recover function](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/f8e3c375d19bd12f54222109dd0801c0e0b60dd2/contracts/utils/cryptography/ECDSA.sol#L88) . in that case, we would be confident that the validator is an EOA and also is owned by us and not mistakenly passed.

### [QA-3] Registered callabacks selectors are vulnerable to hashing collision

The callback handler is intended to handle callbacks coming from DAO.execute() calls, as we know on a bytes4 data type, there is a highly likelyhood of hashing collision, which would lead that the information being passed doesn't necessary need to be the intended hash to be recorded as an event by CallbackReceived, which would record the data being passed, which is expected to be of the record ERC callbacks, but might any other data function parameters combination. since bytes4 has 2**32 possibilities, which with many tries can lead to 2 functions with common hash. which would allow that any caller will be able to call the fallback function with any data and record it through the event.

As an example, ERC20 and ERC721 transfer functions has many [function selectors collisions](https://www.4byte.directory/signatures/?bytes4_signature=0xa9059cbb) with other functions.
### [QA-4] The external execute call is vulnerable to gas griefing attacks

making external call using address.call() in DAO.execute() is suscepitble to griefing attack, since the returned response is copied to memory which could allow the callee to return a large bytes which would trigger memory expansion that would lead to wasting/disturbing the actions execution. which can also possible distrupt the actions execution.

a possible solution could be to provide another paramter named `_ignoreResponseMap`, similar to `_allowFailureMap` which would provide flags to ignore the return to be received. in the case the action bit is set to 1, it would make an assembly call with 0 size allocation for returned response, otherwise it would just receive it as normal.

```solidity

function execute(bytes32 _callId, Action[] memory _actions, uint256 _allowFailureMap, uint256 _ignoreResponseMap)
        external
        override
        auth(EXECUTE_PERMISSION_ID)
        returns (bytes[] memory execResults, uint256 failureMap, uint256 ignoredResponseMap)
    {
        if (_actions.length > MAX_ACTIONS) {
            revert TooManyActions();
        }

        execResults = new bytes[](_actions.length);
        bool success;
        bytes memory response;
        address to;
        address value;
        bytes memory _data;
        for (uint256 i = 0; i < _actions.length;) {
            to = _actions[i].to;
            value = _actions[i].value;
            // +                success := call(gas(), dest, amount, 0, 0)

            if(hasBit(_ignoreResponseMap,uint8(i))){
                _data = _actions[i].data;
                assembly {
                    success := call(gas(),to,value, _data /* data memory location */, mload(_data) /* data length */, 0, 0)
                }

                ignoredResponseMap = flipBit(ignoredResponseMap, uint8(i));

            } else {
                (success, response) = to.call{value: _actions[i].value}(_actions[i].data);

                execResults[i] = response;
            }

            if (!success) {
                // If the call failed and wasn't allowed in allowFailureMap, revert.
                if (!hasBit(_allowFailureMap, uint8(i))) {
                    revert ActionFailed(i);
                }

                // If the call failed, but was allowed in allowFailureMap, store that
                // this specific action has actually failed.
                failureMap = flipBit(failureMap, uint8(i));
            }

            unchecked {
                ++i;
            }
        }

        emit Executed({
            actor: msg.sender,
            callId: _callId,
            actions: _actions,
            failureMap: failureMap,
            execResults: execResults
        });
    }
}

```