At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L178
    https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L182
    https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L184

- Store `_actions.length` in stack

`uint256 actionsLength = _actions.length;`

- Don't explicitly set `i` to 0

`for (uint256 i; i < actionsLength;) {`

At: https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L150
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L170
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L260
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L287
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L328
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L331
https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/permission/PermissionManager.sol#L328

- Store `items.length` in stack

`uint256 itemsLength = items.length;
 for (uint256 i; i < items.length;) {`

- Use `_msgSender(), _msgData()` instead of `msg.sender, msg.data`

`emit Granted(_permissionId, _msgSender(), _where, _who, _condition);`
`emit Revoked(_permissionId, _msgSender(), _where, _who);`
`if (!isGranted(address(this), _msgSender(), _permissionId, _msgData())) {`
`who: _msgSender(),`