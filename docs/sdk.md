# Javascript SDK

The Forta Agent Javascript SDK comes with a set of classes and type definitions to provide a consistent interface for developers to write their agents with. There are also some utility functions available for your convenience to do common operations like searching for an event in a transaction receipt.

## Handlers

The most relevant type definitions for agent developers are the `HandleBlock` and `HandleTransaction` types. They are function types with signatures of `(blockEvent: BlockEvent) => Promise<Finding[]>` and `(txEvent: TransactionEvent) => Promise<Finding[]>`, respectively. Your agent handlers must implement these types to process blocks and transactions as they are received.

Your agent must have a default export object with the `handleBlock` and/or `handleTransaction` properties that export the handler functions. You can export one or both of these, depending on your use case, but at least one must be provided. The return type of these functions is `Promise<Finding[]>`, meaning they are asynchronous functions that return an array of zero or more `Finding` objects.

## BlockEvent

When a block is mined and detected by a Forta scan node, it will generate a `BlockEvent` containing information such as the block hash and block number. It contains the following fields:

- `type` - specifies whether this was a block reorg or a regular block
- `network` - specifies which network the block was mined on (e.g. mainnet, ropsten, rinkeby, etc)
- `blockHash` (python: `block_hash`) - hash of the block
- `blockNumber` (python: `block_number`) - number of the block
- `block` - data object containing the following fields:
    - `difficulty`
    - `extraData` (python: `extra_data`)
    - `gasLimit` (python: `gas_limit`)
    - `gasUsed` (python: `gas_used`)
    - `hash`
    - `logsBloom` (python: `logs_bloom`)
    - `miner`
    - `mixHash` (python: `mix_hash`)
    - `nonce`
    - `number`
    - `parentHash` (python: `parent_hash`)
    - `receiptsRoot` (python: `receipts_root`)
    - `sha3Uncles` (python: `sha3_uncles`)
    - `size`
    - `stateRoot` (python: `state_root`)
    - `timestamp`
    - `totalDifficulty` (python: `total_difficulty`)
    - `transactions`
    - `transactionsRoot` (python: `transactions_root`)
    - `uncles`

## TransactionEvent

When a transaction is mined and detected by a Forta scan node, it will generate a `TransactionEvent` containing various information about the transaction. It contains the following fields:

- `type` - specifies whether this was from a block reorg or a regular block
- `network` - specifies which network the transaction was mined on (e.g. mainnet, ropsten, rinkeby, etc)
- `hash` - alias for `transaction.hash`
- `from` (python: `from_`) - alias for `transaction.from`
- `to` - alias for `transaction.to`
- `gasPrice` (python: `gas_price`) - alias for `transaction.gasPrice`
- `gasUsed` (python: `gas_used`) - alias for `receipt.gasUsed`
- `status` - alias for `receipt.status`
- `logs` - alias for `receipt.logs`
- `timestamp` - alias for `block.timestamp`
- `blockNumber` (python: `block_number`) - alias for `block.number`
- `blockHash` (python: `block_hash`) - alias for `block.hash`
- `addresses` - map of addresses involved in the transaction (generated from transaction to/from address, any event log address and trace data address if available)
- `block` - data object containing following fields:
    - `hash`
    - `number`
    - `timestamp`
- `transaction` - data object containing the following fields:
    - `hash`
    - `from` (python: `from_`)
    - `to`
    - `nonce`
    - `gas`
    - `gasPrice` (python: `gas_price`)
    - `value`
    - `data`
    - `r`
    - `s`
    - `v`
- `receipt` - receipt object containing the following fields:
    - `status`
    - `root`
    - `gasUsed`
    - `cumulativeGasUsed` (python: `cumulative_gas_used`)
    - `logsBloom` (python: `logs_bloom`)
    - `contractAddress` (python: `contract_address`)
    - `blockNumber` (python: `block_number`)
    - `blockHash` (python: `block_hash`)
    - `transactionIndex` (python: `transaction_index`)
    - `transactionHash` (python: `transaction_hash`)
    - `logs` - list of log objects with following fields:
        - `address`
        - `topics`
        - `data`
        - `logIndex` (python: `log_index`)
        - `blockNumber` (python: `block_number`)
        - `blockHash` (python: `block_hash`)
        - `transactionIndex` (python: `transaction_index`)
        - `transactionHash` (python: `transaction_hash`)
        - `removed`
