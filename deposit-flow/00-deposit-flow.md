# L3 Deposit Flow: L2 -> L3

## Flow

```text
L2 user
-> parent-chain gateway outboundTransfer(...)
-> outboundEscrowTransfer(...)
-> getOutboundCalldata(...)
-> createRetryableTicket(...)
-> AbsInbox._createRetryableTicket(...)
-> child-chain gateway finalizeInboundTransfer(...)
-> inboundEscrowTransfer(...) / mint(...)
```

## Simple Meaning

The user starts a deposit on the parent chain.

For an Arbitrum L3, the parent chain is an L2 and the child chain is the L3.

The parent-chain gateway locks or escrows tokens, then sends a retryable ticket to the L3 gateway.

The child-chain gateway finalizes the message and credits the recipient.
