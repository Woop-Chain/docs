---
id: troubleshooting
title: Troubleshooting
description: "Troubleshooting section for WoopChain"
keywords:
  - docs
  - woopchain
  - woop
  - troubleshooting
  
---

# Troubleshooting

## `method=eth_call err="invalid signature"` error

When you are using a wallet to make a transaction with WoopChain, please make sure that in your wallet's local network setup:

1. The `chainID` is the right one. The default `chainID` for WoopChain Mainnet is `139`. 
 
2. Make sure that, on the “RPC URL”, field you use the JSON RPC port of the node you are connecting to.


## How to get a WebSocket URL

By default, when you run a WoopChain node, it exposes a WebSocket endpoint based on the chain location.
The URL scheme `wss://` is used for HTTPS links, and `ws://` for HTTP.

Localhost WebSocket URL:
````bash
ws://<JSON-RPC URL>:<PORT>/ws
````
Please note that the port number depends on the chosen JSON-RPC port for the node.

WoopChain WebSocket URL:
````bash
wss://rpc.woop.ai/ws
````

## `insufficient funds` error when trying to deploy a contract

If you get this error, please make sure you have enough funds on the desired address, and the address used is the correct one.<br/>
 





