## Lines too long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible.

Here are some of the instances entailed:

[File: Proxy.sol#L11](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L11)
[File: TokenFactory.sol#L72-L74](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L72-L74)

## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instance below could be refactored conforming to most other instances in the code bases as follows:

[File: Proxy.sol#L5](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/utils/Proxy.sol#L5)

```diff
- import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
+ import {ERC1967Proxy} from "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
```
