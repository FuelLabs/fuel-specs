# Transaction Validity

- [Transaction Lifecycle](#transaction-lifecycle)
- [Access Lists](#access-lists)
- [VM Precondition Validity Rules](#vm-precondition-validity-rules)
  - [Base Sanity Checks](#base-sanity-checks)
  - [Spending UTXOs and Created Contracts](#spending-utxos-and-created-contracts)
  - [Sufficient Balance](#sufficient-balance)
  - [Valid Signatures](#valid-signatures)
- [Predicate Verification](#predicate-verification)
- [Script Execution](#script-execution)
- [VM Postcondition Validity Rules](#vm-postcondition-validity-rules)
  - [Correct Change](#correct-change)
  - [State Changes](#state-changes)

## Transaction Lifecycle

Once a transaction is seen, it goes through several stages of validation, in this order:

1. [Pre-checks](#vm-precondition-validity-rules)
1. [Predicate verification](#predicate-verification)
1. [Script execution](#script-execution)
1. [Post-checks](#vm-postcondition-validity-rules)

## Access Lists

The validity rules below assume sequential transaction validation for side effects (i.e. state changes). However, by construction, transactions with disjoint write access lists can be validated in parallel, including with overlapping read-only access lists. Transactions with overlapping write access lists must be validated and placed in blocks in topological order.

UTXOs and contracts in the read-only and write-destroy access lists must exist (i.e. have been created previously) in order for a transaction to be valid. In other words, for a unique state element ID, the write-create must precede the write-destroy.

Read-only access list:

Write-destroy access list:

- For each [input `InputType.Coin`](../tx-format/input.md#inputcoin)
  - The [UTXO ID](../identifiers/utxo-id.md) `(txID, outputIndex)`
- For each [input `InputType.Contract`](../tx-format/input.md#inputcontract)
  - The [UTXO ID](../identifiers/utxo-id.md) `(txID, outputIndex)`
- For each [input `InputType.Message`](../tx-format/input.md#inputmessage)
  - The [message ID](../identifiers/utxo-id.md#message-id) `messageID`

Write-create access list:

- For each [output `OutputType.ContractCreated`](../tx-format/output.md#outputcontractcreated)
  - The contract ID `contractID`
- For each output
  - The [created UTXO ID](../identifiers/utxo-id.md)

Note that block proposers use the contract ID `contractID` for inputs and outputs of type [`InputType.Contract`](../tx-format/input.md#inputcontract) and [`OutputType.Contract`](../tx-format/output.md#outputcontract) rather than the pair of `txID` and `outputIndex`.

## VM Precondition Validity Rules

This section defines _VM precondition validity rules_ for transactions: the bare minimum required to accept an unconfirmed transaction into a mempool, and preconditions that the VM assumes to hold prior to execution. Chains of unconfirmed transactions are omitted.

For a transaction `tx`, UTXO set `state`, contract set `contracts`, and message set `messages`, the following checks must pass.

> **Note:** [InputMessages](../tx-format/input.md#inputmessage) where `input.dataLength > 0` are not dropped from the `messages` message set until they are included in a transaction of type `TransactionType.Script` with a `ScriptResult` receipt where `result` is equal to `0` indicating a successful script exit.

### Base Sanity Checks

Base sanity checks are defined in the [transaction format](../tx-format/index.md).

### Spending UTXOs and Created Contracts

```py
for input in tx.inputs:
    if input.type == InputType.Contract:
        if not input.contractID in contracts:
                return False
    elif input.type == InputType.Message:
        if not input.nonce in messages:
                return False
    else:
        if not (input.txID, input.outputIndex) in state:
            return False
return True
```

If this check passes, the UTXO ID `(txID, outputIndex)` fields of each contract input is set to the UTXO ID of the respective contract. The `txPointer` of each input is also set to the TX pointer of the UTXO with ID `utxoID`.

### Sufficient Balance

For each asset ID `asset_id` in the input and output set:

```py
def sum_data_messages(tx, asset_id) -> int:
    """
    Returns the total balance available from messages containing data
    """
    total: int = 0
    if asset_id == 0:
        for input in tx.inputs:
            if input.type == InputType.Message and input.dataLength > 0:
                total += input.amount
    return total

def sum_inputs(tx, asset_id) -> int:
    total: int = 0
    for input in tx.inputs:
        if input.type == InputType.Coin and input.asset_id == asset_id:
            total += input.amount
        elif input.type == InputType.Message and asset_id == 0 and input.dataLength == 0:
            total += input.amount
    return total

def sum_outputs(tx, asset_id) -> int:
    total: int = 0
    for output in tx.outputs:
        if output.type == OutputType.Coin and output.asset_id == asset_id:
            total += output.amount
    return total

def available_balance(tx, asset_id) -> int:
    """
    Make the data message balance available to the script
    """
    availableBalance = sum_inputs(tx, asset_id) + sum_data_messages(tx, asset_id)
    return availableBalance

def unavailable_balance(tx, asset_id) -> int:
    sentBalance = sum_outputs(tx, asset_id)
    gasBalance = gasPrice * gasLimit / GAS_PRICE_FACTOR
    # Size excludes witness data as it is malleable (even by third parties!)
    bytesBalance = size(tx) * GAS_PER_BYTE * gasPrice / GAS_PRICE_FACTOR
    # Total fee balance
    feeBalance = ceiling(gasBalance + bytesBalance)
    # Only base asset can be used to pay for gas
    if asset_id == 0:
        return sentBalance + feeBalance
    return sentBalance

# The sum_data_messages total is not included in the unavailable_balance since it is spendable as long as there 
# is enough base asset amount to cover gas costs without using data messages. Messages containing data can't
# cover gas costs since they are retryable.
return available_balance(tx, asset_id) >= (unavailable_balance(tx, asset_id) + sum_data_messages(tx, asset_id))
```

### Valid Signatures

```py
def address_from(pubkey: bytes) -> bytes:
    return sha256(pubkey)[0:32]

for input in tx.inputs:
    if (input.type == InputType.Coin || input.type == InputType.Message) and input.predicateLength == 0:
        # ECDSA signatures must be 64 bytes
        if tx.witnesses[input.witnessIndex].dataLength != 64:
            return False
        # Signature must be from owner
        if address_from(ecrecover_k1(txhash(), tx.witnesses[input.witnessIndex].data)) != input.owner:
            return False
return True
```

Signatures and signature verification are specified [here](./cryptographic-primitives.md#public-key-cryptography).

The transaction hash is computed as defined [here](../identifiers/transaction-id.md).

## Predicate Verification

For each input of type `InputType.Coin` or `InputType.Message`, and `predicateLength > 0`, [verify its predicate](../fuel-vm/index.md#predicate-verification).

## Script Execution

Given transaction `tx`, the following checks must pass:

If `tx.scriptLength == 0`, there is no script and the transaction defines a simple balance transfer, so no further checks are required.

If `tx.scriptLength > 0`, the script must be executed. For each asset ID `asset_id` in the input set, the free balance available to be moved around by the script and called contracts is `freeBalance[asset_id]`. The initial message balance available to be moved around by the script and called contracts is `messageBalance`:

```py
freeBalance[asset_id] = available_balance(tx, asset_id) - unavailable_balance(tx, asset_id)
messageBalance = sum_data_messages(tx, 0)
```

Once the free balances are computed, the [script is executed](../fuel-vm/index.md#script-execution). After execution, the following is extracted:

1. The transaction in-memory on VM termination is used as the final transaction which is included in the block.
1. The unspent free balance `unspentBalance` for each asset ID.
1. The unspent gas `unspentGas` from the `$ggas` register.

The fees incurred for a transaction are `ceiling(((size(tx) * GAS_PER_BYTE) + (tx.gasLimit - unspentGas)) * tx.gasPrice / GAS_PRICE_FACTOR)`.

If the transaction as included in a block does not match this final transaction, the block is invalid.

## VM Postcondition Validity Rules

This section defines _VM postcondition validity rules_ for transactions: the requirements for a transaction to be valid after it has been executed.

Given transaction `tx`, state `state`, and contract set `contracts`, the following checks must pass.

### Correct Change

If change outputs are present, they must have:

- if the transaction does not revert;
  - if the asset ID is `0`; an `amount` of `unspentBalance + floor((unspentGas * tx.gasPrice) / GAS_PRICE_FACTOR)`
  - otherwise; an `amount` of the unspent free balance for that asset ID after VM execution is complete
- if the transaction reverts;
  - if the asset ID is `0`; an `amount` of the initial free balance plus `(unspentGas * tx.gasPrice) - messageBalance`
  - otherwise; an `amount` of the initial free balance for that asset ID.

### State Changes

Transaction processing is completed by removing spent UTXOs from the state and adding created UTXOs to the state.

### Coinbase Transaction

The coinbase transaction is a mechanism for block creators to convert fees into spendable UTXOs.

In order for a coinbase transaction to be valid:

1. It must be a [Mint](../tx-format/transaction.md#TransactionMint) transaction.
2. The coinbase transaction must be the first transaction within a block, even if there are no other transactions in the block and the fee is zero.
3. The total output value of the coinbase transaction cannot exceed the total amount of fees processed from all other transactions within the same block.
4. The `asset_id` for coinbase transaction outputs must match the `asset_id` that fees are paid in (`asset_id == 0`).
