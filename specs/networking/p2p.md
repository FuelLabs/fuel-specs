# p2p

It is a system that allows peer to peer (p2p) low level communication between peers. As it currently stands we are going to use libp2p as the protocol for communication. Bear in mind that libp2p supports discovery of peers, versioning of API and stream multiplexing and they are all desired features.

* multiplexing: https://docs.libp2p.io/concepts/stream-multiplexing/
* libp2p protocols: https://docs.libp2p.io/concepts/protocols/

Every p2p API should have its size limit and its request timeout. In case of these things happen or in case that malformed or unexpected data is received, we should disconnect that peer. Improvements in eth/64 is kinda desirable but not crucial for the first version (it allows better forking when network upgrade happens). It is interesting to think about having some kind of reputation mechanism on peer inbound data but it is not necessary for the first version and it is up to the client to implement it on its own accord.

# API

All requests should have `RequestId` attached to messages so that the handlers should be easier to implement and it would allow parallel requests to be sent from one peer to another.

Because these are binary streams we need to decide what exactly serialization protocol for binary representation we are going to use.

For the naming of API it is proposed that we have `Request`, `Response`, `Broadcast` prefixes, where `greeting`/`hello` would be one exception that is sent/received only at the beginning of communication between peers.

The notation used to define binary format is rust based:
* Data array of N elements is: `[data;N]` where if we don't need to know a number of elements we can use use `[data]` to represent a list.
* Serialized structure is with three elements is: `(a,b,c)`
* union data is specified as: `a | b`

## Transactions

Transaction propagation is simple and consists of sending body or hash of transaction and allowing fetching of a transaction from peers txpool. You can see similar protocol implemented in Ethereum and most recently eth/65 improvement. It connects the sync layer with the txpool layer. With the requirement of txpool that when sending new transactions we need to send their ancestors, we should probably revise tx API to accommodate that.

Proposal for Transaction API:
* **RequestTransactions**: request `N` number of transactions by specifying their hashes.
 * Format: `[TxHash: N]`
* **RespondTransactions**: respond with a max of `N` transactions that were requested. An unknown transaction can be omitted.
 * Format: `[TxBody; N]`
* **BroadcastTransactionHashes**: 
 * When transactions are received from this same API we should include them inside txpool and after that rebroadcast them to other peers if this is a new transaction.
 * Because of structure of UTXO and potential gaps that can happen if UTXO's are dependent on another,and peer does not have its predecesor it is advised when broadcasting txhash to include all needed predecesor so that for other peers can use this and include new transaction.
 * It is advised that the order of txhashes should be sorted by GasPrice so that txpool can insert depending on transactions in the right order.
 * Do broadcasting to 2/ 3 of randomly selected peers.
 * Format: `[TxHash; N]`
* **BroadcastTransactions**:
 * When a new transaction is received from user space we are sure that no other peers have it, so it is best to directly broadcast txbody to peers and not bother with ceremony of hashes.
 * Additional if a transaction has a dependency, include their hashes so that peers can fetch it.
 * Do broadcasting to 2/ 3 of randomly selected peers.
 * Format: `[(TxBody,[TxHash]);N]`

## Blocks

It has similar mechanisms as transaction API.
* **BroadcastBlockHashes**:
 * When a new block is inserted in the canonical chain, broadcast its hash to randomly selected 2/3 of available peers.
 * Format: `[BlockHash]`
* **BroadcastBlock**:
 * When a new block is created by our peer broadcasts its full data to everyone.
 * Format: `[Block]`
* **RequestBlockHeaders**:
 * Request `N` block headers in `Ascending/Descending` order starting from `BlockNumber|BlockHash`. Allow that we specify `Step` that will take every `step` number block apart.
 * Format: ``(N,Ascending, Step, BlockNumber|BlockHash)``
* **RespondBlockHeaders**
 * Format: `[BlockHeader;N]`
* **RequestBlockBodies**
 * Format: `[BlockHash;N]`
* **RespondBlockBodies**
 * Format: `[BlockBody;N]`
* **RequestBlockReceipts**
 * Format: `[BlockHash;N]`
* **RespondBlockReceipts**
 * Format: `[BlockReceipt;N]`

## Consensus

TBD


TBD if we need a `greeting` package as the first message that is going to be sent/received.