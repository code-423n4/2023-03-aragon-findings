## QA REPORT

| |Issue|
|-|:-|
| [01] | `DAO.execute` FUNCTION DOES NOT CONSIDER TOKEN'S `transfer` OR `transferFrom` FUNCTION CALL THAT DOES NOT REVERT BUT RETURNS `false` AS A FAILURE |
| [02] | `DAO.execute` FUNCTION DOES NOT CHECK IF `_actions[i].to` HAS ANY CONTRACT CODE WHEN `_actions[i].data` IS NOT EMPTY |
| [03] | WHEN `_actions[i].data` IS NOT EMPTY, `DAO.execute` FUNCTION DOES NOT CHECK IF SUCH `_actions[i].data`'S FUNCTION EXISTS IN `_actions[i].to` CONTRACT |
| [04] | `PermissionManager.revoke` TRANSACTION CAN BE FRONTRUN |
| [05] | WHETHER `PermissionManager._grantWithCondition` FUNCTION SHOULD REVERT WHEN `_where == ANY_ADDR` OR `_who == ANY_ADDR` IS TRUE NEEDS TO BE RESOLVED |
| [06] | `DAO` CONTRACT'S `receive()` CAN BE UPDATED TO CALL `DAO.deposit` FUNCTION WITH `_token` INPUT BEING `address(0)` |
| [07] | UNLIKE `DAO.deposit`, `DAO` CONTRACT HAS NO FUNCTIONS FOR DEPOSITING ERC721 AND ERC1155 TO DAO |
| [08] | `PermissionManager.applyMultiTargetPermissions` FUNCTION ALREADY COVERS USE CASES OF `PermissionManager.applySingleTargetPermissions` FUNCTION |
| [09] | MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS |
| [10] | REDUNDANT RETURN STATEMENTS FOR FUNCTIONS WITH NAMED RETURNS CAN BE REMOVED |
| [11] | VULNERABILITIES IN VERSION 4.8.1 OF `@openzeppelin/contracts` AND `@openzeppelin/contracts-upgradeable` |
| [12] | SOLIDITY VERSION `0.8.19` CAN BE USED |
| [13] | DEFINITIONS OF `UNSET_FLAG` AND `ALLOW_FLAG` ARE INCORRECT IN DOCUMENTATION |
| [14] | `bytes4(0)` CAN BE REPLACED WITH A CONSTANT |
| [15] | `NewURI` EVENT CAN BE MOVED TO `IDAO` INTERFACE |
| [16] | INPUT VARIABLE CAN BE NAMED WITH LEADING UNDERSCORE |
| [17] | WORD TYPING TYPOS |
| [18] | INCOMPLETE NATSPEC COMMENTS |

## [01] `DAO.execute` FUNCTION DOES NOT CONSIDER TOKEN'S `transfer` OR `transferFrom` FUNCTION CALL THAT DOES NOT REVERT BUT RETURNS `false` AS A FAILURE
Some tokens do not revert but return `false` when calling their `transfer` or `transferFrom` functions fail; to cover this scenario, OpenZeppelin's `SafeERC20` library would ensure that the corresponding return data must not be false by executing this [require](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol#L123) statement. However, for the same scenario, when calling the following `DAO.execute` function, if `_actions[i].to` corresponds to such token and `_actions[i].data`'s function is `transfer` or `transferFrom`, `success` would be set to true after executing `(bool success, bytes memory response) = to.call{value: _actions[i].value}(_actions[i].data)`, and this call would not be considered as a failure. As a result, the DAO can result in an unexpected state; for example, because transferring such tokens to the DAO fail silently, the DAO could falsely think that it has received the corresponding funds and update its accounting system incorrectly.

As a mitigation, the `DAO.execute` function can be updated to check if `response` returned by executing `to.call` is false when `_actions[i].data`'s function is `transfer` or `transferFrom`. If it is false, the corresponding call should be considered as a failure; whether such failure can be allowed or not can then be determined by the `_allowFailureMap` input.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L168-L215
```solidity
    function execute(
        bytes32 _callId,
        Action[] calldata _actions,
        uint256 _allowFailureMap
    )
        external
        override
        auth(EXECUTE_PERMISSION_ID)
        returns (bytes[] memory execResults, uint256 failureMap)
    {
        ...
        for (uint256 i = 0; i < _actions.length; ) {
            address to = _actions[i].to;
            (bool success, bytes memory response) = to.call{value: _actions[i].value}(
                _actions[i].data
            );

            if (!success) {
                // If the call failed and wasn't allowed in allowFailureMap, revert.
                if (!hasBit(_allowFailureMap, uint8(i))) {
                    revert ActionFailed(i);
                }

                // If the call failed, but was allowed in allowFailureMap, store that
                // this specific action has actually failed.
                failureMap = flipBit(failureMap, uint8(i));
            }
            ...
        }
        ...
    }
```

