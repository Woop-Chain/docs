---
description: Woopchain genesis file

---

# Genesis file


WoopChain Mainnet Genesis

    ```json
    {
  "config" : {
    "chainId" : 139,
    "homesteadBlock" : 0,
    "eip150Block" : 0,
    "eip155Block" : 0,
    "eip158Block" : 0,
    "byzantiumBlock" : 0,
    "constantinopleBlock" : 0,
    "petersburgBlock" : 0,
    "istanbulBlock" : 0,
    "berlinBlock" : 0,
    "londonBlock" : 0,
    "qbft" : {
      "blockperiodseconds" : 2,
      "epochlength" : 28800,
      "requesttimeoutseconds" : 4,
      "blockreward" : "449220000000000000000",
      "baseFeePerGas" : "0x834",
      "miningbeneficiary" : "0x4E848893aA90EAF694a019C300b7E2EA2cA6a999"
    }
  },
  "transitions" : {
    "qbft" : [ {
      "block" : 189216000,
      "blockreward" : "0"
    } ]
  },
  "nonce" : "0x0",
  "timestamp" : "0x0",
  "gasLimit" : "0x1fffffffffffff",
  "difficulty" : "0x1",
  "mixHash" : "0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
  "coinbase" : "0x0000000000000000000000000000000000000000",
  "alloc" : {
    "0x6d60719Cb3aE54C37A517B256aAD94A90ada0A92" : {
      "balance" : "15000000000000000000000000000"
    }
  },
  "extraData" : "0xf87aa00000000000000000000000000000000000000000000000000000000000000000f854941fda38c831db197f03bcbc46427dca81638f25c494011793d01b4316bb8d1a075195a93d743b9aa9e99442d964ce7f12370774183c0297a1b3f4ec0a1f939457bb435543c6d8f00d4bb42e1c716eceec534a60c080c0"
}
    ```
