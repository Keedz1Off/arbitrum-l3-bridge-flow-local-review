# L3 Withdrawal Flow: L3 -> L2

## Flow

```text
L3 user
-> child-chain gateway outboundTransfer(...) / withdraw(...)
-> burn / lock on L3
-> getOutboundCalldata(...)
-> createOutboundTx(...)
-> parent-chain gateway finalizeInboundTransfer(...) / finalizeWithdrawal(...)
-> inboundEscrowTransfer(...) / release(...)
```

## Simple Meaning

The user starts a withdrawal on the L3 chain.

The child-chain gateway burns or locks value on L3, then creates an outbound message back to the parent chain.

The parent-chain gateway finalizes the message and releases tokens to the recipient on L2.
