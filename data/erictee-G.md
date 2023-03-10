### [G-01] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
src/framework/dao/DAOFactory.sol:L95        for (uint256 i; i < _pluginSettings.length; ++i) {

src/framework/utils/RegistryUtils.sol:L16    for (uint256 i; i < nameLength; i++) {

```
