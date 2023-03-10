https://github.com/aragon/osx/blob/a2461ae61a8c4cc833a117120b76e306936f5e1c/packages/contracts/src/token/ERC20/governance/GovernanceERC20.sol#L106-L107

This mint function has a callback in mint(to). Functions with callbacks should have reentrancy guards in place for protection against possible malicious actors both from inside and outside the protocol. Add a reentrancy guard modifier on the mint() function.

Furthermore use Safemint() rather than mint().