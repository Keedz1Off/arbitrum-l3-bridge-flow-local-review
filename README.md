# Arbitrum L3 Bridge Flow Local Review

This repository is an educational local review of the Arbitrum L3 bridge flow.

The goal is to understand the architecture and the important functions before writing manual Break Think notes.

## Main Idea

The L3 bridge uses mostly the same core gateway logic as the Arbitrum L2 bridge.

The main difference is the chain context.

```text
L2 bridge: L1 <-> L2
L3 bridge: L2 <-> L3
```

For L3:

```text
parent chain = L2
child chain  = L3
```

## Deposit Flow: L2 -> L3

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

## Withdrawal Flow: L3 -> L2

```text
L3 user
-> child-chain gateway outboundTransfer(...) / withdraw(...)
-> burn / lock on L3
-> getOutboundCalldata(...)
-> createOutboundTx(...)
-> parent-chain gateway finalizeInboundTransfer(...) / finalizeWithdrawal(...)
-> inboundEscrowTransfer(...) / release(...)
```

## What Changes In L3

```text
L1 gateway becomes parent-chain L2 gateway.
L2 gateway becomes child-chain L3 gateway.
Retryable tickets go from L2 to L3.
Withdrawals go from L3 back to L2.
Token mapping is between L2 token and L3 token.
```

## Repository Structure

```text
arbitrum-l3-bridge-flow-local-review/
+-- README.md
+-- deposit-flow/
+-- withdrawal-flow/
+-- concepts/
+-- break-think/
```

## Break Think

The `break-think/` folder is only a placeholder for manual notes later.

This repository documents architecture and function behavior only.
