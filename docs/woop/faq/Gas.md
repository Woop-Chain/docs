---
id: gas
title: Gas FAQ
description: "Gas FAQ for WoopChain"
keywords:
  - docs
  - woopchain
  - woop
  - FAQ
  - validators
  
---

## How to enforce a minimum gas price?
You can use the `--price-limit` flag provided on the server command. This will enforce your node to only accept transactions that have the gas higher or equal to the price limit you set. To make sure it is enforced on the entire network, you need to make sure all the nodes have the same price limit.

## Does Woop support ERC-20 as a gas token?

Woop doesn't support ERC-20 token as gas token. Only the native Woop currency is supported for gas.

## How to increase the gas limit?

There are two options for increasing the gas limit in WoopChain:
1. Wiping the chain and increasing `block-gas-limit` to maximum uint64 value in the genesis file
2. Use the `--block-gas-target` flag with a high value to increase the gas limit of all nodes. This requires node reboot. Detailed explanation [here](/docs/woop/architecture/modules/txpool/#block-gas-target).