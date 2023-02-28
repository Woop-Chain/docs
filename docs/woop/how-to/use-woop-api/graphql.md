---
description: How to access the WoopChain API using GraphQL

---

# Use GraphQL over HTTP

GraphQL can reduce the overhead needed for common queries. For example, instead of querying each
receipt in a block, GraphQL can get the same result with a single query for the entire block.

The [Woop GraphQL schema] describes the GraphQL implementation for Ethereum. Enable the GraphQL
service using [command line options](index.md#enable-api-access).

!!! note

    GraphQL is not supported over WebSocket.

Access the GraphQL endpoint at `http://<HOST>:<PORT>/graphql`. Configure `<HOST>` and `<PORT>`
using [`graphql-http-host`](../../reference/cli/options.md#graphql-http-host) and
[`graphql-http-port`](../../reference/cli/options.md#graphql-http-port). The default endpoint
is `http://127.0.0.1:8547/graphql`.

## GraphQL requests with cURL

[WoopChain JSON-RPC API methods](../../reference/api/index.md) with an equivalent
[GraphQL](graphql.md) query include a GraphQL request and result in the method example.

!!! example

    The following [`syncing`](../../reference/api/index.md#eth_syncing) request returns data
    about the synchronization status.

    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{ "query": "{syncing{startingBlock currentBlock highestBlock}}"}' http://localhost:8547/graphql
    ```

## GraphQL requests with GraphiQL app

The third-party tool, [GraphiQL](https://github.com/skevy/graphiql-app), provides a tabbed
interface for editing and testing GraphQL queries and mutations. GraphiQL also provides access to
the [Woop GraphQL schema] from within the app.

![GraphiQL](../../../assets/GraphiQL.png)

## Pending

`transactionCount` and `transactions` supports the Pending query.

!!! important

    Woop does not execute pending transactions so results from `account`, `call`, and `estimateGas`
    for Pending do not reflect pending transactions.

!!! example

    === "Pending transaction count"

        ```bash
        curl -X POST -H "Content-Type: application/json" --data '{ "query": "{pending {transactionCount}}"}' http://localhost:8547/graphql
        ```

    === "Result"

        ```bash
        {
          "data" : {
            "pending" : {
              "transactionCount" : 2
            }
          }
        }
        ```

!!! example

    === "Pending transactions"

        ```bash
        curl -X POST -H "Content-Type: application/json" --data '{ "query": "{pending {transactions{hash}}}"}' http://localhost:8547/graphql
        ```

    === "Result"

        ```bash
        {
          "data" : {
            "pending" : {
              "transactions" : [ {
                "hash" : "0xbb3ab8e2113a4afdde9753782cb0680408c0d5b982572dda117a4c72fafbf3fa"
              }, {
                "hash" : "0xf6bd6b1bccf765024bd482a71c6855428e2903895982090ab5dbb0feda717af6"
              } ]
            }
          }
        }
        ```

<!-- Links -->
