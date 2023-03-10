# Summary
Certain optimizations were benchmarked to use as baselines for issues + illustrate average savings. All benchmarks were done via the protocol's tests. Instances are illustrated with diffs.

Functions that are not covered in the protocol's tests and/or not benchmarked: gas savings are explained via opcodes & EVM gas costs. Instances are also illustrated with diffs.

**Note**: Some code snippets may be truncated to save space. Code snippets may also be accompanied by `@audit` tags in comments to aid in explaining the issue.

## Gas Optimizations
| Number |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G-01](#state-variables-only-set-in-the-constructor-should-be-declared-immutable) | State variables only set in the constructor should be declared immutable | 11 | 23100 |
| [G-02](#state-variables-can-be-cached-instead-of-re-reading-them-from-storage) | State variables can be cached instead of re-reading them from storage | 16 |  1600 |
| [G-03](#multiple-accesses-of-a-mappingarray-should-use-a-storage-pointer) | Multiple accesses of a mapping/array should use a storage pointer | 4 | 227 |
| [G-04](#avoid-calling-the-same-internal-function-multiple-times) | Avoid calling the same internal function multiple times | - | - |

## State variables only set in the constructor should be declared immutable
The solidity compiler will directly embed the values of immutable variables into your contract bytecode and therefore will save you from incurring a `Gsset (20000 gas)` when you set storage variables in the constructor, a `Gcoldsload (2100 gas)` when you access storage variables for the first time in a transaction,  and a `Gwarmaccess (100 gas)` for each subsequent access to that storage slot.

Total Instances: 11

Gas Savings: `11 * 2100 = 23100`

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol#L15-L18

### Gas Savings for `createPluginRepo()`, obtained via protocol's tests: Avg 4206 gas | Set `pluginRepoRegistry` and `pluginRepoBase` to `immutable` to save ~4200 gas.
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  -  |  -  |  531312 |    4    |
| After  |  -  |  -  |  527106 |    4    |
```solidity
File: src/framework/plugin/repo/PluginRepoFactory.sol
15:   PluginRepoRegistry public pluginRepoRegistry;
16:
17:    /// @notice The address of the `PluginRepo` base contract.
18:    address public pluginRepoBase;
```
```diff
diff --git a/src/framework/plugin/repo/PluginRepoFactory.sol b/src/framework/plugin/repo/PluginRepoFactory.sol
index 9bb5590..88df6de 100644
--- a/src/framework/plugin/repo/PluginRepoFactory.sol
+++ b/src/framework/plugin/repo/PluginRepoFactory.sol
@@ -12,10 +12,10 @@ import {PluginRepo} from "./PluginRepo.sol";
 /// @notice This contract creates `PluginRepo` proxies and registers them on an `PluginRepoRegistry` contract.
 contract PluginRepoFactory {
     /// @notice The Aragon plugin registry contract.
-    PluginRepoRegistry public pluginRepoRegistry;
+    PluginRepoRegistry public immutable pluginRepoRegistry;

     /// @notice The address of the `PluginRepo` base contract.
-    address public pluginRepoBase;
+    address public immutable pluginRepoBase;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L128

### Set `repoRegistry` as `immutable` to save ~2100 gas
```solidity
File: src/framework/plugin/setup/PluginSetupProcessor.sol
127:    /// @notice The plugin repo registry listing the `PluginRepo` contracts versioning the `PluginSetup` contracts.
128:    PluginRepoRegistry public repoRegistry;
```
```diff
diff --git a/src/framework/plugin/setup/PluginSetupProcessor.sol b/src/framework/plugin/setup/PluginSetupProcessor.sol
index 41f68e5..4278c4b 100644
--- a/src/framework/plugin/setup/PluginSetupProcessor.sol
+++ b/src/framework/plugin/setup/PluginSetupProcessor.sol
@@ -125,7 +125,7 @@ contract PluginSetupProcessor {
     }

     /// @notice The plugin repo registry listing the `PluginRepo` contracts versioning the `PluginSetup` contracts.
-    PluginRepoRegistry public repoRegistry;
+    PluginRepoRegistry public immutable repoRegistry;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L26-L36

### Move `setupBases()` logic into the `constructor` and set `governanceERC20Base`, `governanceWrappedERC20Base`, `merkleMinterBase`, and `distributorBase` to `immutable` to save ~8400 gas. 

*Note that `setupBases()` is a private function only called in the constructor.*
```solidity
File: src/framework/utils/TokenFactory.sol
26:    /// @notice The address of the `GovernanceERC20` base contract to clone from.
27:    address public governanceERC20Base;
28:
29:    /// @notice The address of the `GovernanceWrappedERC20` base contract to clone from.
30:    address public governanceWrappedERC20Base;
31:
32:    /// @notice The address of the `MerkleMinter` base contract to clone from.
33:    address public merkleMinterBase;
34:
35:    /// @notice The `MerkleDistributor` base contract used to initialize the `MerkleMinter` clones.
36:    MerkleDistributor public distributorBase;
```
```diff
diff --git a/src/framework/utils/TokenFactory.sol b/src/framework/utils/TokenFactory.sol
index 381e745..c24b133 100644
--- a/src/framework/utils/TokenFactory.sol
+++ b/src/framework/utils/TokenFactory.sol
@@ -24,16 +24,16 @@ contract TokenFactory {
     using Clones for address;

     /// @notice The address of the `GovernanceERC20` base contract to clone from.
-    address public governanceERC20Base;
+    address public immutable governanceERC20Base;

     /// @notice The address of the `GovernanceWrappedERC20` base contract to clone from.
-    address public governanceWrappedERC20Base;
+    address public immutable governanceWrappedERC20Base;

     /// @notice The address of the `MerkleMinter` base contract to clone from.
-    address public merkleMinterBase;
+    address public immutable merkleMinterBase;

     /// @notice The `MerkleDistributor` base contract used to initialize the `MerkleMinter` clones.
-    MerkleDistributor public distributorBase;
+    MerkleDistributor public immutable distributorBase;

     /// @notice Emitted when a new token is created.
     /// @param token [ERC-20](https://eips.ethereum.org/EIPS/eip-20) token address.
@@ -66,7 +66,19 @@ contract TokenFactory {

     /// @notice Initializes the different base contracts for the factory to clone from.
     constructor() {
-        setupBases();
+        distributorBase = new MerkleDistributor();
+        governanceERC20Base = address(
+            new GovernanceERC20(
+                IDAO(address(0)),
+                "baseName",
+                "baseSymbol",
+                GovernanceERC20.MintSettings(new address[](0), new uint256[](0))
+            )
+        );
+        governanceWrappedERC20Base = address(
+            new GovernanceWrappedERC20(IERC20Upgradeable(address(0)), "baseName", "baseSymbol")
+        );
+        merkleMinterBase = address(new MerkleMinter());
     }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L19-L21

### Set `multiplyHelperBase` and `counterBase` to `immutable` to save ~4200 gas.
```solidity
File: src/plugins/counter-example/v1/CounterV1PluginSetup.sol
19:    // For testing purposes, the below are public...
20:    MultiplyHelper public multiplyHelperBase;
21:    CounterV1 public counterBase;
```
```diff
diff --git a/src/plugins/counter-example/v1/CounterV1PluginSetup.sol b/src/plugins/counter-example/v1/CounterV1PluginSetup.sol
index 7af94ab..a7ce824 100644
--- a/src/plugins/counter-example/v1/CounterV1PluginSetup.sol
+++ b/src/plugins/counter-example/v1/CounterV1PluginSetup.sol
@@ -17,8 +17,8 @@ contract CounterV1PluginSetup is PluginSetup {
     using Clones for address;

     // For testing purposes, the below are public...
-    MultiplyHelper public multiplyHelperBase;
-    CounterV1 public counterBase;
+    MultiplyHelper public immutable multiplyHelperBase;
+    CounterV1 public immutable counterBase;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L19-L21

### Set `multiplyHelperBase` and `counterBase` to `immutable` to save ~4200 gas.
```solidity
File: src/plugins/counter-example/v2/CounterV2PluginSetup.sol
19:    // For testing purposes, the contracts below are public.
20:    MultiplyHelper public multiplyHelperBase;
21:    CounterV2 public counterBase;
```
```diff
diff --git a/src/plugins/counter-example/v2/CounterV2PluginSetup.sol b/src/plugins/counter-example/v2/CounterV2PluginSetup.sol
index 527074b..8d73f02 100644
--- a/src/plugins/counter-example/v2/CounterV2PluginSetup.sol
+++ b/src/plugins/counter-example/v2/CounterV2PluginSetup.sol
@@ -17,8 +17,8 @@ contract CounterV2PluginSetup is PluginSetup {
     using Clones for address;

     // For testing purposes, the contracts below are public.
-    MultiplyHelper public multiplyHelperBase;
-    CounterV2 public counterBase;
+    MultiplyHelper public immutable multiplyHelperBase;
+    CounterV2 public immutable counterBase;
```

## State variables can be cached instead of re-reading them from storage
Caching of a state variable replaces each `Gwarmaccess (100 gas)` with a much cheaper stack read. 

*Note these are instances that the c4udit tool missed.*

Total Instances: 16

Gas savings: `11 * 100 + 501 (5 benchmarked instances) = 1601`

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L128-L153

### Gas Savings for `createVersion()`, obtained via protocol's tests: Avg 107 gas | Cache `latestRelease` as a stack variable to save 1 SLOAD
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  121713  |  178154  |  161829 |    40    |
| After  |  121606  |  178047  |  161722 |    40    |
```solidity
File: src/framework/plugin/repo/PluginRepo.sol
128:    function createVersion(
129:        uint8 _release,
130:        address _pluginSetup,
131:        bytes calldata _buildMetadata,
132:        bytes calldata _releaseMetadata
133:    ) external auth(MAINTAINER_PERMISSION_ID) {
134:        if (!_pluginSetup.supportsInterface(type(IPluginSetup).interfaceId)) {
135:            revert InvalidPluginSetupInterface();
136:        }
137:
138:        if (_release == 0) {
139:            revert ReleaseZeroNotAllowed();
140:        }
141:
142:        // Check that the release number is not incremented by more than one
143:        if (_release - latestRelease > 1) { // @audit: 1st sload
144:            revert InvalidReleaseIncrement({latestRelease: latestRelease, newRelease: _release});
145:        }
146:
147:        if (_release > latestRelease) { // @audit: 2nd sload
148:            latestRelease = _release;
149:
150:            if (_releaseMetadata.length == 0) {
151:                revert EmptyReleaseMetadata();
152:            }
153:        }
```
```diff
diff --git a/src/framework/plugin/repo/PluginRepo.sol b/src/framework/plugin/repo/PluginRepo.sol
index 6dc2c8b..dfde528 100644
--- a/src/framework/plugin/repo/PluginRepo.sol
+++ b/src/framework/plugin/repo/PluginRepo.sol
@@ -140,11 +140,12 @@ contract PluginRepo is
         }

         // Check that the release number is not incremented by more than one
-        if (_release - latestRelease > 1) {
-            revert InvalidReleaseIncrement({latestRelease: latestRelease, newRelease: _release});
+        uint8 _latestRelease = latestRelease;
+        if (_release - _latestRelease > 1) {
+            revert InvalidReleaseIncrement({latestRelease: _latestRelease, newRelease: _release});
         }

-        if (_release > latestRelease) {
+        if (_release > _latestRelease) {
             latestRelease = _release;
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L82-L96

### Gas Savings for `createVersion()`, obtained via protocol's tests: Avg 394 gas | Cache `node`, `ens`, and `resolver` to save 4 SLOADs
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  140546  |  142827  |  141691 |    14    |
| After  |  140152  |  142433  |  141297 |    14    |
```solidity
File: src/framework/utils/ens/ENSSubdomainRegistrar.sol
82:    function registerSubnode(
83:        bytes32 _label,
84:        address _targetAddress
85:    ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
86:        bytes32 subnode = keccak256(abi.encodePacked(node, _label)); // @audit: 1st sload for `node`
87:        address currentOwner = ens.owner(subnode); // @audit: 1st sload for `ens`
88:
89:        if (currentOwner != address(0)) {
90:            revert AlreadyRegistered(subnode, currentOwner);
91:        }
92:
93:        ens.setSubnodeOwner(node, _label, address(this)); // @audit: 2nd sload for `ens` and `node`
94:        ens.setResolver(subnode, resolver); // @audit: 3rd sload for `node` and 1st sload for `resolver`
95:        Resolver(resolver).setAddr(subnode, _targetAddress); // @audit: 2nd sload for resolver
96:    }
```
```diff
diff --git a/src/framework/utils/ens/ENSSubdomainRegistrar.sol b/src/framework/utils/ens/ENSSubdomainRegistrar.sol
index 0d5ea5e..3ed14bd 100644
--- a/src/framework/utils/ens/ENSSubdomainRegistrar.sol
+++ b/src/framework/utils/ens/ENSSubdomainRegistrar.sol
@@ -83,16 +83,19 @@ contract ENSSubdomainRegistrar is UUPSUpgradeable, DaoAuthorizableUpgradeable {
         bytes32 _label,
         address _targetAddress
     ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
-        bytes32 subnode = keccak256(abi.encodePacked(node, _label));
-        address currentOwner = ens.owner(subnode);
+        bytes32 _node = node;
+        ENS _ens = ens;
+        address _resolver = resolver;
+        bytes32 subnode = keccak256(abi.encodePacked(_node, _label));
+        address currentOwner = _ens.owner(subnode);

         if (currentOwner != address(0)) {
             revert AlreadyRegistered(subnode, currentOwner);
         }

-        ens.setSubnodeOwner(node, _label, address(this));
-        ens.setResolver(subnode, resolver);
-        Resolver(resolver).setAddr(subnode, _targetAddress);
+        _ens.setSubnodeOwner(_node, _label, address(this));
+        _ens.setResolver(subnode, _resolver);
+        Resolver(_resolver).setAddr(subnode, _targetAddress);
     }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleMinter.sol#L74-L95

### Cache `token` as a stack variable to save 1 SLOAD
```solidity
File: src/plugins/token/MerkleMinter.sol
74:    function merkleMint(
75:        bytes32 _merkleRoot,
76:        uint256 _totalAmount,
77:        bytes calldata _tree,
78:        bytes calldata _context
79:    ) external override auth(MERKLE_MINT_PERMISSION_ID) returns (IMerkleDistributor distributor) {
80:        address distributorAddr = createERC1967Proxy(
81:            address(distributorBase),
82:            abi.encodeWithSelector(
83:                MerkleDistributor.initialize.selector,
84:                dao(),
85:                IERC20Upgradeable(address(token)), // @audit: 1st sload
86:                _merkleRoot
87:            )
88:        );
89:
90:        token.mint(distributorAddr, _totalAmount); // @audit: 2nd sload
91:
92:        emit MerkleMinted(distributorAddr, _merkleRoot, _totalAmount, _tree, _context);
93:
94:        return IMerkleDistributor(distributorAddr);
95:    }
```
```diff
diff --git a/src/plugins/token/MerkleMinter.sol b/src/plugins/token/MerkleMinter.sol
index fab8959..a372746 100644
--- a/src/plugins/token/MerkleMinter.sol
+++ b/src/plugins/token/MerkleMinter.sol
@@ -77,17 +77,18 @@ contract MerkleMinter is IMerkleMinter, PluginUUPSUpgradeable {
         bytes calldata _tree,
         bytes calldata _context
     ) external override auth(MERKLE_MINT_PERMISSION_ID) returns (IMerkleDistributor distributor) {
+        IERC20MintableUpgradeable _token = token;
         address distributorAddr = createERC1967Proxy(
             address(distributorBase),
             abi.encodeWithSelector(
                 MerkleDistributor.initialize.selector,
                 dao(),
-                IERC20Upgradeable(address(token)),
+                IERC20Upgradeable(address(_token)),
                 _merkleRoot
             )
         );

-        token.mint(distributorAddr, _totalAmount);
+        _token.mint(distributorAddr, _totalAmount);

         emit MerkleMinted(distributorAddr, _merkleRoot, _totalAmount, _tree, _context);
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L248-L258

### Cache `signatureValidator` as a stack variable to save 1 SLOAD
```solidity
File: src/core/dao/DAO.sol
248:    function isValidSignature(
249:        bytes32 _hash,
250:        bytes memory _signature
251:    ) external view override(IDAO, IERC1271) returns (bytes4) {
252:        if (address(signatureValidator) == address(0)) { // @audit: 1st sload
253:            // Return the invalid magic number
254:            return bytes4(0);
255:        }
256:        // Forward the call to the set signature validator contract
257:        return signatureValidator.isValidSignature(_hash, _signature); // @audit: 2nd sload
258:    }
```
```diff
diff --git a/src/core/dao/DAO.sol b/src/core/dao/DAO.sol
index d7c912d..9b3379f 100644
--- a/src/core/dao/DAO.sol
+++ b/src/core/dao/DAO.sol
@@ -249,12 +249,13 @@ contract DAO is
         bytes32 _hash,
         bytes memory _signature
     ) external view override(IDAO, IERC1271) returns (bytes4) {
-        if (address(signatureValidator) == address(0)) {
+        IERC1271 _signatureValidator = signatureValidator;
+        if (address(_signatureValidator) == address(0)) {
             // Return the invalid magic number
             return bytes4(0);
         }
         // Forward the call to the set signature validator contract
-        return signatureValidator.isValidSignature(_hash, _signature);
+        return _signatureValidator.isValidSignature(_hash, _signature);
     }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/repo/PluginRepo.sol#L155-L163

### Cache `version.tag.release` as a stack variable to save 2 SLOADs
```solidity
File: src/framework/plugin/repo/PluginRepo.sol
155:        // Make sure the same plugin setup wasn't used in previous releases.
156:        Version storage version = versions[latestTagHashForPluginSetup[_pluginSetup]];
157:        if (version.tag.release != 0 && version.tag.release != _release) { // @audit: 1st and 2nd sload 
158:            revert PluginSetupAlreadyInPreviousRelease(
159:                version.tag.release, // @audit: 3rd sload, unhappy path 
160:                version.tag.build,
161:                _pluginSetup
162:            );
163:        }
```
```diff
diff --git a/src/framework/plugin/repo/PluginRepo.sol b/src/framework/plugin/repo/PluginRepo.sol
index 6dc2c8b..4c18f74 100644
--- a/src/framework/plugin/repo/PluginRepo.sol
+++ b/src/framework/plugin/repo/PluginRepo.sol
@@ -154,9 +154,10 @@ contract PluginRepo is

         // Make sure the same plugin setup wasn't used in previous releases.
         Version storage version = versions[latestTagHashForPluginSetup[_pluginSetup]];
-        if (version.tag.release != 0 && version.tag.release != _release) {
+        uint8 prevRelease = version.tag.release;
+        if (prevRelease != 0 && prevRelease != _release) {
             revert PluginSetupAlreadyInPreviousRelease(
-                version.tag.release,
+                prevRelease,
                 version.tag.build,
                 _pluginSetup
             );
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L43-L88

### Cache `counterBase` and `multiplyHelperBase` as stack variables to save 2 SLOADs
```solidity
File: src/plugins/counter-example/v1/CounterV1PluginSetup.sol
43:        if (_multiplyHelper == address(0)) {
44:            multiplyHelper = createERC1967Proxy(address(multiplyHelperBase), bytes("")); // @audit: 1st sload for `multiplyHelperBase`
45:        }
46:
47:        bytes memory initData = abi.encodeWithSelector(
48:            bytes4(keccak256("initialize(address,address,uint256)")),
49:            _dao,
50:            multiplyHelper,
51:            _num
52:        );
53:
54:        PermissionLib.MultiTargetPermission[]
55:            memory permissions = new PermissionLib.MultiTargetPermission[](
56:                _multiplyHelper == address(0) ? 3 : 2
57:            );
58:        address[] memory helpers = new address[](1);
59:
60:        // deploy
61:        plugin = createERC1967Proxy(address(counterBase), initData); // @audit: 1st sload for `counterBase`
62:
63:        // set permissions
64:        permissions[0] = PermissionLib.MultiTargetPermission(
65:            PermissionLib.Operation.Grant,
66:            _dao,
67:            plugin,
68:            PermissionLib.NO_CONDITION,
69:            keccak256("EXECUTE_PERMISSION")
70:        );
71:
72:        permissions[1] = PermissionLib.MultiTargetPermission(
73:            PermissionLib.Operation.Grant,
74:            plugin,
75:            _dao,
76:            PermissionLib.NO_CONDITION,
77:            counterBase.MULTIPLY_PERMISSION_ID() // @audit: 2nd sload for `counterBase`
78:        );
79:
80:        if (_multiplyHelper == address(0)) {
81:            permissions[2] = PermissionLib.MultiTargetPermission(
82:                PermissionLib.Operation.Grant,
83:                multiplyHelper,
84:                plugin,
85:                PermissionLib.NO_CONDITION,
86:                multiplyHelperBase.MULTIPLY_PERMISSION_ID() // @audit: 2nd sload for `multiplyHelperBase`
87:            );
88:        }
```
```diff
diff --git a/src/plugins/counter-example/v1/CounterV1PluginSetup.sol b/src/plugins/counter-example/v1/CounterV1PluginSetup.sol
index 7af94ab..85d2dc6 100644
--- a/src/plugins/counter-example/v1/CounterV1PluginSetup.sol
+++ b/src/plugins/counter-example/v1/CounterV1PluginSetup.sol
@@ -39,9 +39,9 @@ contract CounterV1PluginSetup is PluginSetup {
         (address _multiplyHelper, uint256 _num) = abi.decode(_data, (address, uint256));

         address multiplyHelper = _multiplyHelper;
-
+        MultiplyHelper _multiplyHelperBase = multiplyHelperBase;
         if (_multiplyHelper == address(0)) {
-            multiplyHelper = createERC1967Proxy(address(multiplyHelperBase), bytes(""));
+            multiplyHelper = createERC1967Proxy(address(_multiplyHelperBase), bytes(""));
         }

         bytes memory initData = abi.encodeWithSelector(
@@ -58,7 +58,8 @@ contract CounterV1PluginSetup is PluginSetup {
         address[] memory helpers = new address[](1);

         // deploy
-        plugin = createERC1967Proxy(address(counterBase), initData);
+        CounterV1 _counterBase = counterBase;
+        plugin = createERC1967Proxy(address(_counterBase), initData);

         // set permissions
         permissions[0] = PermissionLib.MultiTargetPermission(
@@ -74,7 +75,7 @@ contract CounterV1PluginSetup is PluginSetup {
             plugin,
             _dao,
             PermissionLib.NO_CONDITION,
-            counterBase.MULTIPLY_PERMISSION_ID()
+            _counterBase.MULTIPLY_PERMISSION_ID()
         );

         if (_multiplyHelper == address(0)) {
@@ -83,7 +84,7 @@ contract CounterV1PluginSetup is PluginSetup {
                 multiplyHelper,
                 plugin,
                 PermissionLib.NO_CONDITION,
-                multiplyHelperBase.MULTIPLY_PERMISSION_ID()
+                _multiplyHelperBase.MULTIPLY_PERMISSION_ID()
             );
         }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L44-L89

### Cache `counterBase` and `multiplyHelperBase` as stack variables to save 2 SLOADs (Identical to instance above)

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L313-L321

### Cache `proposal_.parameters.supportThreshold` as a stack variable to save 1 SLOAD
```solidity
File: src/plugins/governance/majority-voting/MajorityVotingBase.sol
313:    function isSupportThresholdReached(uint256 _proposalId) public view virtual returns (bool) {
314:        Proposal storage proposal_ = proposals[_proposalId];
315:
316:        // The code below implements the formula of the support criterion explained in the top of this file.
317:        // `(1 - supportThreshold) * N_yes > supportThreshold *  N_no`
318:        return
319:            (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes > // @audit: 1st sload
320:            proposal_.parameters.supportThreshold * proposal_.tally.no; // @audit: 2nd sload
321:    }
```
```diff
diff --git a/src/plugins/governance/majority-voting/MajorityVotingBase.sol b/src/plugins/governance/majority-voting/MajorityVotingBase.sol
index 58ba2fe..d6ce8b0 100644
--- a/src/plugins/governance/majority-voting/MajorityVotingBase.sol
+++ b/src/plugins/governance/majority-voting/MajorityVotingBase.sol
@@ -315,9 +315,10 @@ abstract contract MajorityVotingBase is

         // The code below implements the formula of the support criterion explained in the top of this file.
         // `(1 - supportThreshold) * N_yes > supportThreshold *  N_no`
+        uint32 _supportThreshold = proposal_.parameters.supportThreshold;
         return
-            (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >
-            proposal_.parameters.supportThreshold * proposal_.tally.no;
+            (RATIO_BASE - _supportThreshold) * proposal_.tally.yes >
+            _supportThreshold * proposal_.tally.no;
     }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L324-L338

### Cache `proposal_.tally.yes` and `proposal_.parameters.supportThreshold` as stack variables to save 2 SLOADs
```solidity
File: src/plugins/governance/majority-voting/MajorityVotingBase.sol
324:    function isSupportThresholdReachedEarly(
325:        uint256 _proposalId
326:    ) public view virtual returns (bool) {
327:       Proposal storage proposal_ = proposals[_proposalId];
328:
329:        uint256 noVotesWorstCase = totalVotingPower(proposal_.parameters.snapshotBlock) -
330:            proposal_.tally.yes - // @audit: 1st sload for `proposal_.tally.yes`
331:            proposal_.tally.abstain;
332:
333:        // The code below implements the formula of the early execution support criterion explained in the top of this file.
334:        // `(1 - supportThreshold) * N_yes > supportThreshold *  N_no,worst-case`
335:        return
336:            (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes > // @audit: 1st sload for `proposal_.parameters.supportThreshold`, 2nd sload for `proposal_.tally.yes`
337:            proposal_.parameters.supportThreshold * noVotesWorstCase; // @audit: 2nd sload for `proposal_.parameters.supportThreshold`
338:    }
```
```diff
diff --git a/src/plugins/governance/majority-voting/MajorityVotingBase.sol b/src/plugins/governance/majority-voting/MajorityVotingBase.sol
index 58ba2fe..e6cf684 100644
--- a/src/plugins/governance/majority-voting/MajorityVotingBase.sol
+++ b/src/plugins/governance/majority-voting/MajorityVotingBase.sol
@@ -325,16 +325,18 @@ abstract contract MajorityVotingBase is
         uint256 _proposalId
     ) public view virtual returns (bool) {
         Proposal storage proposal_ = proposals[_proposalId];
+        uint256 _yes = proposal_.tally.yes;
+        uint32 _supportThreshold = proposal_.parameters.supportThreshold;

         uint256 noVotesWorstCase = totalVotingPower(proposal_.parameters.snapshotBlock) -
-            proposal_.tally.yes -
+            _yes -
             proposal_.tally.abstain;

         // The code below implements the formula of the early execution support criterion explained in the top of this file.
         // `(1 - supportThreshold) * N_yes > supportThreshold *  N_no,worst-case`
         return
-            (RATIO_BASE - proposal_.parameters.supportThreshold) * proposal_.tally.yes >
-            proposal_.parameters.supportThreshold * noVotesWorstCase;
+            (RATIO_BASE - _supportThreshold) * _yes >
+            _supportThreshold * noVotesWorstCase;
     }
```

## Multiple accesses of a mapping/array should use a storage pointer
Caching a mapping's value in a storage pointer when the value is accessed multiple times saves ~40 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it.

To achieve this, declare a storage pointer for the variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `proposals[_proposalId]`, save its reference via a storage pointer: `Proposal storage proposal_ = proposals[_proposalId]` and use the pointer instead.

Total instances: 4

Gas savings: `2 * 40 + 147 (from 2 benchmarked instances) = 227`

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L348-L359

### Gas Savings for `execute()`, obtained via protocol's tests: Avg 147 gas | Use storage pointer `proposal_` instead of re-accessing the mapping.
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  70489  |  72989  |  71991 |    10     |
| After  |  70342  |  72842  |  71844 |    10     |
```solidity
File: src/plugins/governance/multisig/Multisig.sol
348:    function _execute(uint256 _proposalId) internal {
349:        Proposal storage proposal_ = proposals[_proposalId];
350:
351:        proposal_.executed = true;
352:
353:        _executeProposal(
354:            dao(),
355:            _proposalId,
356:            proposals[_proposalId].actions, // @audit: use storage pointer
357:            proposals[_proposalId].allowFailureMap // @audit: use storage pointer
358:        );
359:    }
```
```diff
diff --git a/src/plugins/governance/multisig/Multisig.sol b/src/plugins/governance/multisig/Multisig.sol
index d2dd072..eba5457 100644
--- a/src/plugins/governance/multisig/Multisig.sol
+++ b/src/plugins/governance/multisig/Multisig.sol
@@ -353,8 +353,8 @@ contract Multisig is
         _executeProposal(
             dao(),
             _proposalId,
-            proposals[_proposalId].actions,
-            proposals[_proposalId].allowFailureMap
+            proposal_.actions,
+            proposal_.allowFailureMap
         );
     }
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L457-L466

### `proposals[_proposalId]` can be cached as a storage pointer
```solidity
File: src/plugins/governance/majority-voting/MajorityVotingBase.sol
457:    function _execute(uint256 _proposalId) internal virtual {
458:        proposals[_proposalId].executed = true;
459:
460:        _executeProposal(
461:            dao(),
462:            _proposalId,
463:            proposals[_proposalId].actions,
464:            proposals[_proposalId].allowFailureMap
465:        );
466:    }
```
```diff
diff --git a/src/plugins/governance/majority-voting/MajorityVotingBase.sol b/src/plugins/governance/majority-voting/MajorityVotingBase.sol
index 58ba2fe..3985b7b 100644
--- a/src/plugins/governance/majority-voting/MajorityVotingBase.sol
+++ b/src/plugins/governance/majority-voting/MajorityVotingBase.sol
@@ -455,13 +455,14 @@ abstract contract MajorityVotingBase is
     /// @notice Internal function to execute a vote. It assumes the queried proposal exists.
     /// @param _proposalId The ID of the proposal.
     function _execute(uint256 _proposalId) internal virtual {
-        proposals[_proposalId].executed = true;
+        Proposal storage proposal_ = proposals[_proposalId];
+        proposal_.executed = true;

         _executeProposal(
             dao(),
             _proposalId,
-            proposals[_proposalId].actions,
-            proposals[_proposalId].allowFailureMap
+            proposal_.actions,
+            proposal_.allowFailureMap
         );
     }
```

## Avoid calling the same internal function multiple times
Calling an internal function will cost at least ~16-20 gas (JUMP to internal function instructions and JUMP back to original insturctions). If you call the same internal function multiple times you can save gas by caching the return value of the internal function.

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L91-L102

### Cache value from `_msgSender()`
```solidity
File: src/plugins/governance/majority-voting/token/TokenVoting.sol
91:        if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {
92:            revert ProposalCreationForbidden(_msgSender());
93:        }
94:
95:        proposalId = _createProposal({
96:            _creator: _msgSender(),
97:            _metadata: _metadata,
98:            _startDate: _startDate,
99:            _endDate: _endDate,
100:            _actions: _actions,
101:            _allowFailureMap: _allowFailureMap
102:        });
```
```diff
diff --git a/src/plugins/governance/majority-voting/token/TokenVoting.sol b/src/plugins/governance/majority-voting/token/TokenVoting.sol
index a3e26c3..820d3bc 100644
--- a/src/plugins/governance/majority-voting/token/TokenVoting.sol
+++ b/src/plugins/governance/majority-voting/token/TokenVoting.sol
@@ -88,12 +88,13 @@ contract TokenVoting is IMembership, MajorityVotingBase {
             revert NoVotingPower();
         }

-        if (votingToken.getPastVotes(_msgSender(), snapshotBlock) < minProposerVotingPower()) {
-            revert ProposalCreationForbidden(_msgSender());
+        address sender = _msgSender();
+        if (votingToken.getPastVotes(sender, snapshotBlock) < minProposerVotingPower()) {
+            revert ProposalCreationForbidden(sender);
         }

         proposalId = _createProposal({
-            _creator: _msgSender(),
+            _creator: sender,
             _metadata: _metadata,
             _startDate: _startDate,
             _endDate: _endDate,
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L97-L108
```solidity
File: src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol
97:        if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
98:            revert ProposalCreationForbidden(_msgSender());
99:        }
100:
101:        proposalId = _createProposal({
102:            _creator: _msgSender(),
103:            _metadata: _metadata,
104:            _startDate: _startDate,
105:            _endDate: _endDate,
106:            _actions: _actions,
107:            _allowFailureMap: _allowFailureMap
108:        });
```
```diff
diff --git a/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol b/src/plugins/governance/majority-voting/addresslist/AddressListVoting.sol
index 0ccd8a3..df9912d 100644
--- a/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol
+++ b/src/plugins/governance/majority-voting/addresslist/AddressListVoting.sol
@@ -93,13 +93,14 @@ contract AddresslistVoting is IMembership, Addresslist, MajorityVotingBase {
         unchecked {
             snapshotBlock = block.number.toUint64() - 1;
         }
-
-        if (minProposerVotingPower() != 0 && !isListedAtBlock(_msgSender(), snapshotBlock)) {
-            revert ProposalCreationForbidden(_msgSender());
+
+        address sender = _msgSender();
+        if (minProposerVotingPower() != 0 && !isListedAtBlock(sender, snapshotBlock)) {
+            revert ProposalCreationForbidden(sender);
         }

         proposalId = _createProposal({
-            _creator: _msgSender(),
+            _creator: sender,
             _metadata: _metadata,
             _startDate: _startDate,
             _endDate: _endDate,
```

https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L216-L237
```solidity
File: src/plugins/governance/multisig/Multisig.sol
216:        if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {
217:            revert ProposalCreationForbidden(_msgSender());
218:        }
219:
220:        if (_startDate == 0) {
221:            _startDate = block.timestamp.toUint64();
222:        } else if (_startDate < block.timestamp.toUint64()) {
223:            revert DateOutOfBounds({limit: block.timestamp.toUint64(), actual: _startDate});
224:        }
225:
226:        if (_endDate < _startDate) {
227:            revert DateOutOfBounds({limit: _startDate, actual: _endDate});
228:        }
229:
230:        proposalId = _createProposal({
231:            _creator: _msgSender(),
232:            _metadata: _metadata,
233:            _startDate: _startDate,
234:            _endDate: _endDate,
235:            _actions: _actions,
236:            _allowFailureMap: _allowFailureMap
237:        });
```
```diff
diff --git a/src/plugins/governance/multisig/Multisig.sol b/src/plugins/governance/multisig/Multisig.sol
index d2dd072..f64e8ec 100644
--- a/src/plugins/governance/multisig/Multisig.sol
+++ b/src/plugins/governance/multisig/Multisig.sol
@@ -212,9 +212,10 @@ contract Multisig is
         uint64 _endDate
     ) external returns (uint256 proposalId) {
         uint64 snapshotBlock = block.number.toUint64() - 1;
-
-        if (multisigSettings.onlyListed && !isListedAtBlock(_msgSender(), snapshotBlock)) {
-            revert ProposalCreationForbidden(_msgSender());
+
+        address sender = _msgSender();
+        if (multisigSettings.onlyListed && !isListedAtBlock(sender, snapshotBlock)) {
+            revert ProposalCreationForbidden(sender);
         }

         if (_startDate == 0) {
@@ -228,7 +229,7 @@ contract Multisig is
         }

         proposalId = _createProposal({
-            _creator: _msgSender(),
+            _creator: sender,
             _metadata: _metadata,
             _startDate: _startDate,
             _endDate: _endDate,
```