## 1. `keccak256()` EXPRESSIONS WHICH ARE FIXED, CAN BE PRECALCULATED AND ASSIGNED TO THE CONSTANT VARIABLES.

Instead of calculating a statevar with keccak256() every time the contract is made, pre calculate them before and only give the result to a constant

    /// @notice The ID of the permission required to call the `execute` function.
    bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");

    /// @notice The ID of the permission required to call the `_authorizeUpgrade` function.
    bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");

    /// @notice The ID of the permission required to call the `setMetadata` function.
    bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L39-L58
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L15
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49-L52
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16-L17
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27-L35
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18-L19
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L14

## 2. CHECK FOR THE CONTRACT EXISTENCE USING `extcodesize` IF USING LOW LEVEL `call` FUNCTION TO SEND ETHER TO CONTRACTS WITH `recieve()` FUNCTIONS.

            (bool success, bytes memory response) = to.call{value: _actions[i].value}(

In the above code if the `to` address is a contract, and if does not include any code in it, it will still return `success = true`.
Hence it is best practice to check for the contract existence before sending ether to a contract using low level `call()` function

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L186

## 3. USE BOTH OLD AND NEW VALUES WHEN EMITTING EVENTS FOR CRITICAL PARAMETER UPDATES

For the use of off-chain tools it is recommended to emit both old and new values when critical parameters are updated in the protocol.

    function _setDaoURI(string calldata daoURI_) internal {
        _daoURI = daoURI_;

        emit NewURI(daoURI_);
    }

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L335
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L286

Add both old and new values to the event emits of the other setter functions as well.

## 4. AS A SECURITY MEASURE CALL THE `_disableInitializers()` INSIDE THE `constructor()` TO PREVENT AN ATTACKER FROM RE-INITIALIZING THE IMPLEMENTATION CONTRACT IN `UUPSUpgradeable` CONTRACTS 

Add the following code snippet to the `DaoAuthorizableUpgradeable` UUPSUpgradeable contract.

    constructor() {
        _disableInitializers();
    }
	
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L14-L16