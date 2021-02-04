# Transaction Validity

- [Standardness Rules](#standardness-rules)
    - [Version](#version)
    - [Transaction Maturity](#transaction-maturity)
    - [Input Maturity](#input-maturity)
    - [Counts](#counts)
    - [Script Length](#script-length)
    - [Predicate Lengths](#predicate-lengths)
    - [Spending UTXOs](#spending-utxos)
    - [Sufficient Balance](#sufficient-balance)
- [Validity Rules](#validity-rules)

## Standardness Rules

This section defines _standardness rules_ for transactions: the bare minimum required to accept an unconfirmed transaction into a mempool. Chains of unconfirmed transactions are omitted.

For a transaction `tx` and state `state`, the following checks must pass.

### Version

```py
return tx.version == 0
```

### Transaction Maturity

```py
return blockheight() >= tx.maturity
```

### Input Maturity

```py
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
return scriptLength <= MAX_SCRIPT_LENGTH
```

### Predicate Lengths

```py
for input in tx.inputs:
    if input.type == InputType.Coin:
        if input.dataLength >= MAX_PREDICATE_LENGTH
            return False
return True
```

### Spending UTXOs

```py
for input in tx.inputs:
    if not tx.utxoID in state:
        return False
return True
```

### Sufficient Balance

```py
def sum_inputs(tx) -> int:
    total: int = 0
    for input in tx.inputs:
        if input.type == InputType.Coin:
            total += state[tx.utxoID]
    return total

def sum_outputs(tx) -> int:
    total: int = 0
    for output in tx.outputs:
        if output.type == OutputType.Coin:
            total += output.amount
    return total

availableBalance = sum_inputs(tx)
sentBalance = sum_outputs(tx)
gasBalance = gasPrice * gasLimit
bytesBalance = size(tx) * GAS_PER_BYTE * gasPrice
# Note: we don't charge for predicate verification because predicates are
# monotonic and the cost of Ethereum calldata more than makes up for this
return availableBalance - sentBalance - gasBalance - bytesBalance >= 0
```

## Validity Rules

This section defines _validity rules_ for transactions: the requirements for a confirmed transaction to be valid.