## [02] `DAO.execute` FUNCTION DOES NOT CHECK IF `_actions[i].to` HAS ANY CONTRACT CODE WHEN `_actions[i].data` IS NOT EMPTY
It is a popular practice for a protocol to deploy its token using the same deployer contract address and the same nonce so the addresses of such token are the same on different chains. Thus, the DAO could expect that the addresses of an external protocol's token are the same on different chains. Yet, it is possible that the external protocol has not deployed its token on one of these chains but the DAO does not notice this and calls the following `DAO.execute` function to interact with such token address on such chain, such as for transferring certain amount of such token to the DAO. When the `_actions[i].to` address corresponding to such token does not have any contract code, executing `(bool success, bytes memory response) = to.call{value: _actions[i].value}(_actions[i].data)` in the `DAO.execute` function would return a true `success`. In this case, this action for interacting with such token on such chain is considered as a success even though such interaction did fail silently, which can cause the DAO to end up in an unexpected state, such as that the DAO expects to receive an amount of such token but does not in reality. Because of this, disputes can occur among the DAO, the DAO's community, and this protocol.

As a mitigation, the `DAO.execute` function can be updated to check the `_actions[i].to` address's contract code size if `_actions[i].data` is not empty for `_actions[i]`. When `_actions[i].data` is not empty but `_actions[i].to` address does not have any contract code, the corresponding call should be considered as a failure and can be processed according to the provided `_allowFailureMap`.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L168-L215
```solidity
    function execute(
        bytes32 _callId,
        Action[] calldata _actions,
        uint256 _allowFailureMap
    )
        external
        override
        auth(EXECUTE_PERMISSION_ID)
        returns (bytes[] memory execResults, uint256 failureMap)
    {
        ...
        for (uint256 i = 0; i < _actions.length; ) {
            address to = _actions[i].to;
            (bool success, bytes memory response) = to.call{value: _actions[i].value}(
                _actions[i].data
            );

            if (!success) {
                // If the call failed and wasn't allowed in allowFailureMap, revert.
                if (!hasBit(_allowFailureMap, uint8(i))) {
                    revert ActionFailed(i);
                }

                // If the call failed, but was allowed in allowFailureMap, store that
                // this specific action has actually failed.
                failureMap = flipBit(failureMap, uint8(i));
            }
            ...
        }
        ...
    }
```

## [03] WHEN `_actions[i].data` IS NOT EMPTY, `DAO.execute` FUNCTION DOES NOT CHECK IF SUCH `_actions[i].data`'S FUNCTION EXISTS IN `_actions[i].to` CONTRACT
After contracts for the same usage are deployed on different chains by a protocol, it is possible that these contracts are somewhat different, such as due to an upgrade. When a contract has a function but the contract for the same usage on the other chain does not have that function while still having a fallback function, the DAO might not notice this and could expect that both contracts are the same, such as because of the lack of clear documentation by the external protocol that owns these contracts. In this situation, when the DAO calls the following `DAO.execute` function with `_actions[i].to` being the contract on the other chain, the corresponding `_actions[i].data`'s function is not found in the `_actions[i].to` contract but the `_actions[i].to` contract's fallback function is triggered. This can result in an unexpected state for the DAO; for example, the `_actions[i].to` contract's fallback function can transfer out the DAO's funds and execute some logics but calling `_actions[i].data`'s function should transfer out the DAO's funds for executing different logics.

