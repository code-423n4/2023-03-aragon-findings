### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] |Remove the `initializer` modifier| 14 |
| [G-02] |Use hardcode address instead ``address(this)``| 19 |
| [G-03] |State variables only set in the constructor should be declared ``immutable`` |2 |
| [G-04] |Remove unused struct | 1 |
| [G-05] |Function Calls in a Loop Can Cause Denial of Service and out of gas due to not checking the Array length |1|
| [G-06] |Using `storage` instead of `memory` for `structs/arrays` saves gas| 8 |
| [G-07] |Multiple ``address``/ID mappings can be combined into a single ``mapping`` of an ``address``/ID to a ``struct``, where appropriates | 3 |
| [G-08] | Multiple accesses of a mapping/array should use a local variable caches | 6 |
| [G-09] | Empty blocks should be removed or emit something | 9 |
| [G-10] |Don't use _msgSender() if not supporting EIP-2771 | 14 |
| [G-11] | ``bytes`` constants are more eficient than ``string`` constans |10 |
| [G-12] |Change ``public`` function visibility to ``external`` | 8 |
| [G-13] |Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | 3 |
| [G-14] |``internal`` functions only called once can be inlined to save gas |27 |
| [G-15] |Do not calculate constants variables | 34 |
| [G-16] |Use ``assembly`` to write _address storage values_  |17 |
| [G-17] |Setting the _constructor_ to `payable` | 21 |
| [G-18] |Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement |1 |
| [G-19] |Use nested if and, avoid multiple check combinations|11|
| [G-20] |Sort Solidity operations using short-circuit mode | 2 |
| [G-21] |Optimize names to save gas | All contracts |
| [G-22] |Use a more recent version of solidity | All contracts |

Total 22 issues

### [G-01] Remove the `initializer` modifier

if we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn't need to worry about it getting called again. 

14 results - 14 files:
```solidity
src\core\dao\DAO.sol:

  109:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L109


```solidity
src\framework\dao\DAORegistry.sol:

  40:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L40


```solidity
src\framework\plugin\repo\PluginRepo.sol:

  120:     function initialize(address initialOwner) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L120


```solidity
src\framework\plugin\repo\PluginRepoRegistry.sol:

41:     function initialize(IDAO _dao, ENSSubdomainRegistrar _subdomainRegistrar) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L41

```solidity
src\framework\utils\ens\ENSSubdomainRegistrar.sol:

57:     function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L57

```solidity
src\plugins\counter-example\v1\CounterV1.sol:

  30:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L30

```solidity
src\plugins\governance\admin\Admin.sol:

  29:     function initialize(IDAO _dao) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L29

```solidity
src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol:

  38:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L38

```solidity
src\plugins\governance\majority-voting\token\TokenVoting.sol:

  40:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L40

```solidity
src\plugins\governance\multisig\Multisig.sol:

  129:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L129

```solidity
src\plugins\token\MerkleDistributor.sol:

  50:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L50

```solidity
src\plugins\token\MerkleMinter.sol:

  45:     ) external initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L45

