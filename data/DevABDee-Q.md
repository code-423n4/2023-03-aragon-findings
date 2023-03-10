# Codebase Qualitative Analysis:

| Metric | Rating |
| :-----: | :-----: | 
| Test Coverage | Fine | 
| Best Practices | Great |
| Documentation | Great |
| NatSpec Comments | Excellent |
| Code Quality & Complexity| Excellent |

Overall, the Code Quality was found to be of exceptional quality, with an absence of complexity throughout. Code Readability at its finest. It is notable that the NatSpec Comments were composed with a high level of excellence. Furthermore, the documentation was found to be commendable, although opportunities exist for further improvement. The project has largely adhered to prevailing industry standards and best practices, particularly with regard to naming conventions and contract layout. However, the ordering of functions requires attention. Nonetheless, the most critical area for improvement relates to Test coverage, which is presently deficient and necessitates improvement.

#### Quality Rating Scale:
Dead ----------- Poor -------------- Bad -------------- Fine ------------- Good ------------ Great ----------- Excellent
|-------------------|-------------------|-------------------|-------------------|-------------------|-------------------|

## Findings Summary:
| No | Issue |
| :-----: | :-----: |
| L-01 | Lack of Input Validation may cause unexpected behavior for the users |
| L-02 | Record what the contract receives not what it expects to receive |
| L-03 | The permission IDs of permissions native to the DAO Contract CAN be used |
| L-04 | Checks missing to make sure that `_startDate` & `_endDate` is in the present or future |
| L-05 | Storage slot __gap missing in some contracts |
| - | - |
| NC-1 | Constant values such as a call to keccak256(), should used to immutable rather than constant |
| NC-2 | Lack of event emission after critical initialize() function and constructors |
| NC-3 | Functions ordering doesn't comply with the Solidity Style Guidelines |
| NC-4 | Imports not specific |
| NC-5 | Critical address change in one step |
| NC-6 | Function argument/Input name not specified |
| NC-7 | Unbounded Loops |
| - | - |
| I-01 | Insufficient test coverage |
| I-02 | Use a single file system for constants |
| I-03 | Use a single file system for error |
| I-04 | Comments line shouldn't exceed 120 chars |
| I-05 | Goerli & Rinkeby deprecated |

# Low Findings:

## L-01: Lack of Input Validation may cause unexpected behavior for the users
In the function [PulginRepo.createVersion()](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L128), there is a check to make sure that release number is not incremented by more than one:
```solidity
       if (_release - latestRelease > 1) {
            revert InvalidReleaseIncrement({latestRelease: latestRelease, newRelease: _release});
        }
```
If the user input number, `_release` will be smaller than the `latestRelease`, EVM will revert this function without throwing any specific error becuase of the subtraction problem and users are not gonna know why the function is reverting.
#### Migitation
Consider adding a check before this check, to make sure user doesn't inputs a smaller number than the `latestRealease`
```solidity
       require(_release > latestRelease, "Release Lower Than Latest Realease");
``` 
OR
```solidity
       if(_release <= latestRelease) revert ReleaseLowerThanLatestRealease();
```

## L-02: Record what the contract receives not what it expects to receive
Function [Dao.deposit()](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L218), allows users to deposit native protocol tokens (ethers) or any other token. Whatever the user it emits an [event](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L235) `emit Deposited(msg.sender, _token, _amount, _reference);`. The `_amount` is the user's input and it can be ethers or any other token. For ethers it's fine but for other ERC20 tokens, it can be problematic/incorrect, because of some malicious/fee-on-transfer ERC20 tokens like USDT. It should emit the `_amount` that the contract receives not what it expects to receive.
#### Migitation:
Calculate the before and after balance and then emit that amount:
```solidity
        uint256 balanceBefore = IERC20Upgradeable(_token).balanceOf(address(this));
        IERC20Upgradeable(_token).safeTransferFrom(msg.sender, address(this), _amount);
        uint256 balanceAfter = IERC20Upgradeable(_token).balanceOf(address(this));
        require(balanceAfter > balanceBefore, "Not Enough Tokens Transferred")
        uint256 balance = balanceAfter - balanceBefore;
        emit Deposited(msg.sender, _token, balance, _reference);
```