As a mitigation, when `_actions[i].data` is not empty, the `DAO.execute` function can be updated to check if such `_actions[i].data`'s function exists in the `_actions[i].to` contract. If such `_actions[i].data`'s function does not exist, the corresponding call should be considered as a failure and can be processed based on the specified `_allowFailureMap`.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L168-L215
```solidity
    function execute(
        bytes32 _callId,
        Action[] calldata _actions,
        uint256 _allowFailureMap
    )
        external
        override
        auth(EXECUTE_PERMISSION_ID)
        returns (bytes[] memory execResults, uint256 failureMap)
    {
        ...
        for (uint256 i = 0; i < _actions.length; ) {
            address to = _actions[i].to;
            (bool success, bytes memory response) = to.call{value: _actions[i].value}(
                _actions[i].data
            );

            if (!success) {
                // If the call failed and wasn't allowed in allowFailureMap, revert.
                if (!hasBit(_allowFailureMap, uint8(i))) {
                    revert ActionFailed(i);
                }

                // If the call failed, but was allowed in allowFailureMap, store that
                // this specific action has actually failed.
                failureMap = flipBit(failureMap, uint8(i));
            }
            ...
        }
        ...
    }
```

## [04] `PermissionManager.revoke` TRANSACTION CAN BE FRONTRUN
Calling the following `PermissionManager.revoke` function, which further calls the `PermissionManager._revoke` function, would revoke the permission from an address for calling the corresponding functions on a target contract, such as when such address has become untrusted. However, such address can monitor the mempool and frontruns the relevant `PermissionManager.revoke` transaction so it can call the corresponding functions on the target contract in a malicious manner before losing the permission. For example, when the address, who has the permission associated with `REGISTER_STANDARD_CALLBACK_PERMISSION_ID`, becomes compromised or malicious, this address can frontrun the DAO's `PermissionManager.revoke` transaction to call the `DAO.registerStandardCallback` function with the `_callbackSelector` input being `IERC721ReceiverUpgradeable.onERC721Received.selector` and the `_magicNumber` input being a `bytes4` that is not `IERC721ReceiverUpgradeable.onERC721Received.selector`; then, the safe-transfers of any ERC721 tokens to the DAO can fail.

