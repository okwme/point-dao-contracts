# Point DAO

Point DAO aims to facilitate [PartyBid](https://github.com/PartyDAO/partybid)-style collective ownership of Urbit galaxies.

This repo contains v1 of the protocol.

## Architecture

```
// Main treasury and governance controller. Funds and galaxies go here.
PointTreasury

// Generic governance. Controlled by PointTreasury.
PointGovernor

// ERC20 governance token with added voting capabilities.
Point

// Party-buy galaxies
GalaxyAsks.sol
```

# GalaxyAsks Mechanics

There are two main ways that GalaxyAsks enables Point DAO to acquire a galaxy:
 - A galaxy owner can call `swapGalaxy` to immediately transfer their galaxy to the DAO and receive 1000 POINT.
 - A galaxy owner can call `createAsk` to list their galaxy for sale. The owner asks for a certain ETH:POINT rate as well as some amount of POINT. If it is approved by governance, then anybody can contribute to filling the ask, and ultimately the DAO acquires the galaxy and distributes a total of 1000 POINT to the owner and contributors.
 
Note that a galaxy owner that transfers their galaxy to Point DAO via GalaxyAsks *can not* automatically get their galaxy back. Once Point DAO owns it, it's up to the DAO what they do with the galaxy. The motivation for the project was for the DAO to vote on Urbit proposals with their galaxies in perpetuity.

For example: Alice owns `~zod` and values it at 1000 ETH. She wants to sell it, but she also wants to retain some Urbit voting power. She can call `createAsk(0, 1*10**18, 100*10**18)` meaning she would like to sell `~zod` at 1 ETH per POINT, and she wants 100 POINT for herself. If governance approves this ask, then there will be a "PartyBuy" with 900 unallocated ETH remaining. Anybody can contribute until 900 ETH is hit. When the asking price is hit, Alice receives 900 ETH and 100 POINT, the DAO receives the galaxy, and the contributors can claim their fair share of the remaining 900 POINT.

See the GalaxyAsks [integration test](https://github.com/jgeary/point-dao-contracts/blob/master/contracts/test/GalaxyAsks.integration.t.sol) to see a thorough example of how it works in code.

# Governance and Urbit Proposals
The governance system uses standard general-purpose openzeppelin governance contracts, so it is battle-tested and compatible with tools like [Tally](https://www.withtally.com/). In the interest of minimizing transaction fees, the DAO should do snapshot votes for each Urbit proposal to get a yes/no answer, and then a proposer can propose an onchain transaction which would submit that winning answer to Urbit's Polls contract on behalf of each galaxy owned by the treasury.

# Token
There is a fixed supply of 281,600 POINT - 1000 POINT for all 256 Urbit galaxies and 10% intended for team, incentives, airdrops and grants. Only GalaxyAsks will be able to distribute any of the 256,000 POINT, so the vast majority of it will be locked for the foreseeable future. The DAO will likely lock the remaining 10% in a long-term vesting contract to ensure proper incentives.