## L-03: The permission IDs of permissions native to the DAO Contract CAN be used
In Docs, it's mentioned that:
> the permission IDs of [permissions native to the DAO Contract](https://devs-stg.aragon.org/docs/osx/how-it-works/core/permissions/#permissions-native-to-the-dao-contract) cannot be used. 

In code, this is only true for one ID [`ROOT_PERMISSION_ID`](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/permission/PermissionManager.sol#L241). Users can set other DAO native permission IDs. Even The following test fails if we add other DAO native permission IDs in the `RESTRICTED_PERMISSIONS_FOR_ANY_ADDR` Array:
```ts
    const ROOT_PERMISSION_ID = ethers.utils.id('ROOT_PERMISSION'); 
    const ADMIN_PERMISSION_ID = ethers.utils.id('ADMIN_PERMISSION');
+ const EXECUTE_PERMISSION_ID = ethers.utils.id('EXECUTE_PERMISSION_ID');
    const RESTRICTED_PERMISSIONS_FOR_ANY_ADDR = [
      ROOT_PERMISSION_ID,
+    EXECUTE_PERMISSION_ID,
      ethers.utils.id('TEST_PERMISSION_1'),
      ethers.utils.id('TEST_PERMISSION_2'),
];

    it('reverts if permissionId is restricted and `_who = ANY_ADDR` or `_where = ANY_ADDR`', async () => {
      for (let i = 0; i < RESTRICTED_PERMISSIONS_FOR_ANY_ADDR.length; i++) {
        await expect(
          pm.grant(pm.address, ANY_ADDR, RESTRICTED_PERMISSIONS_FOR_ANY_ADDR[i])
        ).to.be.revertedWithCustomError(
          pm,
          'PermissionsForAnyAddressDisallowed'
        );
        await expect(
          pm.grant(ANY_ADDR, pm.address, RESTRICTED_PERMISSIONS_FOR_ANY_ADDR[i])
        ).to.be.revertedWithCustomError(
          pm,
          'PermissionsForAnyAddressDisallowed'
        );
      }
```

## L-04: Checks missing to make sure that `_startDate` & `_endDate` is in the present or future
These functions, [Multisig.createProposal()](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L205),  [TokenVoting.createProposal()](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L71), [AddresslistVoting.createProposal()](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L83) & [MajorityVotingBase._validateProposalDates()](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L564) doesn't make sure that `_startDate`/`_start` & `_endDate`/`_end` is in the present/future. User's can input a timestamp that was in the past.

#### Mitigation:
Consider adding this check on all instances: 
```solidity
require(_startDate >= block.timestamp && _endDate > block.timestamp, "PAST TIME")
```

## L-05: Storage slot __gap missing in some contracts
In almost every upgradeable contract, the Storage slot __gap is defined but it's missing in these uppgradeable contracts:
```
src\framework\plugin\repo\PluginRepo.sol:

src\plugins\governance\admin\Admin.sol:

src\token\ERC20\governance\GovernanceERC20.sol:

src\token\ERC20\governance\GovernanceWrappedERC20.sol:
```


# Non-Critical Findings:

## NC-1: Constant values such as a call to keccak256(), should used to immutable rather than constant
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.
While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.
There are many instances of this issue:
```solidity
src\core\dao\DAO.sol:
  44:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");
  47:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");
  50:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");

src\core\permission\PermissionManager.sol:
  17:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

src\core\plugin\PluginUUPSUpgradeable.sol:
  35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");


src\framework\dao\DAORegistry.sol:
  17:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

src\framework\plugin\repo\PluginRepo.sol:
  50:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");
  53:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

src\framework\plugin\setup\PluginSetupProcessor.sol:
  32:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

src\plugins\counter-example\MultiplyHelper.sol:
  12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src\plugins\counter-example\v1\CounterV1.sol:
  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src\plugins\counter-example\v2\CounterV2.sol:
  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

src\plugins\token\MerkleMinter.sol:
  24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

src\token\ERC20\governance\GovernanceERC20.sol:
  28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");
```

## NC-2: Lack of event emission after critical initialize() function and constructors
To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the initialize() function and constructor:
Instances: **All Contracts**.
```solidity
    function initialize(
        bytes calldata _metadata,
        address _initialOwner,
        address _trustedForwarder,
        string calldata daoURI_
    ) external initializer {
        _registerInterface(type(IDAO).interfaceId);
        _registerInterface(type(IERC1271).interfaceId);
        _registerInterface(type(IEIP4824).interfaceId);
        _registerTokenInterfaces();

        _setMetadata(_metadata);
        _setTrustedForwarder(_trustedForwarder);
        _setDaoURI(daoURI_);
        __PermissionManager_init(_initialOwner);

      /// @audit NC EVENT EMITION MISSING
    }
```

## NC-3: Functions ordering doesn't comply with the Solidity Style Guidelines
Almost all the contracts do not follow this [Solidity Style Guidelines Function ordering](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions)
```
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- view and pure functions
```
POC: [recieve & fallback](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L263) functions defined in the last. And in other contracts [view/pure](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L122) functions are defined at the start.

## NC-4: Imports not specific
A really good practice of specifically named imports is followed throughout the codebase except for a few contracts. good practices should be followed to the graves. Make these import statements name specified as well:
```solidity
src\core\dao\DAO.sol:
  7 import "@openzeppelin/contracts-upgradeable/utils/introspection/ERC165StorageUpgradeable.sol";  

src\core\plugin\proposal\ProposalUpgradeable.sol:
  9  import "./IProposal.sol";

src\framework\utils\ens\ENSMigration.sol:
  8  import "@ensdomains/ens-contracts/contracts/registry/ENSRegistry.sol";

src\framework\utils\ens\ENSSubdomainRegistrar.sol:
  6  import "@ensdomains/ens-contracts/contracts/registry/ENS.sol";
```

## NC-5: Critical address change in one step
The lack of 2-step procedure for critical operations leaves them error-prone. Consider adding a two-step procedure on the critical functions. `_setTrustedForwarder()` and then `_acceptTrustedForwarder()`.
```solidity
    function _setTrustedForwarder(address _trustedForwarder) internal {
        trustedForwarder = _trustedForwarder;

        emit TrustedForwarderSet(_trustedForwarder);
    }
```

## NC-6: Function argument/Input name not specified:
```solidity
src\core\plugin\PluginUUPSUpgradeable.sol:
  63      function _authorizeUpgrade(address) ...

src\framework\plugin\repo\PluginRepo.sol:
  266      function _authorizeUpgrade(address) ...

src\framework\utils\InterfaceBasedRegistry.sol:
  56      function _authorizeUpgrade(address) ...

src\framework\utils\ens\ENSSubdomainRegistrar.sol:
  77      function _authorizeUpgrade(address) ...
```

## NC-7: Unbounded Loops
For [DAO.execute](https://github.com/code-423n4/2023-03-aragon/blob/4db573870aa4e1f40a3381cdd4ec006222e471fe/packages/contracts/src/core/dao/DAO.sol#L168) function, there is a great practice is followed for the `for` loop. That `for` loop is bounded using `MAX_ACTIONS`. This practice should be followed for other `for` loops as well. Bound these functions' `for` loops as well:
```solidity
src\core\permission\PermissionManager.sol:
  151      function applySingleTargetPermissions(

  173      function applyMultiTargetPermissions(

src\framework\dao\DAOFactory.sol:
  66      function createDao(

src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol:
  84      function createProposal(

src\plugins\governance\majority-voting\token\TokenVoting.sol:
  74      function createProposal(

src\plugins\governance\multisig\Multisig.sol:
  207      function createProposal(

src\plugins\utils\Addresslist.sol:
  60      function _addAddresses(address[] calldata _newAddresses) internal virtual {
```

# Informational Findings:
### I-01: Insufficient test coverage
The test coverage rate of the project is ~80%. Testing all functions is best practice in terms of security criteria.
Consider 100% or at least +95% Test coverage

### I-02: Use a single file system for constants
Consider using a single file system for constants to further improve code readability

### I-03: Use a single file system for error
Consider using a single file system for custom erros to further improve code readability

### I-04: Comments line shouldn't exceed 120 characters.
Many Comments exceed the 120 chars limit:
```solidity
src\core\dao\DAO.sol:
  141      /// @notice Internal method authorizing the upgrade of the contract via the [upgradeabilty mechanism for UUPS proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable) (see [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822)).

src\core\dao\IDAO.sol:
  20      /// @notice Checks if an address has permission on a contract via a permission identifier and considers if `ANY_ADDRESS` was used in the granting process.

  42      /// @notice Executes a list of actions. If no failure map is provided, one failing action results in the entire excution to be reverted. If a non-zero failure map is provided, allowed actions can fail without the remaining actions being reverted.

src\core\permission\IPermissionCondition.sol:
  8  /// @notice This interface can be implemented to support more customary permissions depending on on- or off-chain state, e.g., by querying token ownershop or a secondary condition, respectively.

src\core\permission\PermissionLib.sol:
  33      /// @notice A struct containing the information for a permission to be applied on multiple target contracts, optionally, with a conditon.

  38      /// @param condition The `PermissionCondition` that will be asked for authorization on calls connected to the specified permission identifier.

src\core\permission\PermissionManager.sol:

   29      /// @notice A mapping storing permissions as hashes (i.e., `permissionHash(where, who, permissionId)`) and their status encoded by an address (unset, allowed, or redirecting to a `PermissionCondition`).

  236      /// @dev Note, that granting permissions with `_who` or `_where` equal to `ANY_ADDR` does not replace other permissions with specific `_who` and `_where` addresses that exist in parallel.

src\framework\plugin\setup\PluginSetupProcessor.sol:
  652      /// @dev If the block number stored in `states[pluginInstallationId].blockNumber` exceeds the one stored in `pluginState.preparedSetupIdToBlockNumber[preparedSetupId]`, the prepared setup with `preparedSetupId` is outdated and not applicable anymore.

src\plugins\governance\admin\Admin.sol:

  62      /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.

```

### I-05: Goerli & Rinkeby deprecated
Rinkeby Deprecated already and Goerli will be deprecated soon InShaaAllah. Consider active ETH testnet `Sepolia`
```ts
hardhat.config.ts:
  80        rinkeby: process.env.ETHERSCAN_KEY || '',
  81        goerli: process.env.ETHERSCAN_KEY || '',
```