# Transaction Validity

- [Standardness Rules](#standardness-rules)
    - [Version](#version)
    - [Spending UTXOs or Created Contracts](#spending-utxos-or-created-contracts)
    - [Transaction Maturity](#transaction-maturity)
    - [Input Maturity](#input-maturity)
    - [Counts](#counts)
    - [Script Length](#script-length)
    - [Predicate Lengths](#predicate-lengths)
    - [Sufficient Balance](#sufficient-balance)
    - [Valid Signatures](#valid-signatures)
    - [Predicate Validity](#predicate-validity)
- [Validity Rules](#validity-rules)
    - [No Inflation](#no-inflation)

## Standardness Rules

This section defines _standardness rules_ for transactions: the bare minimum required to accept an unconfirmed transaction into a mempool. Chains of unconfirmed transactions are omitted.

For a transaction `tx`, state `state`, and contract set `contracts`, the following checks must pass.

### Version

```py
return tx.version == 0
```

### Spending UTXOs or Created Contracts

```py
for input in tx.inputs:
    if input.type == InputType.Coin:
        if not input.utxoID in state:
            return False
    if input.type == InputType.Contract:
        if not input.contractID in contracts:
return True
```

### Transaction Maturity

```py
return blockheight() >= tx.maturity
```

### Input Maturity

```py
for input in tx.inputs:
    if input.type == InputType.Coin:
        if blockheight() < state[input.utxoID].created + input.maturity:
            return False
return True
```

### Counts

```py
return (
    tx.inputsCount <= MAX_INPUTS and
    tx.outputsCount <= MAX_OUTPUTS and
    tx.witnessesCount <= MAX_WITNESSES
)
```

### Script Length

```py
return (
    scriptLength <= MAX_SCRIPT_LENGTH and
    scriptDataLength <= MAX_SCRIPT_DATA_LENGTH
)
```

### Predicate Lengths

```py
for input in tx.inputs:
    if input.type == InputType.Coin:
        if (
            input.predicateLength > MAX_PREDICATE_LENGTH or
            input.predicateDataLength > MAX_PREDICATE_DATA_LENGTH
        ):
            return False
return True
```

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
    return sha256(pubkey)[0:31]

for input in tx.inputs:
    if input.type == InputType.Coin:
        if address_from(ecrecover(txhash(), tx.witnesses[input.witnessIndex])) != state[input.utxoID].owner:
            return False
return True
```

### Predicate Validity

For each input of type `InputType.Coin` and `predicateLength > 0`, [verify its predicate](./main.md#predicate-verification).

## Validity Rules

This section defines _validity rules_ for transactions: the requirements for a confirmed transaction to be valid.

Given transaction `tx`, state `state`, and contract set `contracts`:

If `tx.scriptLength == 0`, there is no script and the transaction defines a simple balance transfer, and no further checks are required. Transaction processing is completed by removing spent UTXOs from the state and adding created UTXOs to the state.

If `tx.scriptLength > 0`, the script must be executed. The free balance available to be moved around by the script and called contracts is `freeBalance`:

```py
freeBalance = available_balance(tx) - unavailable_balance(tx)
```

The following checks must pass.

### No Inflation

```py
def sum_all_inputs(tx) -> int:
    total: int = 0
    for input in tx.inputs:
        if input.type == InputType.Coin:
            total += state[input.utxoID].amount
        else if input.type == InputType.Contract:
            total += state[tx.witnesses[input.witnessIndex]].amount
    return total

def sum_all_outputs(tx) -> int:
    total: int = 0
    for output in tx.outputs:
        if output.type == OutputType.Coin:
            total += output.amount
        else if output.type == OutputType.Contract:
            total += tx.witnesses[output.amountWitnessIndex]
    return total

return sum_all_inputs(tx) >= sum_all_outputs(tx)
```
