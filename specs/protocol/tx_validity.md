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

- For each [input `InputType.Coin`](./tx_format.md#inputcoin)
  - The UTXO ID `(txID, outputIndex)`
- For each [input `InputType.Contract`](./tx_format.md#inputcontract)
  - The UTXO_ID of `(txID, outputIndex)`

Write-create access list:

- For each [output `OutputType.OutputContractCreated`](./tx_format.md#outputcontractcreated)
  - The contract ID `contractID`
- For each output
  - The [created UTXO ID](./identifiers.md#utxo-id)

Note that block proposers use the contract ID `contractID` for inputs and outputs of type [`InputType.Contract`](./tx_format.md#inputcontract) and [`OutputType.Contract`](./tx_format.md#outputcontract) rather than the pair of `txID` and `outputIndex`.

## VM Precondition Validity Rules

This section defines _VM precondition validity rules_ for transactions: the bare minimum required to accept an unconfirmed transaction into a mempool, and preconditions that the VM assumes to hold prior to execution. Chains of unconfirmed transactions are omitted.

For a transaction `tx`, UTXO set `state`, and contract set `contracts`, the following checks must pass.

### Base Sanity Checks

Base sanity checks are defined in the [transaction format](./tx_format.md).

### Spending UTXOs and Created Contracts

```py
for input in tx.inputs:
    if input.type == InputType.Contract:
        if not input.contractID in contracts:
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
def sum_inputs(tx, col) -> int:
    total: int = 0
    for input in tx.inputs:
        if input.type == InputType.Coin and input.asset_id == col:
            total += input.amount
    return total

def sum_outputs(tx, col) -> int:
    total: int = 0
    for output in tx.outputs:
        if (output.type == OutputType.Coin or output.type == OutputType.Withdrawal) and output.asset_id == col:
            total += output.amount
    return total

def available_balance(tx, col) -> int:
    availableBalance = sum_inputs(tx, col)
    return availableBalance

def unavailable_balance(tx, col) -> int:
    """
    Note: we don't charge for predicate verification because predicates are
    monotonic and the cost of Ethereum calldata more than makes up for this
    """
    sentBalance = sum_outputs(tx, col)
    gasBalance = gasPrice * gasLimit
    # Size excludes witness data as it is malleable (even by third parties!)
    bytesBalance = size(tx) * gasPrice * GAS_PER_BYTE
    # Only base asset can be used to pay for gas
    if col != 0:
        return sentBalance
    return sentBalance + gasBalance + bytesBalance

return available_balance(tx, col) >= unavailable_balance(tx, col)
```

### Valid Signatures

```py
def address_from(pubkey: bytes) -> bytes:
    return sha256(pubkey)[0:32]

for input in tx.inputs:
    if input.type == InputType.Coin and input.predicateLength == 0:
        # ECDSA signatures must be 64 bytes
        if tx.witnesses[input.witnessIndex].dataLength != 64:
            return False
        # Signature must be from owner
        if address_from(ecrecover(txhash(), tx.witnesses[input.witnessIndex].data)) != input.owner:
            return False
return True
```

Signatures and signature verification are specified [here](./cryptographic_primitives.md#public-key-cryptography).

The transaction hash is computed as defined [here](./identifiers.md#transaction-id).

## Predicate Verification

For each input of type `InputType.Coin` and `predicateLength > 0`, [verify its predicate](../vm/main.md#predicate-verification).

## Script Execution

Given transaction `tx`, the following checks must pass:

If `tx.scriptLength == 0`, there is no script and the transaction defines a simple balance transfer, so no further checks are required.

If `tx.scriptLength > 0`, the script must be executed. For each asset ID `asset_id` in the input set, the free balance available to be moved around by the script and called contracts is `freeBalance[asset_id]`:

```py
freeBalance[col] = available_balance(tx, col) - unavailable_balance(tx, col)
```

Once the free balances are computed, the [script is executed](../vm/main.md#script-execution). After execution, the following is extracted:

1. The transaction in-memory on VM termination is used as the final transaction which is included in the block.
1. The unspent free balance `unspentBalance` for each asset ID.
1. The unspent gas `unspentGas` from the `$ggas` register.

The fees incurred for a transaction are `(tx.gasLimit - unspentGas) * tx.gasPrice`.

If the transaction as included in a block does not match this final transaction, the block is invalid.

## VM Postcondition Validity Rules

This section defines _VM postcondition validity rules_ for transactions: the requirements for a transaction to be valid after it has been executed.

Given transaction `tx`, state `state`, and contract set `contracts`, the following checks must pass.

### Correct Change

If change outputs are present, they must have:

1. if the transaction does not revert; an `amount` of `unspentBalance + unspentGas * tx.gasPrice` if their asset ID is `0`, or an `amount` of the unspent free balance for that asset ID after VM execution is complete, or
1. if the transaction reverts; an `amount` of the initial free balance plus `unspentGas * tx.gasPrice` if their asset ID is `0`, or an `amount` of the initial free balance for that asset ID.

### State Changes

Transaction processing is completed by removing spent UTXOs from the state and adding created UTXOs to the state.
