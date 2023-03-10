### [N-01] Incomplete Natspec

#### Findings:

1. File: packages/contracts/src/core/dao/DAO.sol (lines [96-109](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/dao/DAO.sol#L96-L109))

```
    /// @notice Initializes the DAO by
    /// - registering the [ERC-165](https://eips.ethereum.org/EIPS/eip-165) interface ID
    /// - setting the trusted forwarder for meta transactions
    /// - giving the `ROOT_PERMISSION_ID` permission to the initial owner (that should be revoked and transferred to the DAO after setup).
    /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
    /// @param _metadata IPFS hash that points to all the metadata (logo, description, tags, etc.) of a DAO.
    /// @param _initialOwner The initial owner of the DAO having the `ROOT_PERMISSION_ID` permission.
    /// @param _trustedForwarder The trusted forwarder responsible for verifying meta transactions.
    function initialize(
        bytes calldata _metadata,
        address _initialOwner,
        address _trustedForwarder,
        string calldata daoURI_
    ) external initializer {
```
Missing `@param daoURI_`.




2. File: packages/contracts/src/core/plugin/proposal/Proposal.sol (lines [38-52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L38-L52))
```
   /// @notice Internal function to create a proposal.
    /// @param _metadata The the proposal metadata.
    /// @param _startDate The start date of the proposal in seconds.
    /// @param _endDate The end date of the proposal in seconds.
    /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
    /// @param _actions The actions that will be executed after the proposal passes.
    /// @return proposalId The ID of the proposal.
    function _createProposal(
        address _creator,
        bytes calldata _metadata,
        uint64 _startDate,
        uint64 _endDate,
        IDAO.Action[] calldata _actions,
        uint256 _allowFailureMap
    ) internal virtual returns (uint256 proposalId) {
```
Missing `@param _creator`.


3.File: packages/contracts/src/core/plugin/proposal/Proposal.sol (lines [66-77](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/Proposal.sol#L66-L77))

```
    /// @notice Internal function to execute a proposal.
    /// @param _proposalId The ID of the proposal to be executed.
    /// @param _actions The array of actions to be executed.
    /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
    /// @return execResults The array with the results of the executed actions.
    /// @return failureMap The failure map encoding which actions have failed.
    function _executeProposal(
        IDAO _dao,
        uint256 _proposalId,
        IDAO.Action[] memory _actions,
        uint256 _allowFailureMap
    ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {
```
Missing `@param _dao`.

4.File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol (lines [38-52](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L38-L52))

```
    /// @notice Internal function to create a proposal.
    /// @param _metadata The the proposal metadata.
    /// @param _startDate The start date of the proposal in seconds.
    /// @param _endDate The end date of the proposal in seconds.
    /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
    /// @param _actions The actions that will be executed after the proposal passes.
    /// @return proposalId The ID of the proposal.
    function _createProposal(
        address _creator,
        bytes calldata _metadata,
        uint64 _startDate,
        uint64 _endDate,
        IDAO.Action[] calldata _actions,
        uint256 _allowFailureMap
    ) internal virtual returns (uint256 proposalId) {

```
Missing `@param _creator`.


5.File: packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol (lines [66-77](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/plugin/proposal/ProposalUpgradeable.sol#L66-L77))

```
       /// @notice Internal function to execute a proposal.
    /// @param _proposalId The ID of the proposal to be executed.
    /// @param _actions The array of actions to be executed.
    /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
    /// @return execResults The array with the results of the executed actions.
    /// @return failureMap The failure map encoding which actions have failed.
    function _executeProposal(
        IDAO _dao,
        uint256 _proposalId,
        IDAO.Action[] memory _actions,
        uint256 _allowFailureMap
    ) internal virtual returns (bytes[] memory execResults, uint256 failureMap) {

```
Missing `@param _dao`.

6.File: packages/contracts/src/core/utils/auth.sol (lines [14-25](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/auth.sol#L14-L25))

```
/// @notice A free function checking if a caller is granted permissions on a target contract via a permission identifier that redirects the approval to a `PermissionCondition` if this was specified in the setup.
/// @param _where The address of the target contract for which `who` recieves permission.
/// @param _who The address (EOA or contract) owning the permission.
/// @param _permissionId The permission identifier.
/// @param _data The optional data passed to the `PermissionCondition` registered.
function _auth(
    IDAO _dao,
    address _where,
    address _who,
    bytes32 _permissionId,
    bytes calldata _data
) view {
```
Missing `@param _dao`.

7.File: packages/contracts/src/core/utils/CallbackHandler.sol (lines [27-34](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/core/utils/CallbackHandler.sol#L27-L34))

```
    /// @notice Handles callbacks to adaptively support ERC standards.
    /// @dev This function is supposed to be called via `_handleCallback(msg.sig, msg.data)` in the `fallback()` function of the inheriting contract.
    /// @param _callbackSelector The function selector of the callback function.
    /// @return The magic number registered for the function selector triggering the fallback.
    function _handleCallback(
        bytes4 _callbackSelector,
        bytes memory _data
    ) internal virtual returns (bytes4) {
```
Missing `@param _data`.



8.File: packages/contracts/src/framework/utils/TokenFactory.sol (lines [52-54](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/utils/TokenFactory.sol#L52-L54))

```
    /// @notice Thrown if token address is not ERC20.
    /// @param token The token address
    error TokenNotERC20(address token, bytes data);
```
Missing `@param data`.


9.File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol (lines [28-37](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L28-L37))

```
    /// @inheritdoc IPluginSetup
    function prepareInstallation(
        address _dao,
        bytes calldata _data
    )
        external
        virtual
        override
        returns (address plugin, PreparedSetupData memory preparedSetupData)
    {
```
Missing `@param _dao` & `@param _data`.


10.File: packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol (lines [99-103](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v1/CounterV1PluginSetup.sol#L99-L103))

```
    /// @inheritdoc IPluginSetup
    function prepareUninstallation(
        address _dao,
        SetupPayload calldata _payload
    ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```
Missing `@param _dao` & `@param _payload`.


11.File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol (lines [29-38](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L29-L38))

```
    /// @inheritdoc IPluginSetup
    function prepareInstallation(
        address _dao,
        bytes calldata _data
    )
        external
        virtual
        override
        returns (address plugin, PreparedSetupData memory preparedSetupData)
    {
```
Missing `@param _dao` & `@param _data`.


12.File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol (lines [100-110](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L100-L110))

```
    /// @inheritdoc IPluginSetup
    function prepareUpdate(
        address _dao,
        uint16 _currentBuild,
        SetupPayload calldata _payload
    )
        external
        view
        override
        returns (bytes memory initData, PreparedSetupData memory preparedSetupData)
    {
```
Missing `@param _dao`, `@param _currentBuild` & `@param _payload`.


13.File: packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol (lines [139-143](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/counter-example/v2/CounterV2PluginSetup.sol#L139-L143))

```
    /// @inheritdoc IPluginSetup
    function prepareUninstallation(
        address _dao,
        SetupPayload calldata _payload
    ) external virtual override returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```
Missing `@param _dao` & `@param _payload`.

14.File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol (lines [30-38](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVoting.sol#L30-L38))

```
    /// @notice Initializes the component.
    /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
    /// @param _dao The IDAO interface of the associated DAO.
    /// @param _votingSettings The voting settings.
    function initialize(
        IDAO _dao,
        VotingSettings calldata _votingSettings,
        address[] calldata _members
    ) external initializer {
```
Missing `@param _members`.


15.File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol (lines [24-28](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L24-L28))

```
    /// @inheritdoc IPluginSetup
    function prepareInstallation(
        address _dao,
        bytes calldata _data
    ) external returns (address plugin, PreparedSetupData memory preparedSetupData) {
```
Missing `@param _dao` & `@param _data`.

16.File: packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol (lines [86-90](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/addresslist/AddresslistVotingSetup.sol#L86-L90))

```
    /// @inheritdoc IPluginSetup
    function prepareUninstallation(
        address _dao,
        SetupPayload calldata _payload
    ) external view returns (PermissionLib.MultiTargetPermission[] memory permissions) {
```
Missing `@param _dao` & `@param _payload`.

17.File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol (lines [65-66](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L65-L66))

```
    /// @inheritdoc MajorityVotingBase
    function totalVotingPower(uint256 _blockNumber) public view override returns (uint256) {
```
Missing `@param _blockNumber`.


18.File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol (lines [70-79](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L70-L79))

```
   /// @inheritdoc MajorityVotingBase
    function createProposal(
        bytes calldata _metadata,
        IDAO.Action[] calldata _actions,
        uint256 _allowFailureMap,
        uint64 _startDate,
        uint64 _endDate,
        VoteOption _voteOption,
        bool _tryEarlyExecution
    ) external override returns (uint256 proposalId) {
```
Missing `@param _metadata`, `@param _actions`, `@param _allowFailureMap,`, `@param _startDate`, `@param _endDate`, `@param `, `@param _voteOption` & `@param _tryEarlyExecution`.


19.File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol (lines [136-137](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L136-L137))

```
    /// @inheritdoc IMembership
    function isMember(address _account) external view returns (bool) {
```
Missing `@param _account`.

20.File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol (lines [142-148](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L142-L148))

```
    /// @inheritdoc MajorityVotingBase
    function _vote(
        uint256 _proposalId,
        VoteOption _voteOption,
        address _voter,
        bool _tryEarlyExecution
    ) internal override {
```
Missing `@param _proposalId`, `@param _voteOption`, `@param _voter`, `@param _tryEarlyExecution`.

21.File: packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol (lines [187-192](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/token/TokenVoting.sol#L187-L192))

```
    /// @inheritdoc MajorityVotingBase
    function _canVote(
        uint256 _proposalId,
        address _account,
        VoteOption _voteOption
    ) internal view override returns (bool) {
```
Missing `@param _proposalId`, `@param _account`, `@param _voteOption`.


22.File: packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol (lines [444-453](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/majority-voting/MajorityVotingBase.sol#L444-L453))

```
    /// @notice Internal function to cast a vote. It assumes the queried vote exists.
    /// @param _proposalId The ID of the proposal.
    /// @param _voteOption The chosen vote option to be casted on the proposal vote.
    /// @param _tryEarlyExecution If `true`,  early execution is tried after the vote cast. The call does not revert if early execution is not possible.
    function _vote(
        uint256 _proposalId,
        VoteOption _voteOption,
        address _voter,
        bool _tryEarlyExecution
    ) internal virtual;
```
Missing `@param _voter`.

23.File: packages/contracts/src/plugins/governance/multisig/Multisig.sol (lines [121-129](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L121-L129))

```
    /// @notice Initializes the component.
    /// @dev This method is required to support [ERC-1822](https://eips.ethereum.org/EIPS/eip-1822).
    /// @param _dao The IDAO interface of the associated DAO.
    /// @param _multisigSettings The multisig settings.
    function initialize(
        IDAO _dao,
        address[] calldata _members,
        MultisigSettings calldata _multisigSettings
    ) external initializer {
```
Missing `@param _member`.

24.File: packages/contracts/src/plugins/governance/multisig/Multisig.sol (lines [198-213](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/governance/multisig/Multisig.sol#L198-L213))

```
    /// @notice Creates a new majority voting proposal.
    /// @param _metadata The metadata of the proposal.
    /// @param _actions The actions that will be executed after the proposal passes.
    /// @param _allowFailureMap A bitmap allowing the proposal to succeed, even if individual actions might revert. If the bit at index `i` is 1, the proposal succeeds even if the `i`th action reverts. A failure map value of 0 requires every action to not revert.
    /// @param _approveProposal If `true`, the sender will approve the proposal.
    /// @param _tryExecution If `true`, execution is tried after the vote cast. The call does not revert if early execution is not possible.
    /// @return proposalId The ID of the proposal.
    function createProposal(
        bytes calldata _metadata,
        IDAO.Action[] calldata _actions,
        uint256 _allowFailureMap,
        bool _approveProposal,
        bool _tryExecution,
        uint64 _startDate,
        uint64 _endDate
    ) external returns (uint256 proposalId) {
```
Missing `@param _startDate` & `@param _endDate`.

25.File: packages/contracts/src/plugins/token/MerkleDistributor.sol (lines [65-88](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/plugins/token/MerkleDistributor.sol#L65-L88))

```
    /// @inheritdoc IMerkleDistributor
    function claim(
        uint256 _index,
        address _to,
        uint256 _amount,
        bytes32[] calldata _proof
    ) external override {
        if (isClaimed(_index)) revert TokenAlreadyClaimed({index: _index});
        if (!_verifyBalanceOnTree(_index, _to, _amount, _proof))
            revert TokenClaimInvalid({index: _index, to: _to, amount: _amount});

        _setClaimed(_index);
        token.safeTransfer(_to, _amount);

        emit Claimed(_index, _to, _amount);
    }

    /// @inheritdoc IMerkleDistributor
    function unclaimedBalance(
        uint256 _index,
        address _to,
        uint256 _amount,
        bytes32[] memory _proof
    ) public view override returns (uint256) {
```
Missing `@param _index`, `@param _to`, `@param _amount` & `@param _proof`,




### [N-02] Duplicate Natspec

#### Findings:

1.File: packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol (lines [608-609](https://github.com/code-423n4/2023-03-aragon/blob/main/packages/contracts/src/framework/plugin/setup/PluginSetupProcessor.sol#L608-L609))



