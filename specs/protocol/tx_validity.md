# Transaction Validity

- [Transaction Lifecycle](#transaction-lifecycle)
- [VM Precondition Validity Rules](#vm-precondition-validity-rules)
    - [Base Sanity Checks](#base-sanity-checks)
    - [Spending UTXOs and Created Contracts](#spending-utxos-and-created-contracts)
    - [Sufficient Balance](#sufficient-balance)
    - [Valid Signatures](#valid-signatures)
- [Predicate Verification](#predicate-verification)
- [Script Execution](#script-execution)
- [VM Postcondition Validity Rules](#vm-postcondition-validity-rules)
    - [No Inflation](#no-inflation)
    - [State Changes](#state-changes)

## Transaction Lifecycle

Once a transaction is seen, it goes through several stages of validation, in this order:
1. [Pre-checks](#vm-precondition-validity-rules)
1. [Predicate verification](#predicate-verification)
1. [Script execution](#script-execution)
1. [Post-checks](#vm-postcondition-validity-rules)

## VM Precondition Validity Rules

This section defines _vm precondition validity rules_ for transactions: the bare minimum required to accept an unconfirmed transaction into a mempool, and preconditions that the VM assumes to hold prior to execution. Chains of unconfirmed transactions are omitted.

The validity rules assuming sequential transaction validation for side effects (i.e. state changes). However, by construction, transactions with different access lists can be validated in parallel. Transactions with overlapping access lists must be validated and placed in blocks in topological order.

For a transaction `tx`, state `state`, and contract set `contracts`, the following checks must pass.

### Base Sanity Checks

Base sanity checks are defined in the [transaction format](./tx_format.md).

### Spending UTXOs and Created Contracts

```py
for input in tx.inputs:
    if input.type == InputType.Contract:
        if not input.contractID in contracts:
                return False
    else:
        if not input.utxoID in state:
            return False
return True
```

If this check passes, the `utxoID` field of each input is set to the UTXO ID of the respective contract.

### Sufficient Balance

```py
def sum_inputs(tx) -> int:
    total: int = 0
    for input in tx.inputs:
        if input.type == InputType.Coin:
            total += state[input.utxoID].amount
    return total

def sum_outputs(tx) -> int:
    total: int = 0
    for output in tx.outputs:
        if output.type == OutputType.Coin:
            total += output.amount
    return total

def available_balance(tx) -> int:
    availableBalance = sum_inputs(tx)
    return availableBalance

def unavailable_balance(tx) -> int:
    """
    Note: we don't charge for predicate verification because predicates are
    monotonic and the cost of Ethereum calldata more than makes up for this
    """
    sentBalance = sum_outputs(tx)
    gasBalance = gasPrice * gasLimit
    bytesBalance = size(tx) * GAS_PER_BYTE * gasPrice
    return sentBalance + gasBalance + bytesBalance

return available_balance(tx) >= unavailable_balance(tx)
```

### Valid Signatures

```py
def address_from(pubkey: bytes) -> bytes:
    return sha256(pubkey)[0:32]

for input in tx.inputs:
    if input.type == InputType.Coin:
        if address_from(ecrecover(txhash(), tx.witnesses[input.witnessIndex])) != state[input.utxoID].owner:
            return False
return True
```

The transaction hash is computed as defined [here](./identifiers.md#transaction-id).

## Predicate Verification

For each input of type `InputType.Coin` and `predicateLength > 0`, [verify its predicate](../vm/main.md#predicate-verification).

## Script Execution

Given transaction `tx`, the following checks must pass:

If `tx.scriptLength == 0`, there is no script and the transaction defines a simple balance transfer, so no further checks are required.

If `tx.scriptLength > 0`, the script must be executed. The free balance available to be moved around by the script and called contracts is `freeBalance`:

```py
freeBalance = available_balance(tx) - unavailable_balance(tx)
```

Once the free balance is computed, the [script is executed](../vm/main.md#script-execution) and the transaction in memory on VM termination is used as the final transaction which is included in the block, i.e.:

```
tx = MEM[40, MEM[32, 8]]
```

If the transaction as included in a block does not match the final transaction, the block is invalid.

## VM Postcondition Validity Rules

This section defines _VM postcondition validity rules_ for transactions: the requirements for a transaction to be valid after it has been executed.

Given transaction `tx`, state `state`, and contract set `contracts`, the following checks must pass.

### No Inflation

```py
def sum_all_inputs(tx) -> int:
    total: int = 0
    for input in tx.inputs:
        total += state[input.utxoID].amount
    return total

def sum_all_outputs(tx) -> int:
    total: int = 0
    for output in tx.outputs:
        if output.type != OutputType.ContractCreated:
            total += output.amount
    return total

return sum_all_inputs(tx) >= sum_all_outputs(tx)
```

### State Changes

Transaction processing is completed by removing spent UTXOs from the state and adding created UTXOs to the state.
