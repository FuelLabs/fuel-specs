# Transaction Validity

- [Transaction Life Cycle](#transaction-life-cycle)
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

## Transaction Life Cycle

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
  - The [UTXO ID](../identifiers/utxo-id.md) `(txId, outputIndex)`
- For each [input `InputType.Contract`](../tx-format/input.md#inputcontract)
  - The [UTXO ID](../identifiers/utxo-id.md) `(txId, outputIndex)`
- For each [input `InputType.Message`](../tx-format/input.md#inputmessage)
  - The [message ID](../identifiers/utxo-id.md#message-id) `messageID`

Write-create access list:

- For each [output `OutputType.ContractCreated`](../tx-format/output.md#outputcontractcreated)
  - The contract ID `contractID`
- For each output
  - The [created UTXO ID](../identifiers/utxo-id.md)

Note that block proposers use the contract ID `contractID` for inputs and outputs of type [`InputType.Contract`](../tx-format/input.md#inputcontract) and [`OutputType.Contract`](../tx-format/output.md#outputcontract) rather than the pair of `txId` and `outputIndex`.

## VM Precondition Validity Rules

This section defines _VM precondition validity rules_ for transactions: the bare minimum required to accept an unconfirmed transaction into a mempool, and preconditions that the VM assumes to hold prior to execution. Chains of unconfirmed transactions are omitted.

For a transaction `tx`, UTXO set `state`, contract set `contracts`, and message set `messages`, the following checks must pass.

> **Note:** [`InputMessages`](../tx-format/input.md#inputmessage) where `input.dataLength > 0` are not dropped from the `messages` message set until they are included in a transaction of type `TransactionType.Script` with a `ScriptResult` receipt where `result` is equal to `0` indicating a successful script exit.

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
        if not (input.txId, input.outputIndex) in state:
            return False
return True
```

If this check passes, the UTXO ID `(txId, outputIndex)` fields of each contract input is set to the UTXO ID of the respective contract. The `txPointer` of each input is also set to the TX pointer of the UTXO with ID `utxoID`.

### Sufficient Balance

For each asset ID `assetId` in the input and output set:

```py
def gas_to_fee(gas, gasPrice) -> int:
    """
    Converts gas units into a fee amount
    """
    return ceil(gas * gasPrice / GAS_PRICE_FACTOR)


def sum_data_messages(tx, assetId) -> int:
    """
    Returns the total balance available from messages containing data
    """
    total: int = 0
    if assetId == 0:
        for input in tx.inputs:
            if input.type == InputType.Message and input.dataLength > 0:
                total += input.amount
    return total


def sum_inputs(tx, assetId) -> int:
    total: int = 0
    for input in tx.inputs:
        if input.type == InputType.Coin and input.assetId == assetId:
            total += input.amount
        elif input.type == InputType.Message and assetId == 0 and input.dataLength == 0:
            total += input.amount
    return total


def transaction_size_gas_fees(tx) -> int:
    """
    Computes the intrinsic gas cost of a transaction based on size in bytes
    """
    return size(tx) * GAS_PER_BYTE


def minted(tx, assetId) -> int:
    """
    Returns any minted amounts by the transaction
    """
    if tx.type != TransactionType.Mint or assetId != tx.mintAssetId:
        return 0
    return tx.mint_amount


def sum_outputs(tx, assetId) -> int:
    total: int = 0
    for output in tx.outputs:
        if output.type == OutputType.Coin and output.assetId == assetId:
            total += output.amount
    return total


def input_gas_fees(tx) -> int:
    """
    Computes the intrinsic gas cost of verifying input utxos
    """
    total: int = 0
    witnessIndices = set()
    for input in tx.inputs:
        if input.type == InputType.Coin or input.type == InputType.Message:
            # add fees allocated for predicate execution
            if input.predicateLength == 0:
                # notate witness index if input is signed
                witnessIndices.add(input.witnessIndex)
            else:
                # add intrinsic gas cost of predicate merkleization based on number of predicate bytes
                total += contract_code_root_gas_fee(input.predicateLength)
                total += input.predicateGasUsed
                # add intrinsic cost of vm initialization
                total += vm_initialization_gas_fee()
    # add intrinsic cost of verifying witness signatures
    total += len(witnessIndices) * eck1_recover_gas_fee()
    return total


def metadata_gas_fees(tx) -> int:
    """
    Computes the intrinsic gas cost of processing transaction outputs
    
    The `contract_code_root_gas_fee`, `sha256_gas_fee`, and `contract_state_root_gas_fee` 
    are based on the benchmarked gas costs of these operations.
    
    Consensus parameters contain definitions of gas costs for all operations and opcodes in the network.
    """
    total: int = 0
    if tx.type == TransactionType.Create:
        for output in tx.outputs:
            if output.type == OutputType.OutputContractCreated:
                # add intrinsic cost of calculating the code root based on the size of the contract bytecode
                total += contract_code_root_gas_fee(tx.witnesses[tx.bytecodeWitnessIndex].dataLength)
                # add intrinsic cost of calculating the state root based on the number of sotrage slots
                total += contract_state_root_gas_fee(tx.storageSlotCount)
                # add intrinsic cost of calculating the contract id 
                # size = 4 byte seed + 32 byte salt + 32 byte code root + 32 byte state root
                total += sha256_gas_fee(100)
    elif tx.type == TransactionType.Upgrade:
        if tx.upgradePurpose.type == UpgradePurposeType.ConsensusParameters:
            # add intrinsic cost of calculating the consensus parameters hash
            total += sha256_gas_fee(size(tx.witnesses[tx.upgradePurpose.witnessIndex].data))
    elif tx.type == TransactionType.Upload:
        # add intrinsic cost of calculating the root based on the number of bytecode subsections
        total += contract_state_root_gas_fee(tx.subsectionsNumber)
        # add intrinsic cost of hashing the subsection for verification of the connection with Binary Merkle tree root
        total += sha256_gas_fee(size(tx.witnesses[tx.witnessIndex]))
            
    if tx.type != TransactionType.Mint:
        # add intrinsic cost of calculating the transaction id
        total += sha256_gas_fee(size(tx))
    return total


def intrinsic_gas_fees(tx) -> int:
    """
    Computes intrinsic costs for a transaction
    """
    fees: int = 0
    # add the cost of initializing a vm for the script
    if tx.type == TransactionType.Create or tx.type == TransactionType.Script:
        fees += vm_initialization_gas_fee()
        fees += metadata_gas_fees(tx)
        fees += intrinsic_input_gas_fees(tx)
    return fees


def min_gas(tx) -> int:
    """
    Comutes the minimum amount of gas required for a transaction to begin processing.
    """
    gas = transaction_size_gas_fees(tx) + intrinsic_gas_fees(tx)
    if tx.type == TransactionType.Upload
        # charge additionally for storing bytecode on chain
        gas += transaction_size_gas_fees(size(tx.witnesses[tx.witnessIndex]))
        
    return gas


def max_gas(tx) -> int:
    """
    Computes the amount of gas required to process a transaction.
    """
    gas = min_gas(tx)
    gas = gas + (tx.witnessBytesLimit - tx.witnessBytes) * GAS_PER_BYTE
    if tx.type == TransactionType.Script:
       gas += tx.gasLimit
    return gas
    
    
def maxFee(tx, assetId, gasPrice) -> int:
    """
    Computes the maximum potential amount of fees that may need to be charged to process a transaction.
    """
    maxGas = max_gas(tx)
    feeBalance = gas_to_fee(maxGas, gasPrice)
    # Only base asset can be used to pay for gas
    if assetId == 0:
        return feeBalance
    else:
        return 0


def available_balance(tx, assetId) -> int:
    """
    Make the data message balance available to the script
    """
    availableBalance = sum_inputs(tx, assetId) + sum_data_messages(tx, assetId) + minted(tx, assetId)
    return availableBalance


def unavailable_balance(tx, assetId) -> int:
    sentBalance = sum_outputs(tx, assetId)
    # Total fee balance
    feeBalance = tx.policies.max_fee
    # Only base asset can be used to pay for gas
    if assetId == 0:
        return sentBalance + feeBalance
    return sentBalance


# The sum_data_messages total is not included in the unavailable_balance since it is spendable as long as there 
# is enough base asset amount to cover gas costs without using data messages. Messages containing data can't
# cover gas costs since they are retryable.
return available_balance(tx, assetId) >= (unavailable_balance(tx, assetId) + sum_data_messages(tx, assetId))
```

### Valid Signatures

```py
def address_from(pubkey: bytes) -> bytes:
    return sha256(pubkey)[0:32]

for input in tx.inputs:
    if (input.type == InputType.Coin or input.type == InputType.Message) and input.predicateLength == 0:
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

If `tx.scriptLength > 0`, the script must be executed. For each asset ID `assetId` in the input set, the free balance available to be moved around by the script and called contracts is `freeBalance[assetId]`. The initial message balance available to be moved around by the script and called contracts is `messageBalance`:

```py
freeBalance[assetId] = available_balance(tx, assetId) - unavailable_balance(tx, assetId)
messageBalance = sum_data_messages(tx, 0)
```

Once the free balances are computed, the [script is executed](../fuel-vm/index.md#script-execution). After execution, the following is extracted:

1. The transaction in-memory on VM termination is used as the final transaction which is included in the block.
1. The unspent free balance `unspentBalance` for each asset ID.
1. The unspent gas `unspentGas` from the `$ggas` register.

`size(tx)` encompasses the entire transaction serialized according to the transaction format, including witness data.
This ensures every byte of block space either on Fuel or corresponding DA layer can be accounted for.

If the transaction as included in a block does not match this final transaction, the block is invalid.

### Fees

The cost of a transaction can be described by:

```py
def cost(tx, gasPrice) -> int:
    return gas_to_fee(min_gas(tx) + tx.gasLimit - unspentGas, gasPrice)
```

where:

- `min_gas(tx)` is the minimum cost of the transaction in gas, including intrinsic gas fees incurred from:
  - The number of bytes comprising the transaction
  - Processing inputs, including predicates
  - Processing outputs
  - VM initialization
- `unspentGas` is the amount gas left over after intrinsic fees and execution of the transaction, extracted from the `$ggas` register. Converting unspent gas to a fee describes how much "change" is left over from the user's payment; the block producer collects this unspent gas as reward.
- `gas_to_fee` is a function that converts gas to a concrete fee based on a given gas price.

Fees incurred by transaction processing outside the context of execution are collectively referred to as intrinsic fees. Intrinsic fees include the cost of storing the transaction, calculated on a per-byte basis, the cost of processing inputs and outputs, including predicates and signature verification, and initialization of the VM prior to any predicate or script execution. Because intrinsic fees are independent of execution, they can be determined _a priori_ and represent the bare minimum cost of the transaction.

A naturally occurring result of a variable gas limit is the concept of minimum and maximum fees. The minimum fee is, thus, the exact fee required to pay the fee balance, while the maximum fee is the minimum fee plus the gas limit:

```py
min_gas = min_gas(tx)
max_gas = min_gas + (tx.witnessBytesLimit - tx.witnessBytes) * GAS_PER_BYTE + tx.gasLimit
min_fee = gas_to_fee(min_gas, gasPrice)
max_fee = gas_to_fee(max_gas, gasPrice)
```

The cost of the transaction `cost(tx)` must lie within the range defined by [`min_fee`, `max_fee`]. `min_gas` is defined as the sum of all intrinsic costs of the transaction known prior to execution. The definition of `max_gas` illustrates that the delta between minimum gas and maximum gas is the sum of:

- The remaining allocation of witness bytes, converted to gas
- The user-defined `tx.gasLimit`

Note that `gasLimit` applies to transactions of type `Script`. `gasLimit` is not applicable for transactions of type `Create` and is defined to equal `0` in the above formula.

A transaction cost `cost(tx)`, in gas, greater than `max_gas` is invalid and must be rejected; this signifies that the user must provide a higher gas limit for the given transaction. `min_fee` is the minimum reward the producer is guaranteed to collect, and `max_fee` is the maximum reward the producer is potentially eligible to collect. In practice, the user is always charged intrinsic fees; thus, `unspentGas` is the remainder of `max_gas` after intrinsic fees and the variable cost of execution. Calculating a conversion from `unspentGas` to an unspent fee describes the reward the producer will collect in addition to `min_fee`.

## VM Postcondition Validity Rules

This section defines _VM postcondition validity rules_ for transactions: the requirements for a transaction to be valid after it has been executed.

Given transaction `tx`, state `state`, and contract set `contracts`, the following checks must pass.

### Correct Change

If change outputs are present, they must have:

- if the transaction does not revert;
  - if the asset ID is `0`; an `amount` of `unspentBalance + floor((unspentGas * gasPrice) / GAS_PRICE_FACTOR)`
  - otherwise; an `amount` of the unspent free balance for that asset ID after VM execution is complete
- if the transaction reverts;
  - if the asset ID is `0`; an `amount` of the initial free balance plus `(unspentGas * gasPrice) - messageBalance`
  - otherwise; an `amount` of the initial free balance for that asset ID.

### State Changes

Transaction processing is completed by removing spent UTXOs from the state and adding created UTXOs to the state.

### Coinbase Transaction

The coinbase transaction is a mechanism for block creators to collect transaction fees.

In order for a coinbase transaction to be valid:

1. It must be a [Mint](../tx-format/transaction.md#TransactionMint) transaction.
1. The coinbase transaction must be the last transaction within a block, even if there are no other transactions in the block and the fee is zero.
1. The `mintAmount` doesn't exceed the total amount of fees processed from all other transactions within the same block.
1. The `mintAssetId` matches the `assetId` that fees are paid in (`assetId == 0`).

The minted amount of the coinbase transaction intrinsically increases the balance corresponding to the `inputContract`.
This means the balance of `mintAssetId` is directly increased by `mintAmount` on the input contract,
without requiring any VM execution. Compared to coin outputs, intrinsically increasing a contract balance to collect
coinbase amounts prevents the accumulation of dust during low-usage periods.
