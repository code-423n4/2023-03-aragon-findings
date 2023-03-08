## 1. Potential HTML/Javascript injection aka XSS
### Issue: 
The data which is submitted with deposit in variable [_reference](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L221) is supposedly to contain some string which will be later logged in. Unless its purpose is more than just being emitted in a block explorer, any user-originating strings should be considered potentially malicious, as if the reference is later displayed e.g. on the frontend layer, it may lead to vulnerabilities such as XSS or similar. 
### Remediation: 
If purpose of that user-originating strings is to be displayed somewhere, it should be validated against non-alphanumeric characters, especially script elements such as '>', '<', '"' etc. 

## 2. Users can deposit arbitrary tokens
### Issue: 
The (DAO contract)[https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol] allows for depositing any ERC20 tokens. It should be noted, that this broadens potential attack surface, since it opens up possibility to deposit non-standard tokens or exploit tokens (when an exploit scenario is applicable).
### Remediation: 
It might be worth considering to implement an allowlist of tokens allowed by the DAO, so only these from the list are registered and allowed within the protocol. This way, potential attack surface will be much lower, as there will be less possibilities that maliciously or weirdly behaving tokens will be used.

## 3. Initialization may be frontrun

### Issue: 
The initialization routine is present in following contracts:

- plugins/governance/multisig/Multisig.sol
- plugins/governance/majority-voting/token/TokenVoting.sol
- plugins/governance/majority-voting/addresslist/AddresslistVoting.sol
- plugins/governance/admin/Admin.sol
- plugins/token/MerkleDistributor.sol
- plugins/token/MerkleMinter.sol
- plugins/counter-example/v1/CounterV1.sol
- plugins/counter-example/v2/CounterV2.sol
- token/ERC20/governance/GovernanceERC20.sol
- token/ERC20/governance/GovernanceWrappedERC20.sol
- framework/plugin/repo/PluginRepo.sol
- framework/plugin/repo/PluginRepoRegistry.sol
- framework/dao/DAORegistry.sol
- framework/utils/ens/ENSSubdomainRegistrar.sol

The initialization has no access control, which means, it can be called by anyone. Although the contract is usually initialized upon deployment, there is a non-zero probability that an attacker may try to frontrun such initialization and as a result, become an owner and/or set key parameters in some contracts. 

### Remediation: 
After the deployment and initialization of each contract it should be verified if the initialization was done properly and if the intended owner is in place. 

 