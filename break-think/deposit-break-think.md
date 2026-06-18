# Deposit Break Think

## outboundTransfer(...)

```text
INVARIANT
1. L2 escrowed amount must equal L3 minted.

2. The L2 token must match the L3 token.

CONSEQUENCES

1. This may lead to a different amount minted on L3 or unbacked tokens on L3.

2. This may lead to minting another token instead of the intended L3 token.
```

## outboundEscrowTransfer(...)

```text
INVARIANT
1. Failed token transfer must stop the deposit.

2. L2 escrowed amount must equal L3 minted.

3. The tokens must move from the user to escrow.

CONSEQUENCES

1. The deposit message may be created without a successful token transfer.

2. This may lead to a different amount minted on L3 or unbacked tokens on L3.

3. The bridge may create a message without actually locking the user's tokens.
```

## createRetryableTicket(...)

```text
INVARIANT
1. A user must pay enough gas to finalize the L2 -> L3 message.

2. The calldata must match the L2 deposit.

3. Retryable ticket must connect the L3 gateway.

CONSEQUENCES

1. This may lead to funds being stuck because of delayed or failed finalization.

2. An attacker may change the data, which may lead to minting the wrong amount on L3.

3. The message may be sent to the wrong L3 gateway.
```

## AbsInbox._createRetryableTicket(...)

```text
INVARIANT
1. The retryable ticket must preserve the intended L3 target.

2. The retryable ticket must preserve the intended calldata.

3. L2 sender assumptions must match the aliasing rules.

CONSEQUENCES

1. This may lead to minting on the wrong L3 bridge.

2. An attacker may change calldata (_to, _amount), which may lead to wrong execution on L3.

3. Without aliasing, L3 may see the wrong sender for the message.
```

## finalizeInboundTransfer(...)

```text
INVARIANT
1. The bridge must check the correct aliased address.

2. Only authentic L2 -> L3 bridge message can finalize a deposit.

3. The counterpart gateway must be the expected gateway.

CONSEQUENCES

1. Without correct aliasing, L3 may see the wrong sender for the message.

2. A fake message can mint tokens on L3.

3. A wrong gateway can execute the wrong bridge message.
```
