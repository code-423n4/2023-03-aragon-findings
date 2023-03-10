# State variables only set in the constructor should be declared immutable
File: packages/contracts/src/framework/utils/TokenFactory.sol

    27:     address public governanceERC20Base;
    30:     address public governanceWrappedERC20Base;
    33:     address public merkleMinterBase;
    36:     MerkleDistributor public distributorBase;
    
File: packages/contracts/src/framework/utils/ens/ENSSubdomainRegistrar.sol

    26:     ENS public ens;
    29:     bytes32 public node;

File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol

    26:    IVotesUpgradeable private votingToken;

File: packages/contracts/src/framework/dao/DAORegistry.sol

    18:    ENSSubdomainRegistrar public subdomainRegistrar;

File: packages/contracts/src/framework/plugin/repo/PluginRepoRegistry.sol

    20:    ENSSubdomainRegistrar public subdomainRegistrar;

File: packages/contracts/src/framework/plugin/repo/PluginRepoFactory.sol

    15:    PluginRepoRegistry public pluginRepoRegistry;
    18:    address public pluginRepoBase;

File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

    128:    PluginRepoRegistry public repoRegistry;

File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol

    20:    MultiplyHelper public multiplyHelperBase;
    21:    CounterV2 public counterBase;

File: packages/contracts/src/plugins/counter-example/v2/CounterV2.sol

    17:    uint256 public count;
    20:    MultiplyHelper public multiplyHelper;

File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol

    20:    MultiplyHelper public multiplyHelperBase;
    21:    CounterV1 public counterBase;

File: packages/contracts/src/plugins/counter-example/v1/CounterV1.sol

    17:    uint256 public count;
    20:    MultiplyHelper public multiplyHelper;

File: packages/contracts/src/plugins/token/MerkleDistributor.sol

    23:    IERC20Upgradeable public override token;
    26:    bytes32 public override merkleRoot;

File: packages/contracts/src/plugins/token/MerkleMinter.sol

    31:    IERC20MintableUpgradeable public override token;

# State variables should be cached in stack variables rather than re-reading them from storage
File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol

    314:        Proposal storage proposal_ = proposals[_proposalId];
    327:        Proposal storage proposal_ = proposals[_proposalId];
    342:        Proposal storage proposal_ = proposals[_proposalId];
    407:        Proposal storage proposal_ = proposals[_proposalId];
    484:        Proposal storage proposal_ = proposals[_proposalId];
    515:        function _isProposalOpen(Proposal storage proposal_) internal view virtual returns (bool)

File: packages/contracts/src/plugins/governance/multisig/Multisig.sol

    318:        Proposal storage proposal_ = proposals[_proposalId];
    366:        Proposal storage proposal_ = proposals[_proposalId];
    390:        Proposal storage proposal_ = proposals[_proposalId];
    403:    function _isProposalOpen(Proposal storage proposal_) internal view returns (bool) {

File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol

    654:        PluginState storage pluginState = states[pluginInstallationId];

File: 