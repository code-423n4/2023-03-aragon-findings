[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L185](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L185)
-  lack of a zero address check.

```solidity
+ require(_actions[i].to != address(0),"zero address");
  address to = _actions[i].to;
```

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L184](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L184)
- 2. unnecessary uint256 

because of MAX_ACTIONS  == 256 

```solidity
uint256 internal constant MAX_ACTIONS = 256;

if (_actions.length > MAX_ACTIONS) {
       revert TooManyActions();
}

for (uint8 i = 0; i < _actions.length; ) {
if (!success) {
      // If the call failed and wasn't allowed in allowFailureMap, revert.
-     if (!hasBit(_allowFailureMap, uint8(i))) { //@audit openzepplin transfer.
+     if (!hasBit(_allowFailureMap, i)) { //@audit openzepplin transfer.
          revert ActionFailed(i);
      }

      // If the call failed, but was allowed in allowFailureMap, store that
      // this specific action has actually failed.
-      failureMap = flipBit(failureMap, uint8(i));
+      failureMap = flipBit(failureMap, i);
    }
}
```

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L150](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L150)

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L170](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L170)

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/dao/DAOFactory.sol#L95)

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L130](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L130)

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L124](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L124)

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L252](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L252)

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L60](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/utils/Addresslist.sol#L60)

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L81](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L81)
- 3. local variable not initialization

```solidity
- for (uint256 i; i < items.length; ) {
+ for (uint256 i=0; i < items.length; ) {
```

[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L95](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol#L95)

- 4. lack of a zero address check

```solidity
function registerSubnode( //@audit ignored return value.
        bytes32 _label,
        address _targetAddress
    ) external auth(REGISTER_ENS_SUBDOMAIN_PERMISSION_ID) {
+      require(_targetAddress != address(0),"zero address");
      bytes32 subnode = keccak256(abi.encodePacked(node, _label));
      address currentOwner = ens.owner(subnode);

      if (currentOwner != address(0)) {
          revert AlreadyRegistered(subnode, currentOwner);
      }

      ens.setSubnodeOwner(node, _label, address(this));
      ens.setResolver(subnode, resolver);
      Resolver(resolver).setAddr(subnode, _targetAddress); //@audit lack of zero address check.
  }
```