```solidity
src\token\ERC20\governance\GovernanceERC20.sol:

  68:     ) public initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L68

```solidity
src\token\ERC20\governance\GovernanceWrappedERC20.sol:

  50:     ) public initializer {
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L50


In the EVM, the constructor's job is actually to return the bytecode that will live at the contract's address. So, while inside a constructor, your address `(address(this))` will be the deployment address, but there will be no bytecode at that address! So if we check `address(this).code.length` before the constructor has finished, even from within a delegatecall, we will get 0. So now let's update our `initialize()` function to only run if we are inside a constructor:


```diff
src\token\ERC20\governance\GovernanceWrappedERC20.sol:

- 50:     ) public initializer {
+        require(address(this).code.length == 0, 'not in constructor’);

```

Now the Proxy contract's constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero. 

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!

### [G-02] Use hardcode address instead ``address(this)``

Instead of ``address(this)``, it is more gas-efficient to pre-calculate and use the address with a hardcode. Foundry's ``script.sol`` and solmate``LibRlp.sol`` contracts can do this.

Reference:
https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824


19 results - 12 files:
```solidity
src\core\dao\DAO.sol:

  232:             IERC20Upgradeable(_token).safeTransferFrom(msg.sender, address(this), _amount);

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L232

```solidity
src\core\permission\PermissionManager.sol:

  213:         _grant(address(this), _initialOwner, ROOT_PERMISSION_ID);

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L213

```solidity
src\core\plugin\dao-authorizable\DaoAuthorizable.sol:

  32:         _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L32

```solidity
src\core\plugin\dao-authorizable\DaoAuthorizableUpgradeable.sol:

  33:         _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L33

```solidity
src\framework\dao\DAOFactory.sol:

   90:             address(this),

  130:             address(this),

  136:         createdDao.revoke(address(createdDao), address(this), rootPermissionID);

  148:             address(this),

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L90

```solidity
src\framework\plugin\repo\PluginRepo.sol:

  123:         _grant(address(this), initialOwner, MAINTAINER_PERMISSION_ID);

  124:         _grant(address(this), initialOwner, UPGRADE_REPO_PERMISSION_ID);

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L123-L124


```solidity
src\framework\plugin\repo\PluginRepoFactory.sol:

   53:         pluginRepo = _createPluginRepo(_subdomain, address(this));

   94:             address(this),

   99:             address(this),

  104:             address(this),

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L53

```solidity
src\framework\plugin\setup\PluginSetupProcessor.sol:

  702:             !DAO(payable(_dao)).hasPermission(address(this), msg.sender, _permissionId, bytes(""))

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L702


```solidity
src\framework\utils\TokenFactory.sol:

  89:                 abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L89

```solidity
src\framework\utils\ens\ENSSubdomainRegistrar.sol:

  93:         ens.setSubnodeOwner(node, _label, address(this));

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L93

```solidity
src\plugins\governance\admin\Admin.sol:

  51:                 _where: address(this),

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L51

```solidity
src\plugins\governance\majority-voting\token\TokenVotingSetup.sol:

  278:             abi.encodeWithSelector(IERC20Upgradeable.balanceOf.selector, address(this))

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L278

### [G-03] State variables only set in the constructor should be declared ``immutable``

Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a ``PUSH32`` (**3 gas**).


2 results - 2 files:
```solidity
contracts\src\framework\plugin\repo\PluginRepoFactory.sol:

  15:     PluginRepoRegistry public pluginRepoRegistry;

  22     constructor(PluginRepoRegistry _pluginRepoRegistry) {
  23:         pluginRepoRegistry = _pluginRepoRegistry;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L23

```solidity
contracts\src\framework\plugin\setup\PluginSetupProcessor.sol:
  
  128     PluginRepoRegistry public repoRegistry;

  277     constructor(PluginRepoRegistry _repoRegistry) {
  278:         repoRegistry = _repoRegistry;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L278


### [G-04] Remove unused struct

In the TokenFactory contract, ``MintConfig`` struct is not used in any contract and outside the scope. 

1 result - 1 file:
```solidity
src\framework\utils\TokenFactory.sol:

  62:     struct MintConfig {
  63:         address[] receivers;
  64:         uint256[] amounts;
  65:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L62-L65

### [G-05] Function Calls in a Loop Can Cause Denial of Service and out of gas due to not checking the Array length

Function calls made in an infinite loop are prone to gassing with potential resource exhaustion, as they can trap the contract due to gas limitations or failed transactions. Consider limiting the loop length if the array is expected to grow and/or handle a very large list of items to avoid unnecessary waste of gas and denial of service.


```diff
src/framework/dao/DAOFactory.sol#63:

 function createDao(
        DAOSettings calldata _daoSettings,
        PluginSettings[] calldata _pluginSettings
    ) external returns (DAO createdDao) {
        // Check if no plugin is provided.
-       if (_pluginSettings.length == 0) {
-           revert NoPluginProvided();
-       }
+      if(_pluginSettings.length > maxArrayLength) {
+          revert maxArrayLengt();
+       }

        // Create DAO.
        createdDao = _createDAO(_daoSettings);

        // Register DAO.
        daoRegistry.register(createdDao, msg.sender, _daoSettings.subdomain);

        // Get Permission IDs
        bytes32 rootPermissionID = createdDao.ROOT_PERMISSION_ID();
        bytes32 applyInstallationPermissionID = pluginSetupProcessor
            .APPLY_INSTALLATION_PERMISSION_ID();

        // Grant the temporary permissions.
        // Grant Temporarly `ROOT_PERMISSION` to `pluginSetupProcessor`.
        createdDao.grant(address(createdDao), address(pluginSetupProcessor), rootPermissionID);

        // Grant Temporarly `APPLY_INSTALLATION_PERMISSION` on `pluginSetupProcessor` to this `DAOFactory`.
        createdDao.grant(
            address(pluginSetupProcessor),
            address(this),
            applyInstallationPermissionID
        );

        // Install plugins on the newly created DAO.
        for (uint256 i; i < _pluginSettings.length; ++i) {
            // Prepare plugin.
            (
                address plugin,
                IPluginSetup.PreparedSetupData memory preparedSetupData
            ) = pluginSetupProcessor.prepareInstallation(
                    address(createdDao),
                    PluginSetupProcessor.PrepareInstallationParams(
                        _pluginSettings[i].pluginSetupRef,
                        _pluginSettings[i].data
                    )
                );

            // Apply plugin.
            pluginSetupProcessor.applyInstallation(
                address(createdDao),
                PluginSetupProcessor.ApplyInstallationParams(
                    _pluginSettings[i].pluginSetupRef,
                    plugin,
                    preparedSetupData.permissions,
                    hashHelpers(preparedSetupData.helpers)
                )
            );
        }

        // Set the rest of DAO's permissions.
        _setDAOPermissions(createdDao);

        // Revoke the temporarly granted permissions.
        // Revoke Temporarly `ROOT_PERMISSION` from `pluginSetupProcessor`.
        createdDao.revoke(address(createdDao), address(pluginSetupProcessor), rootPermissionID);

        // Revoke `APPLY_INSTALLATION_PERMISSION` on `pluginSetupProcessor` from this `DAOFactory` .
        createdDao.revoke(
            address(pluginSetupProcessor),
            address(this),
            applyInstallationPermissionID
        );

        // Revoke Temporarly `ROOT_PERMISSION_ID` from `pluginSetupProcessor` that implecitly granted to this `DaoFactory`
        // at the create dao step `address(this)` being the initial owner of the new created DAO.
        createdDao.revoke(address(createdDao), address(this), rootPermissionID);
    }

```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L63-L137


### [G-06] Using `storage` instead of `memory` for `structs/arrays` saves gas

When fetching data from a storage location, assigning the data to a ``memory`` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the ``memory`` keyword, declaring the variable with the ``storage`` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a ``memory`` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires ``memory``, or if the array/struct is being read from another ``memory`` array/struct

8 results - 6 files:
```solidity
src\core\permission\PermissionManager.sol:

  151:    PermissionLib.SingleTargetPermission memory item = items[i];

  171:    PermissionLib.MultiTargetPermission memory item = _items[i];

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L151


```solidity

src\framework\dao\DAOFactory.sol:

  99:     IPluginSetup.PreparedSetupData memory preparedSetupData
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L99


```solidity
src\framework\plugin\repo\PluginRepo.sol:

  167:    Tag memory tag = Tag(_release, build);
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L167


```solidity
src\framework\plugin\setup\PluginSetupProcessor.sol:

  433:    PluginRepo.Version memory currentVersion = _params.pluginSetupRepo.getVersion(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L433


```solidity
src\framework\utils\TokenFactory.sol:

  88:     bytes memory data = token.functionStaticCall(
```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L88


```solidity
src\plugins\governance\majority-voting\token\TokenVotingSetup.sol:

  108:    bool[] memory supportedIds = _getTokenInterfaceIds(token);

  212:    bool[] memory supportedIds = _getTokenInterfaceIds(token);

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L108


### [G-07] Multiple ``address``/ID mappings can be combined into a single ``mapping`` of an ``address``/ID to a ``struct``, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.


3 results - 1 file:
```solidity
src\framework\plugin\repo\PluginRepo.sol:

  55:     mapping(uint8 => uint16) internal buildsPerRelease;
  58:     mapping(bytes32 => Version) internal versions;
  61:     mapping(address => bytes32) internal latestTagHashForPluginSetup;

```

### [G-08] Multiple accesses of a mapping/array should use a local variable cache

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata.

6 results - 2 files:
```solidity
src\framework\plugin\setup\PluginSetupProcessor.sol:

  //@audit preparedSetupIdToBlockNumber[preparedSetupId]

  326:         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {

  330:         pluginState.preparedSetupIdToBlockNumber[preparedSetupId] = block.number;

  590:         if (pluginState.blockNumber < pluginState.preparedSetupIdToBlockNumber[preparedSetupId]) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L326


```solidity
src\plugins\governance\majority-voting\MajorityVotingBase.sol:

  // @audit proposals[_proposalId]

  458:     proposals[_proposalId].executed = true;

  463:     proposals[_proposalId].actions,

  464:     proposals[_proposalId].allowFailureMap

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L458


### [G-09] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.


9 results - 9 files:
```solidity
src\core\dao\DAO.sol:

  136:     function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L136

```solidity
src\core\plugin\PluginUUPSUpgradeable.sol:

  63:     ) internal virtual override auth(UPGRADE_PLUGIN_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L63

```solidity
src\framework\plugin\repo\PluginRepo.sol:

  259:     ) internal virtual override auth(UPGRADE_REPO_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L259

```solidity
src\framework\plugin\setup\PluginSetup.sol:
  18:     function prepareUpdate(
  19:         address _dao,
  20:         uint16 _currentBuild,
  21:         SetupPayload calldata _payload
  22:     )
  23:         external
  24:         virtual
  25:         override
  26:         returns (bytes memory initData, PreparedSetupData memory preparedSetupData)
  27:     {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L18-L27

```solidity
src\framework\utils\InterfaceBasedRegistry.sol:

  56:     ) internal virtual override auth(UPGRADE_REGISTRY_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L56

```solidity
src\framework\utils\ens\ENSSubdomainRegistrar.sol:

  76:     ) internal virtual override auth(UPGRADE_REGISTRAR_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L76


```solidity
src\plugins\counter-example\v1\CounterV1.sol:
  43      /// @notice Executes something on the DAO.
  44:     function execute() public {
  45:         // In order to do this, Count needs permission on the dao (EXEC_ROLE)
  46:         //dao.execute(...)
  47:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L43-L47

```solidity

src\plugins\counter-example\v2\CounterV2.sol:
  62      /// @notice Executes something on the DAO.
  63:     function execute() public {
  64:         // In order to do this, Count needs permission on the dao (EXEC_ROLE)
  65:         //dao.execute(...)
  66:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L63-L66


### [G-10] Don't use _msgSender() if not supporting EIP-2771

Use ``msg.sender`` if the code does not implement EIP-2771 trusted forwarder support.

Reference: https://eips.ethereum.org/EIPS/eip-2771

14 results - 7 files:
```solidity
src\core\plugin\dao-authorizable\DaoAuthorizable.sol:

  32:    _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L32

```solidity
src\core\plugin\dao-authorizable\DaoAuthorizableUpgradeable.sol:

  33:    _auth(dao_, address(this), _msgSender(), _permissionId, _msgData());

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L33

```solidity
src\plugins\governance\admin\Admin.sol:

  70:    _creator: _msgSender(),

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L70

```solidity
src\plugins\governance\majority-voting\MajorityVotingBase.sol:

  270:   address account = _msgSender();

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L270

```solidity
src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol:
 
   97:   if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
   98:      revert ProposalCreationForbidden(_msgSender());

  102:   _creator: _msgSender(),

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97-L98

```solidity
src\plugins\governance\majority-voting\token\TokenVoting.sol:
  
  91:    if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {
  92:       revert ProposalCreationForbidden(_msgSender());

  96:    _creator: _msgSender(),

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L91-L92

```solidity
src\plugins\governance\multisig\Multisig.sol:
 
  216:   if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {

  217:       revert ProposalCreationForbidden(_msgSender());

  231:   _creator: _msgSender(),

  266:   address approver = _msgSender();

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216-L217


### [G-11] ``bytes`` constants are more eficient than ``string`` constans

If the data can fit in 32 bytes, the bytes32 data type can be used instead of bytes or strings, as it is less robust in terms of robustness.


10 results - 5 files:
```solidity
src\core\dao\DAO.sol:

   70:     string private _daoURI;

   89:     event NewURI(string daoURI);

  320:     function daoURI() external view returns (string memory) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L70


```solidity
src\core\dao\IEIP4824.sol:

  10:     function daoURI() external view returns (string memory _daoURI);

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/IEIP4824.sol#L10


```solidity
src\framework\dao\DAOFactory.sol:

  34:         string daoURI;
  35:         string subdomain;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L34-L35


```solidity
src\framework\utils\TokenFactory.sol:

  58:         string name;
  59:         string symbol;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L58-L59


```solidity
src\plugins\governance\majority-voting\token\TokenVotingSetup.sol:

  44:         string name;
  45:         string symbol;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L44-L45

### [G-12] Change ``public`` function visibility to ``external``

Since the following public functions are not called from within the contract, their visibility can be made external for gas optimization.

8 results - 7 files:
```solidity
src\framework\plugin\repo\PluginRepo.sol:

  244:     function buildCount(uint8 _release) public view returns (uint256) {
  245:         return buildsPerRelease[_release];
  246:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L244-L246

```solidity
src\plugins\counter-example\v1\CounterV1.sol:

  44:     function execute() public {
  45:         // In order to do this, Count needs permission on the dao (EXEC_ROLE)
  46:         //dao.execute(...)
  47:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L44-L47


```solidity
src\plugins\counter-example\v2\CounterV2.sol:

  63:     function execute() public {
  64:         // In order to do this, Count needs permission on the dao (EXEC_ROLE)
  65:         //dao.execute(...)
  66:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L63-L66

```solidity
src\plugins\governance\majority-voting\MajorityVotingBase.sol:

  291:     function getVoteOption(
  292:         uint256 _proposalId,
  293:         address _voter
  294:     ) public view virtual returns (VoteOption) {
  295:         return proposals[_proposalId].voters[_voter];
  296:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L291-L296

```solidity
src\plugins\governance\multisig\Multisig.sol:

  328:     function hasApproved(uint256 _proposalId, address _account) public view returns (bool) {
  329:         return proposals[_proposalId].approvers[_account];
  330:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L328-L330

```solidity
src\plugins\token\MerkleDistributor.sol:

  83:     function unclaimedBalance(
  84:         uint256 _index,
  85:         address _to,
  86:         uint256 _amount,
  87:         bytes32[] memory _proof
  88:     ) public view override returns (uint256) {
  89:         if (isClaimed(_index)) return 0;
  90:         return _verifyBalanceOnTree(_index, _to, _amount, _proof) ? _amount : 0;
  91:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L83-L91

```solidity
src\token\ERC20\governance\GovernanceWrappedERC20.sol:

  81:     function depositFor(
  82:         address account,
  83:         uint256 amount
  84:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {
  85:         return ERC20WrapperUpgradeable.depositFor(account, amount);
  86:     }


  89:     function withdrawTo(
  90:         address account,
  91:         uint256 amount
  92:     ) public override(IGovernanceWrappedERC20, ERC20WrapperUpgradeable) returns (bool) {
  93:         return ERC20WrapperUpgradeable.withdrawTo(account, amount);
  94:     }

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L81-L86


### [G-13] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contracts gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 

Use a larger size then downcast where needed.

3 results 3 files:
```solidity
contracts\src\framework\plugin\repo\PluginRepo.sol:

  // @audit uint8 _release
  143:         if (_release - latestRelease > 1) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L143


```solidity
contracts\src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol:

  // @audit uint64 snapshotBlock
  94:             snapshotBlock = block.number.toUint64() - 1;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L94


```solidity
contracts\src\plugins\governance\multisig\Multisig.sol:

  // @audit uint64 _startDate
  221:             _startDate = block.timestamp.toUint64();

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L221


### [G-14] ``internal`` functions only called once can be inlined to save gas

Not inlining costs **20** to **40** gas because of two extra ``JUMP`` instructions and additional stack operations needed for function calls.

27 results - 16 files:
```solidity
contracts\src\core\dao\DAO.sol:

  122     function isPermissionRestrictedForAnyAddr(
  123         bytes32 _permissionId
  124:     ) internal pure override returns (bool) {

  136:     function _authorizeUpgrade(address) internal virtual override auth(UPGRADE_DAO_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L122-L132


```solidity
contracts\src\core\permission\PermissionManager.sol:

  95:     function __PermissionManager_init(address _initialOwner) internal onlyInitializing {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L95


```solidity
contracts\src\core\plugin\PluginCloneable.sol:

  22:     function __PluginCloneable_init(IDAO _dao) internal virtual onlyInitializing {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L22


```solidity
contracts\src\core\plugin\PluginUUPSUpgradeable.sol:

  39:     function __PluginUUPSUpgradeable_init(IDAO _dao) internal virtual onlyInitializing {

  61     function _authorizeUpgrade(
  62         address
  63:     ) internal virtual override auth(UPGRADE_PLUGIN_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L39


```solidity
contracts\src\core\plugin\dao-authorizable\DaoAuthorizableUpgradeable.sol:

  20:     function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L20


```solidity
contracts\src\core\plugin\proposal\Proposal.sol:

  45     function _createProposal(
  46         address _creator,
  47         bytes calldata _metadata,
  48         uint64 _startDate,
  49         uint64 _endDate,
  50         IDAO.Action[] calldata _actions,
  51         uint256 _allowFailureMap
  52:     ) internal virtual returns (uint256 proposalId) {

  72     function _executeProposal(
  73         IDAO _dao,
  74         uint256 _proposalId,
  75         IDAO.Action[] memory _actions,
  76         uint256 _allowFailureMap
  77:     ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L45-L52


```solidity
contracts\src\core\plugin\proposal\ProposalUpgradeable.sol:

  45     function _createProposal(
  46         address _creator,
  47         bytes calldata _metadata,
  48         uint64 _startDate,
  49         uint64 _endDate,
  50         IDAO.Action[] calldata _actions,
  51         uint256 _allowFailureMap
  52:     ) internal virtual returns (uint256 proposalId) {

  72     function _executeProposal(
  73         IDAO _dao,
  74         uint256 _proposalId,
  75         IDAO.Action[] memory _actions,
  76         uint256 _allowFailureMap
  77:     ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L45-L52

```solidity
contracts\src\core\utils\CallbackHandler.sol:

  31     function _handleCallback(
  32         bytes4 _callbackSelector,
  33         bytes memory _data
  34:     ) internal virtual returns (bytes4) {

  48:     function _registerCallback(bytes4 _callbackSelector, bytes4 _magicNumber) internal virtual {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L31-L34

```solidity
contracts\src\framework\plugin\repo\PluginRepo.sol:

  257     function _authorizeUpgrade(
  258         address
  259:     ) internal virtual override auth(UPGRADE_REPO_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L257-L259

```solidity
contracts\src\framework\plugin\setup\PluginSetup.sol:

  33     function createERC1967Proxy(
  34         address _implementation,
  35         bytes memory _data
  36:     ) internal returns (address) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetup.sol#L33-L36

```solidity
contracts\src\framework\utils\InterfaceBasedRegistry.sol:

  43     function __InterfaceBasedRegistry_init(
  44         IDAO _managingDao,
  45         bytes4 _targetInterfaceId
  46:     ) internal virtual onlyInitializing {

  54     function _authorizeUpgrade(
  55         address
  56:     ) internal virtual override auth(UPGRADE_REGISTRY_PERMISSION_ID) {}

  61:     function _register(address _registrant) internal {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L43-L46

```solidity
contracts\src\framework\utils\ens\ENSSubdomainRegistrar.sol:

  74     function _authorizeUpgrade(
  75         address
  76:     ) internal virtual override auth(UPGRADE_REGISTRAR_PERMISSION_ID) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L74-L76

```solidity
contracts\src\plugins\governance\majority-voting\MajorityVotingBase.sol:

  238     function __MajorityVotingBase_init(
  239         IDAO _dao,
  240         VotingSettings calldata _votingSettings
  241:     ) internal onlyInitializing {

  564     function _validateProposalDates(
  565         uint64 _start,
  566         uint64 _end
  567:     ) internal view virtual returns (uint64 startDate, uint64 endDate) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L238-L241

```solidity
contracts\src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol:

  148     function _vote(
  149         uint256 _proposalId,
  150         VoteOption _voteOption,
  151         address _voter,
  152         bool _tryEarlyExecution
  153:     ) internal override {

  191     function _canVote(
  192         uint256 _proposalId,
  193         address _account,
  194         VoteOption _voteOption
  195:     ) internal view override returns (bool) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L148-L153

```solidity
contracts\src\plugins\governance\majority-voting\token\TokenVoting.sol:

  143     function _vote(
  144         uint256 _proposalId,
  145         VoteOption _voteOption,
  146         address _voter,
  147         bool _tryEarlyExecution
  148:     ) internal override {

  188     function _canVote(
  189         uint256 _proposalId,
  190         address _account,
  191         VoteOption _voteOption
  192:     ) internal view override returns (bool) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L143-L148

```solidity
contracts\src\plugins\utils\Addresslist.sol:

  59:     function _addAddresses(address[] calldata _newAddresses) internal virtual {

  77:     function _removeAddresses(address[] calldata _exitingAddresses) internal virtual {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L59


### [G-15] Do not calculate constants variables

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

34 results - 20 files:
```solidity
contracts\src\core\dao\DAO.sol:

  40:     bytes32 public constant EXECUTE_PERMISSION_ID = keccak256("EXECUTE_PERMISSION");

  43:     bytes32 public constant UPGRADE_DAO_PERMISSION_ID = keccak256("UPGRADE_DAO_PERMISSION");

  46:     bytes32 public constant SET_METADATA_PERMISSION_ID = keccak256("SET_METADATA_PERMISSION");

  49:     bytes32 public constant SET_TRUSTED_FORWARDER_PERMISSION_ID =
  50          keccak256("SET_TRUSTED_FORWARDER_PERMISSION");

  53:     bytes32 public constant SET_SIGNATURE_VALIDATOR_PERMISSION_ID =
  54          keccak256("SET_SIGNATURE_VALIDATOR_PERMISSION");

  57:     bytes32 public constant REGISTER_STANDARD_CALLBACK_PERMISSION_ID =
  58          keccak256("REGISTER_STANDARD_CALLBACK_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L40-L58


```solidity
contracts\src\core\permission\PermissionManager.sol:

  15:     bytes32 public constant ROOT_PERMISSION_ID = keccak256("ROOT_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L15


```solidity
contracts\src\core\plugin\PluginUUPSUpgradeable.sol:

  35:     bytes32 public constant UPGRADE_PLUGIN_PERMISSION_ID = keccak256("UPGRADE_PLUGIN_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L35


```solidity
contracts\src\core\utils\CallbackHandler.sol:

  14:     bytes4 internal constant UNREGISTERED_CALLBACK = bytes4(0);

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L14


```solidity
contracts\src\framework\dao\DAORegistry.sol:

  15:     bytes32 public constant REGISTER_DAO_PERMISSION_ID = keccak256("REGISTER_DAO_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L15


```solidity
contracts\src\framework\plugin\repo\PluginRepo.sol:

  49:     bytes32 public constant MAINTAINER_PERMISSION_ID = keccak256("MAINTAINER_PERMISSION");

  52:     bytes32 public constant UPGRADE_REPO_PERMISSION_ID = keccak256("UPGRADE_REPO_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L49-L52


```solidity
contracts\src\framework\plugin\repo\PluginRepoRegistry.sol:

  16:     bytes32 public constant REGISTER_PLUGIN_REPO_PERMISSION_ID =
  17          keccak256("REGISTER_PLUGIN_REPO_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L16-L17


```solidity
contracts\src\framework\plugin\setup\PluginSetupProcessor.sol:

  27:     bytes32 public constant APPLY_INSTALLATION_PERMISSION_ID =
  28          keccak256("APPLY_INSTALLATION_PERMISSION");

  31:     bytes32 public constant APPLY_UPDATE_PERMISSION_ID = keccak256("APPLY_UPDATE_PERMISSION");

  34:     bytes32 public constant APPLY_UNINSTALLATION_PERMISSION_ID =
  35          keccak256("APPLY_UNINSTALLATION_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L27-L35

```solidity
contracts\src\framework\utils\InterfaceBasedRegistry.sol:

  18:     bytes32 public constant UPGRADE_REGISTRY_PERMISSION_ID =
  19          keccak256("UPGRADE_REGISTRY_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/InterfaceBasedRegistry.sol#L18-L19


```solidity
contracts\src\framework\utils\ens\ENSSubdomainRegistrar.sol:

  18:     bytes32 public constant UPGRADE_REGISTRAR_PERMISSION_ID =
  19          keccak256("UPGRADE_REGISTRAR_PERMISSION");

  22:     bytes32 public constant REGISTER_ENS_SUBDOMAIN_PERMISSION_ID =
  23          keccak256("REGISTER_ENS_SUBDOMAIN_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L18-L23


```solidity
contracts\src\plugins\counter-example\MultiplyHelper.sol:

  12:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

contracts\src\plugins\counter-example\v1\CounterV1.sol:

  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/MultiplyHelper.sol#L12-L14

```solidity

contracts\src\plugins\counter-example\v2\CounterV2.sol:

  14:     bytes32 public constant MULTIPLY_PERMISSION_ID = keccak256("MULTIPLY_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L14

```solidity
contracts\src\plugins\governance\admin\Admin.sol:

  19:     bytes4 internal constant ADMIN_INTERFACE_ID =
  20          this.initialize.selector ^ this.executeProposal.selector;

  23:     bytes32 public constant EXECUTE_PROPOSAL_PERMISSION_ID =
  24          keccak256("EXECUTE_PROPOSAL_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/Admin.sol#L19-L24

```solidity
contracts\src\plugins\governance\majority-voting\MajorityVotingBase.sol:

  173:     bytes4 internal constant MAJORITY_VOTING_BASE_INTERFACE_ID =
  174          this.minDuration.selector ^
  175              this.minProposerVotingPower.selector ^
  176              this.votingMode.selector ^
  177              this.totalVotingPower.selector ^
  178              this.getProposal.selector ^
  179              this.updateVotingSettings.selector ^
  180              this.createProposal.selector;

  183:     bytes32 public constant UPDATE_VOTING_SETTINGS_PERMISSION_ID =
  184          keccak256("UPDATE_VOTING_SETTINGS_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L173-L184

```solidity
contracts\src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol:

  23:     bytes4 internal constant ADDRESSLIST_VOTING_INTERFACE_ID =
  24          this.initialize.selector ^ this.addAddresses.selector ^ this.removeAddresses.selector;

  27:     bytes32 public constant UPDATE_ADDRESSES_PERMISSION_ID =
  28          keccak256("UPDATE_ADDRESSES_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L23-L28


```solidity
contracts\src\plugins\governance\multisig\Multisig.sol:

  64:     bytes4 internal constant MULTISIG_INTERFACE_ID =
  65          this.initialize.selector ^
  66              this.updateMultisigSettings.selector ^
  67              this.createProposal.selector ^
  68              this.getProposal.selector;
   
  71:     bytes32 public constant UPDATE_MULTISIG_SETTINGS_PERMISSION_ID =
  72          keccak256("UPDATE_MULTISIG_SETTINGS_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L64-L72

```solidity
contracts\src\plugins\token\MerkleMinter.sol:

  24:     bytes32 public constant MERKLE_MINT_PERMISSION_ID = keccak256("MERKLE_MINT_PERMISSION");

  27:     bytes32 public constant CHANGE_DISTRIBUTOR_PERMISSION_ID =
  28          keccak256("CHANGE_DISTRIBUTOR_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L24-L28

```solidity
contracts\src\token\ERC20\governance\GovernanceERC20.sol:
  28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L28


```solidity
contracts\src\plugins\governance\majority-voting\token\TokenVoting.sol:

  22:     bytes4 internal constant TOKEN_VOTING_INTERFACE_ID =
  23          this.initialize.selector ^ this.getVotingToken.selector;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L22-L23


**Recommendation Code:**
```diff
contracts\src\token\ERC20\governance\GovernanceERC20.sol:

- 28:     bytes32 public constant MINT_PERMISSION_ID = keccak256("MINT_PERMISSION");
             // MINT_PERMISSION_ID = keccak256("MINT_PERMISSION")
+ 28:     bytes32 public constant MINT_PERMISSION_ID = 0xb737b436e6cc542520cb79ec04245c720c38eebfa56d9e2d99b043979db20e4c;



contracts\src\plugins\governance\majority-voting\token\TokenVoting.sol:

- 22:     bytes4 internal constant TOKEN_VOTING_INTERFACE_ID =
- 23          this.initialize.selector ^ this.getVotingToken.selector;
              // TOKEN_VOTING_INTERFACE_ID = this.initialize.selector ^ this.getVotingToken.selector
+ 22:     bytes4 internal constant TOKEN_VOTING_INTERFACE_ID = 0x50eb001e;

```

### [G-16] Use ``assembly`` to write _address storage values_ 

17 results - 13 files:
```solidity
contracts\src\framework\plugin\repo\PluginRepoFactory.sol:

  22     constructor(PluginRepoRegistry _pluginRepoRegistry) {
  23:         pluginRepoRegistry = _pluginRepoRegistry;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L23

```solidity
contracts\src\framework\plugin\repo\PluginRepoRegistry.sol:

  41      function initialize(IDAO _dao, ENSSubdomainRegistrar _subdomainRegistrar) external initializer {
  45:         subdomainRegistrar = _subdomainRegistrar;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L45

```solidity
contracts\src\framework\plugin\setup\PluginSetupProcessor.sol:
  277     constructor(PluginRepoRegistry _repoRegistry) {
  278:         repoRegistry = _repoRegistry;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L278

```solidity
contracts\src\plugins\counter-example\v1\CounterV1.sol:

  26      function initialize(
  34:         multiplyHelper = _multiplyHelper;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1.sol#L34

```solidity
contracts\src\plugins\counter-example\v2\CounterV2.sol:

  32      function initialize(
  46:         multiplyHelper = _multiplyHelper;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2.sol#L46

```solidity
contracts\src\plugins\counter-example\v2\CounterV2PluginSetup.sol:

  24     constructor(MultiplyHelper _helper) {
  25:         multiplyHelperBase = _helper;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L25

```solidity
contracts\src\core\dao\DAO.sol:

  283      function _setTrustedForwarder(address _trustedForwarder) internal {
  284:         trustedForwarder = _trustedForwarder;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L284


```solidity
contracts\src\framework\dao\DAORegistry.sol:

  37      function initialize(
  42:         subdomainRegistrar = _subdomainRegistrar;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L42

```solidity
contracts\src\core\plugin\dao-authorizable\DaoAuthorizableUpgradeable.sol:

  20      function __DaoAuthorizableUpgradeable_init(IDAO _dao) internal onlyInitializing {
  21:         dao_ = _dao;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizableUpgradeable.sol#L21

```solidity
contracts\src\framework\utils\ens\ENSSubdomainRegistrar.sol:

  57     function initialize(IDAO _managingDao, ENS _ens, bytes32 _node) external initializer {
  60:         ens = _ens;
  69:         resolver = nodeResolver;

  100      function setDefaultResolver(
  107:         resolver = _resolver;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L60

```solidity
contracts\src\plugins\governance\majority-voting\token\TokenVoting.sol:

  36      function initialize(
  43:         votingToken = _token;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L43


```solidity
contracts\src\plugins\token\MerkleDistributor.sol:

  46      function initialize(
  52:         token = _token;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L52


```solidity
contracts\src\plugins\token\MerkleMinter.sol:

  41      function initialize(
  48:         token = _token;
  49:         distributorBase = _distributorBase;

  53     function changeDistributorBase(
  56:         distributorBase = _distributorBase;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L48-L49

**Recommendation Code:**
```diff
contracts\src\plugins\token\MerkleMinter.sol:

  53     function changeDistributorBase(
- 56:         distributorBase = _distributorBase;
+                  assembly {                      
+                      sstore(distributorBase.slot, _distributorBase)
+                  }                               
          
```

### [G-17] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.


21 results - 21 files:
```solidity
src\core\dao\DAO.sol:
 
  92:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L92

```solidity
src\core\plugin\Plugin.sol:

  17:     constructor(IDAO _dao) DaoAuthorizable(_dao) {}

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/Plugin.sol#L17

```solidity
src\core\plugin\PluginCloneable.sol:

  16:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginCloneable.sol#L16

```solidity
src\core\plugin\PluginUUPSUpgradeable.sol:
 
  25:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/PluginUUPSUpgradeable.sol#L25


```solidity
src\core\plugin\dao-authorizable\DaoAuthorizable.sol:

  19:     constructor(IDAO _dao) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/dao-authorizable/DaoAuthorizable.sol#L19


```solidity
src\framework\dao\DAOFactory.sol:

  53:     constructor(DAORegistry _registry, PluginSetupProcessor _pluginSetupProcessor) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L53


```solidity
src\framework\dao\DAORegistry.sol:
 
   30:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAORegistry.sol#L30

```solidity
src\framework\plugin\repo\PluginRepo.sol:
 
  112:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L112


```solidity
src\framework\plugin\repo\PluginRepoFactory.sol:

  22:     constructor(PluginRepoRegistry _pluginRepoRegistry) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L22


```solidity
src\framework\plugin\repo\PluginRepoRegistry.sol:

  34:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol#L34

```solidity
src\framework\plugin\setup\PluginSetupProcessor.sol:

  277:     constructor(PluginRepoRegistry _repoRegistry) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L277

```solidity
src\framework\utils\TokenFactory.sol:
 
   68:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L68

```solidity
src\framework\utils\ens\ENSSubdomainRegistrar.sol:

  45:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L45

```solidity
src\plugins\counter-example\v1\CounterV1PluginSetup.sol:

  23:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L23

```solidity
src\plugins\counter-example\v2\CounterV2PluginSetup.sol:

  24:     constructor(MultiplyHelper _helper) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L24

```solidity
src\plugins\governance\admin\AdminSetup.sol:
 
  27:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/admin/AdminSetup.sol#L27

```solidity
src\plugins\governance\majority-voting\addresslist\AddresslistVotingSetup.sol:

  20:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L20

```solidity
src\plugins\governance\majority-voting\token\TokenVotingSetup.sol:

  61:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVotingSetup.sol#L61

```solidity
src\plugins\governance\multisig\MultisigSetup.sol:

  19:     constructor() {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/MultisigSetup.sol#L19

```solidity
src\token\ERC20\governance\GovernanceERC20.sol:
 
  49:     constructor(

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L49

```solidity
src\token\ERC20\governance\GovernanceWrappedERC20.sol:
  
  38:     constructor(IERC20Upgradeable _token, string memory _name, string memory _symbol) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L38


**Recommendation:**
Set the constructor to ```payable```


### [G-18] Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement

```
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a } 
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
```
This will stop the check for overflow and underflow so it will save gas.


1 result - 1 file:
```solidity
src\framework\plugin\repo\PluginRepo.sol:

  147:         if (_release > latestRelease) {
  148:             latestRelease = _release;

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L147-L148


### [G-19] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

11 results - 8 files:
```solidity
src\core\permission\PermissionManager.sol:

  236:         if (_where == ANY_ADDR && _who == ANY_ADDR) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L236

```solidity
src\framework\plugin\repo\PluginRepo.sol:

  157:         if (version.tag.release != 0 && version.tag.release != _release) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L157

```solidity
src\framework\utils\RegistryUtils.sol:

  20:         if (char > 96 && char < 123) {

  25:         if (char > 47 && char < 58) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/RegistryUtils.sol#L20

```solidity
src\plugins\governance\majority-voting\addresslist\AddresslistVoting.sol:

   97:         if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
  
  185:         if (_tryEarlyExecution && _canExecute(_proposalId)) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97

```solidity
src\plugins\governance\majority-voting\token\TokenVoting.sol:

  182:         if (_tryEarlyExecution && _canExecute(_proposalId)) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L182

```solidity
src\plugins\governance\multisig\Multisig.sol:

  216:         if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {
  
  283:         if (_tryExecution && _canExecute(_proposalId)) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216

```solidity
src\token\ERC20\governance\GovernanceERC20.sol:

  116:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L116

```solidity
src\token\ERC20\governance\GovernanceWrappedERC20.sol:

  106:         if (to != address(0) && numCheckpoints(to) == 0 && delegates(to) == address(0)) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceWrappedERC20.sol#L106


### [G-20] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

2 results - 1 file:
```solidity
src\core\permission\PermissionManager.sol:

  240:    if (_where == ANY_ADDR || _who == ANY_ADDR) {

  242:    if (_permissionId == ROOT_PERMISSION_ID || isRestricted) {

```
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L240-L243


### [G-21] Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ```DAOl.sol``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

DAO.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
eafb8b06  =>  initialize(bytes,address,address,string)
552b1863  =>  isPermissionRestrictedForAnyAddr(bytes32)
5ec29272  =>  _authorizeUpgrade(address)
da742228  =>  setTrustedForwarder(address)
ce1b815f  =>  getTrustedForwarder()
fdef9106  =>  hasPermission(address,address,bytes32,bytes)
ee57e36f  =>  setMetadata(bytes)
f01de670  =>  execute(bytes32,Action[],uint256)
bfe07da6  =>  deposit(address,uint256,string)
3e2ab0d9  =>  setSignatureValidator(address)
1626ba7e  =>  isValidSignature(bytes32,bytes)
4146c61e  =>  _setMetadata(bytes)
d292f98a  =>  _setTrustedForwarder(address)
f3e54f30  =>  _registerTokenInterfaces()
c4a50145  =>  registerStandardCallback(bytes4,bytes4,bytes4)
7034731b  =>  daoURI()
1080f99b  =>  setDaoURI(string)
f392bdc0  =>  _setDaoURI(string)

```

### [G-22] Use a more recent version of solidity

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

All contracts in scope (**62 files**) are written in ``pragma solidity ^0.8.17;`` and I recommend using the newer battle-tested version of Solidity ``0.8.19``.


**Context:**
All contracts