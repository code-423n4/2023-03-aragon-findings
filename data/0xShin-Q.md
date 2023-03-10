# Title
Lack of Subdomain Validation in i`isSubdomainValid` Function

## Links to affected code
https://github.com/aragon/osx/blob/4fcb8bc06356350a2725b35bad7cbe95dd188f63/packages/contracts/src/framework/utils/RegistryUtils.sol#L13

## Impact
The current implementation of the isSubdomainValid function lacks validation checks for the length of the subdomain string, and whether the string starts or ends with a dash.

## Proof of Concept
Currently, changing the subdomain name in packages/contracts/test/framework/dao/dao-factory.ts such as [const daoDummySubdomain = '-dao1'; ](https://github.com/aragon/osx/blob/4fcb8bc06356350a2725b35bad7cbe95dd188f63/packages/contracts/test/framework/dao/dao-factory.ts#L89) does not cause the test creates a dao with a plugin and emits correct events to fail. However, it should fail because the subdomain name violates the ENS DNS specifications.
## Tools Used
Manual
## Recommended Mitigation Steps
Here's a proposed addition to the validation for the isSubdomainValid function:
```
// Check if the string is too long
if (nameLength > 63) {
    return false;
}

// Check if the string starts or ends with a dash
if (nameBytes[0] == 45 || nameBytes[nameLength - 1] == 45) {
    return false;
}

// Check if the string starts with a dash
if (nameBytes[0] == 45) {
    return false;
}
```