- `traces` - only with tracing enabled; list of trace objects with following fields:
    - `blockHash` (python: `block_hash`)
    - `blockNumber` (python: `block_number`)
    - `subtraces`
    - `traceAddress` (python: `trace_address`)
    - `transactionHash` (python: `transaction_hash`)
    - `transactionPosition` (python: `transaction_position`)
    - `type`
    - `error`
    - `action` - object with following fields:
        - `callType` (python: `call_type`)
        - `to`
        - `from` (python: `from_`)
        - `input`
        - `value`
        - `init`
        - `address`
        - `balance`
        - `refundAddress` (python: `refund_address`)
    - `result` - object with following fields:
        - `gasUsed` (python: `gas_used`)
        - `address`
        - `code`

### filterEvent

A convenience function is provided on `TransactionEvent` to check for the existence of event logs called `filterEvent` (python: `filter_event`). For example, you could use it to filter all of the transfer logs of a particular ERC-20 token:

```javascript
const erc20TokenAddress = "0x123abc";
const transferEventSignature = "Transfer(address,address,uint256)";
const transfers = transactionEvent.filterEvent(
  transferEventSignature,
  erc20TokenAddress
);
console.log(`found ${transfers.length} transfers`);
```

The second argument for the contract address is optional. To better understand how event signatures are formed, see [this article](https://medium.com/mycrypto/understanding-event-logs-on-the-ethereum-blockchain-f4ae7ba50378).

## Finding

If an agent wants to flag a transaction/block because it meets some condition (e.g. flash loan attack), the handler function would return a `Finding` object. This object would detail the results of the finding and provide metadata such as the severity of the finding. A `Finding` object can only be created using the `Finding.fromObject` method which accepts the following properties:

- `name` - **required**; human-readable name of finding e.g. "High Gas"
- `description` - **required**; brief description e.g. "High gas used: 1,000,000"
- `alertId` (python: `alert_id`) - **required**; unique string to identify this class of finding, primarily used to group similar findings for the end user
- `protocol` - **required**; name of protocol being reported on e.g. "aave", defaults to "ethereum" if left blank
- `type` - **required**; indicates type of finding e.g. exploit or suspicious occurrence
- `severity` - **required**; indicates impact level of finding:
    - Critical - exploitable vulnerabilities, massive impact on users/funds
    - High - exploitable under more specific conditions, significant impact on users/funds
    - Medium - notable unexpected behaviours, moderate to low impact on users/funds
    - Low - minor oversights, negligible impact on users/funds
    - Info - miscellaneous behaviours worth describing
- `metadata` - optional; key-value map (both keys and values as strings) for providing extra information
- `everestId` (python: `everest_id`) - optional; [Everest](http://everest.link/) link for information about the specific project/protocol

## getJsonRpcUrl

A convenience function called `getJsonRpcUrl` (python: `get_json_rpc_url`) can be used to load a JSON-RPC URL for your agent. When running in production, this function will return a URL injected by the scan node that is running the agent. When running in development, this function will return the `jsonRpcUrl` property specified in your config file.

## getFortaConfig

A convenience function called `getFortaConfig` (python: `get_forta_config`) can be used to load your config file as an object to access any other properties.

## createBlockEvent

A utility function for writing tests. You can use `createBlockEvent` (python: `create_block_event`) to easily generate a `BlockEvent` object for your unit tests.

## createTransactionEvent

A utility function for writing tests. You can use `createTransactionEvent` (python: `create_transaction_event`) to easily generate a `TransactionEvent` object for your unit tests.