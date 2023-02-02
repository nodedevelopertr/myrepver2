# Manage Your Scanner Pools

## Introduction

A scanner pool contains a group of nodes that scan a specific chain. Each pools belongs to a specific owner and is minted as an NFT (ERC-721) upon registration. These pools require the operators to deposit a total amount of stake which covers the minimum-per-scan-node amount for each node.

When registering any node to a pool, make sure to stake this minimum amount of **2500 FORT** on the pool first.

In addition, the scanner pools allow other FORT token holders (delegators) to stake on your pool and earn rewards. As the pool operator, you can decide

- how much of the delegator or operator stake to be allocated,
- how much commission you want from delegators' rewards.

## Depositing stake

### Depositing on your own pool

### Delegating to a pool

## Creating new nodes

Each node needs to be registered to a specific pool in order to be operational. While one can register many scan nodes on a pool, the pool needs to be populated with more stake first in order to avoid registration issues. This is for preventing pool shutdown conditions that occur when the average per-scanner stake is below minimum.

**TODO:** Add screenshots and describe the steps.

## Disabling existing nodes

If a node is no longer used, it should be disabled to free up allocated stake for newer nodes.

**TODO:** Add screenshots and describe the steps.

## Adjusting stake allocation

While it's always possible to add more stake to a pool, it is allocated proportional to the amount of scan nodes in the pool. Total allocated stake can only be `node_count × max_stake_per_scan_node`.

For example, if there are two scan nodes in a pool, a total of 25k FORT deposited and only 5k FORT worth of stake is allowed per scan node, then the pool can allocate a maximum of `5k × 2 = 10k` FORT in total. This is the amount that is effective in calculating the rewards and the rest of `25k - 10k = 15k` remains unallocated until more scan nodes are registered to the pool.

It is up to the pool owner to allocate more from own stake or more from the delegators so the share of the rewards can be adjusted accordingly.

### Adjusting own stake

**TODO:** Add screenshots and describe the steps.

### Adjusting delegator stake

**TODO:** Add screenshots and describe the steps.

## Changing the commission

The pool owners can adjust the ratio of the shares they would like to keep for themselves or the delegators, by changing the allocated amounts as described in the previous sections. It can be the case that it's more desirable for the operator to not stake more on the pool but let the delegators fill up the available allocation capacity.

In this case, the commission allows the operators to earn a portion of the delegators' rewards.

**TODO:** Add screenshots and describe the steps.

## Editing scanner pool information

**TODO:** Add screenshots and describe the steps.

## Claiming rewards

!!! warning "Phase II feature"
    This is a feature that will be activated and useful in the second phase of the transition to delegated staking. At the time of reading these docs, the feature may not be available on Forta App just yet. The rewards accrued in FORT will be forwarded to owner wallets until this feature is available.

After the rewards are calculated at the end of each epoch, they are transferred to the rewards distributor contract. This contract allows the pool owner and the delegators to claim their rewards.

**TODO:** Add screenshots and describe the steps.