As a mitigation, flashbots can be used to keep the `PermissionManager.revoke` transactions away from the mempool for counteracting frontrunning.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L135-L141
```solidity
    function revoke(
        address _where,
        address _who,
        bytes32 _permissionId
    ) external virtual auth(ROOT_PERMISSION_ID) {
        _revoke(_where, _who, _permissionId);
    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L282-L289
```solidity
    function _revoke(address _where, address _who, bytes32 _permissionId) internal virtual {
        bytes32 permHash = permissionHash(_where, _who, _permissionId);
        if (permissionsHashed[permHash] != UNSET_FLAG) {
            permissionsHashed[permHash] = UNSET_FLAG;

            emit Revoked(_permissionId, msg.sender, _where, _who);
        }
    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L309-L317
```solidity
    function registerStandardCallback(
        bytes4 _interfaceId,
        bytes4 _callbackSelector,
        bytes4 _magicNumber
    ) external override auth(REGISTER_STANDARD_CALLBACK_PERMISSION_ID) {
        _registerInterface(_interfaceId);
        _registerCallback(_callbackSelector, _magicNumber);
        emit StandardCallbackRegistered(_interfaceId, _callbackSelector, _magicNumber);
    }
```

## [05] WHETHER `PermissionManager._grantWithCondition` FUNCTION SHOULD REVERT WHEN `_where == ANY_ADDR` OR `_who == ANY_ADDR` IS TRUE NEEDS TO BE RESOLVED
https://devs.aragon.org/docs/osx/how-it-works/core/permissions/#granting-permission-to-any_addr states that "by granting the `USE_PERMISSION_ID` to `_who: ANY_ADDR` on the contract `_where: serviceAddr` you allow everyone to call the `use()` function and you can add more conditions to it"; yet, this documentation does not indicate that condition(s) must be used in this case. However, https://github.com/code-423n4/2023-03-aragon#grant-with-condition-with-any_addr states that "we only allow setting `who`/ `where` to `ANY_ADDR` if `oracle` is present", where `oracle` seems to mean condition(s); this is matched by the code in which calling the following `PermissionManager._grantWithCondition` function would execute `revert ConditionNotPresentForAnyAddress()` if `_where == ANY_ADDR || _who == ANY_ADDR` is true.

If https://github.com/code-423n4/2023-03-aragon#grant-with-condition-with-any_addr and the `PermissionManager._grantWithCondition` function are accurate, then please update https://devs.aragon.org/docs/osx/how-it-works/core/permissions/#granting-permission-to-any_addr to explicitly indicate that condition(s) must be used in this situation to avoid confusions for users and prevent future disputes. Otherwise, the `PermissionManager._grantWithCondition` function needs to be updated to not revert if `_where == ANY_ADDR || _who == ANY_ADDR` is true because granting permission without condition(s) in this case would be allowed.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L230-L275
```solidity
    function _grantWithCondition(
        address _where,
        address _who,
        bytes32 _permissionId,
        IPermissionCondition _condition
    ) internal virtual {
        if (_where == ANY_ADDR && _who == ANY_ADDR) {
            revert AnyAddressDisallowedForWhoAndWhere();
        }

        if (_where == ANY_ADDR || _who == ANY_ADDR) {
            bool isRestricted = isPermissionRestrictedForAnyAddr(_permissionId);
            if (_permissionId == ROOT_PERMISSION_ID || isRestricted) {
                revert PermissionsForAnyAddressDisallowed();
            }

            if (address(_condition) == ALLOW_FLAG) {
                revert ConditionNotPresentForAnyAddress();
            }
        }

        ...
    }
```

## [06] `DAO` CONTRACT'S `receive()` CAN BE UPDATED TO CALL `DAO.deposit` FUNCTION WITH `_token` INPUT BEING `address(0)`
The `DAO` contract's `receive()` currently emits the `NativeTokenDeposited` event. Yet, the `DAO.deposit` function covers the same purpose of depositing ETH into the DAO, which emits another event that is `Deposited`. Similar to WETH's [fallback function](https://etherscan.io/token/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2#code#L35), the `DAO` contract's `receive()` can be updated to call the `DAO.deposit` function with the `_token` input being `address(0)`. In this way, only the `Deposited` event is needed, and the off-chain monitoring can become more efficient.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L263-L265
```solidity
    receive() external payable {
        emit NativeTokenDeposited(msg.sender, msg.value);
    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L218-L236
```solidity
    function deposit(
        address _token,
        uint256 _amount,
        string calldata _reference
    ) external payable override {
        if (_amount == 0) revert ZeroAmount();

        if (_token == address(0)) {
            if (msg.value != _amount)
                revert NativeTokenDepositAmountMismatch({expected: _amount, actual: msg.value});
        } else {
            ...
        }

        emit Deposited(msg.sender, _token, _amount, _reference);
    }
```

## [07] UNLIKE `DAO.deposit`, `DAO` CONTRACT HAS NO FUNCTIONS FOR DEPOSITING ERC721 AND ERC1155 TO DAO
Calling the following `DAO.deposit` function with the `_token` input being not `address(0)` can conveniently deposit an amount of the corresponding ERC20 token to the DAO. Yet, the `DAO` contract has no functions that allow users to directly interact with the DAO for depositing ERC721 and ERC1155 tokens. To provide more convenience to users, please consider adding functions in the `DAO` contract for depositing ERC721 and ERC1155 tokens to the DAO.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L218-L236
```solidity
    function deposit(
        address _token,
        uint256 _amount,
        string calldata _reference
    ) external payable override {
        if (_amount == 0) revert ZeroAmount();

        if (_token == address(0)) {
            ...
        } else {
            if (msg.value != 0)
                revert NativeTokenDepositAmountMismatch({expected: 0, actual: msg.value});

            IERC20Upgradeable(_token).safeTransferFrom(msg.sender, address(this), _amount);
        }

        emit Deposited(msg.sender, _token, _amount, _reference);
    }
```

## [08] `PermissionManager.applyMultiTargetPermissions` FUNCTION ALREADY COVERS USE CASES OF `PermissionManager.applySingleTargetPermissions` FUNCTION
Calling the following `PermissionManager.applySingleTargetPermissions` function can grant or revoke the permission for `item.who` on a single `_where` target contract; yet, calling this function cannot grant the permission for `item.who` on a single `_where` target contract with condition. However, calling the `PermissionManager.applyMultiTargetPermissions` function can also grant or revoke the permission for `item.who` on a single `item.where` target contract; besides that, calling it can also grant the permission for `item.who` on a single `item.where` target contract with condition, which cannot be achieved by calling the `PermissionManager.applySingleTargetPermissions` function. Since the `PermissionManager.applyMultiTargetPermissions` function covers the use cases of the `PermissionManager.applySingleTargetPermissions` function, the `PermissionManager.applySingleTargetPermissions` function can be considered as redundant. If there is no need to keep the `PermissionManager.applySingleTargetPermissions` function, please consider removing it.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L146-L163
```solidity
    function applySingleTargetPermissions(
        address _where,
        PermissionLib.SingleTargetPermission[] calldata items
    ) external virtual auth(ROOT_PERMISSION_ID) {
        for (uint256 i; i < items.length; ) {
            PermissionLib.SingleTargetPermission memory item = items[i];

            if (item.operation == PermissionLib.Operation.Grant) {
                _grant(_where, item.who, item.permissionId);
            } else if (item.operation == PermissionLib.Operation.Revoke) {
                _revoke(_where, item.who, item.permissionId);
            }

            unchecked {
                ++i;
            }
        }
    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L167-L190
```solidity
    function applyMultiTargetPermissions(
        PermissionLib.MultiTargetPermission[] calldata _items
    ) external virtual auth(ROOT_PERMISSION_ID) {
        for (uint256 i; i < _items.length; ) {
            PermissionLib.MultiTargetPermission memory item = _items[i];

            if (item.operation == PermissionLib.Operation.Grant) {
                _grant(item.where, item.who, item.permissionId);
            } else if (item.operation == PermissionLib.Operation.Revoke) {
                _revoke(item.where, item.who, item.permissionId);
            } else if (item.operation == PermissionLib.Operation.GrantWithCondition) {
                _grantWithCondition(
                    item.where,
                    item.who,
                    item.permissionId,
                    IPermissionCondition(item.condition)
                );
            }

            unchecked {
                ++i;
            }
        }
    }
```

## [09] MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS
( Please note that the following instances are not found in https://gist.github.com/Picodes/16984274f6ad7b83b7a59f8b33cee6a6#nc-1-missing-checks-for-address0-when-assigning-values-to-address-state-variables. )

To prevent unintended behaviors, critical address inputs should be checked against `address(0)`.

`address(0)` check is missing for `_initialOwner` in the following constructor. Please consider checking it.
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L104-L119
```solidity
    function initialize(
        bytes calldata _metadata,
        address _initialOwner,
        address _trustedForwarder,
        string calldata daoURI_
    ) external initializer {
        ...
        __PermissionManager_init(_initialOwner);
    }
```

`address(0)` check is missing for `_newTrustedForwarder` in the following function. Please consider checking it.
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L139-L143
```solidity
    function setTrustedForwarder(
        address _newTrustedForwarder
    ) external override auth(SET_TRUSTED_FORWARDER_PERMISSION_ID) {
        _setTrustedForwarder(_newTrustedForwarder);
    }
```

`address(0)` check is missing for `_signatureValidator` in the following function. Please consider checking it.
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L239-L245
```solidity
    function setSignatureValidator(
        address _signatureValidator
    ) external override auth(SET_SIGNATURE_VALIDATOR_PERMISSION_ID) {
        signatureValidator = IERC1271(_signatureValidator);
        ...
    }
```

## [10] REDUNDANT RETURN STATEMENTS FOR FUNCTIONS WITH NAMED RETURNS CAN BE REMOVED
When a function has named returns and a return statement, this return statement becomes redundant. To improve readability and maintainability, the return statements of the following functions can be removed.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L286-L344
```solidity
    function prepareInstallation(
        address _dao,
        PrepareInstallationParams calldata _params
    ) external returns (address plugin, IPluginSetup.PreparedSetupData memory preparedSetupData) {
        ...
        return (plugin, preparedSetupData);
    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L396-L495
```solidity
    function prepareUpdate(
        address _dao,
        PrepareUpdateParams calldata _params
    )
        external
        returns (bytes memory initData, IPluginSetup.PreparedSetupData memory preparedSetupData)
    {
        ...
        return (initData, preparedSetupData);
    }
```

## [11] VULNERABILITIES IN VERSION 4.8.1 OF `@openzeppelin/contracts` AND `@openzeppelin/contracts-upgradeable`
As shown in the following code in `package.json`, version 4.8.1 of `@openzeppelin/contracts` and `@openzeppelin/contracts-upgradeable` can be used. As described in https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts/4.8.1 and https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts-upgradeable/4.8.1, these versions are vulnerable to incorrect calculation for minting NFTs in batches. To reduce the potential attack surface and be more future-proofed, please consider upgrading this package to at least version 4.8.2.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/package.json#L81-L82
```solidity
    "@openzeppelin/contracts": "4.8.1",
    "@openzeppelin/contracts-upgradeable": "4.8.1"
```

## [12] SOLIDITY VERSION `0.8.19` CAN BE USED
As described in https://github.com/ethereum/solidity/releases, Version `0.8.19` is the latest version of Solidity, which "contains a fix for a long-standing bug that can result in code that is only used in creation code to also be included in runtime bytecode". To be more secured and more future-proofed, please consider using Version `0.8.19`. Some files that use Version `0.8.17` currently are shown below.

```solidity
packages\contracts\src\core\dao\DAO.sol
  3: pragma solidity 0.8.17;

packages\contracts\src\core\permission\PermissionLib.sol
  3: pragma solidity 0.8.17;

packages\contracts\src\core\permission\PermissionManager.sol
  3: pragma solidity 0.8.17;

packages\contracts\src\core\utils\CallbackHandler.sol
  3: pragma solidity 0.8.17;
```

## [13] DEFINITIONS OF `UNSET_FLAG` AND `ALLOW_FLAG` ARE INCORRECT IN DOCUMENTATION
https://devs.aragon.org/docs/osx/how-it-works/core/permissions/#permissions shows that for `permissionsHashed`, "the `bytes32` keys are the permission hashes and the address values are either zero-address flags, such as `ALLOW_FLAG = address(0)` and `UNSET_FLAG = address(2)` indicating if the permission is set, or an actual address pointing to a PermissionCondition contract". However, `UNSET_FLAG` is set to `address(0)` and `ALLOW_FLAG` is set to `address(2)` in the `PermissionManager` contract. To avoid misinformation, please consider updating the documentation to match the code.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L20-L24
```solidity
    /// @notice A special address encoding if a permissions is not set and therefore not allowed.
    address internal constant UNSET_FLAG = address(0);

    /// @notice A special address encoding if a permission is allowed.
    address internal constant ALLOW_FLAG = address(2);
```

## [14] `bytes4(0)` CAN BE REPLACED WITH A CONSTANT
To improve readability and maintainability, the following `bytes4(0)` in the `DAO` contract can be replaced with a constant like how the `CallbackHandler` contract does.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L248-L258
```solidity
    function isValidSignature(
        bytes32 _hash,
        bytes memory _signature
    ) external view override(IDAO, IERC1271) returns (bytes4) {
        if (address(signatureValidator) == address(0)) {
            // Return the invalid magic number
            return bytes4(0);
        }
        ...
    }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L14
```solidity
    bytes4 internal constant UNREGISTERED_CALLBACK = bytes4(0);
```

## [15] `NewURI` EVENT CAN BE MOVED TO `IDAO` INTERFACE
Because all other events of the `DAO` contract are included in the `IDAO` interface, the following `NewURI` event can be moved from the `DAO` contract to the `IDAO` interface for better code organization.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L89
```solidity
    event NewURI(string daoURI);
```

## [16] INPUT VARIABLE CAN BE NAMED WITH LEADING UNDERSCORE
The best practice recommends that a function's input variable can be named with a leading underscore. Please consider renaming `items` to `_items` in the following function.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L146-L163
```solidity
    function applySingleTargetPermissions(
        address _where,
        PermissionLib.SingleTargetPermission[] calldata items
    ) external virtual auth(ROOT_PERMISSION_ID) {
        ...
    }
```

## [17] WORD TYPING TYPOS
( Please note that the following instances are not found in https://gist.github.com/Picodes/16984274f6ad7b83b7a59f8b33cee6a6#nc-5-typos. )

`recieves` can be changed to `receives` in the following comments.
```solidity
packages\contracts\src\core\permission\PermissionManager.sol
  217: /// @param _where The address of the target contract for which `_who` recieves permission.
  225: /// @param _where The address of the target contract for which `_who` recieves permission.
  278: /// @param _where The address of the target contract for which `_who` recieves permission.
```

`implecitly` can be changed to `implicitly` in the following comment.
```solidity
packages\contracts\src\framework\dao\DAOFactory.sol
  134: // Revoke Temporarly `ROOT_PERMISSION_ID` from `pluginSetupProcessor` that implecitly granted to this `DaoFactory`
```

## [18] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The following functions miss the `@param` or `@return` comments. Please consider completing the NatSpec comments for these functions.

```solidity
packages\contracts\src\core\dao\DAO.sol
  104: function initialize(

packages\contracts\src\core\dao\IEIP4824.sol
  10: function daoURI() external view returns (string memory _daoURI);

packages\contracts\src\core\utils\CallbackHandler.sol
  31: function _handleCallback(
```