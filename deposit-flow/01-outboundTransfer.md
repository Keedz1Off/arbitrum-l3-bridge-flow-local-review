# Function Review: outboundTransfer(...)

## Function Code

```solidity
function outboundTransfer(
    address _l1Token,
    address _to,
    uint256 _amount,
    uint256 _maxGas,
    uint256 _gasPriceBid,
    bytes calldata _data
) public payable override returns (bytes memory res) {
    return
        outboundTransferCustomRefund(_l1Token, _to, _to, _amount, _maxGas, _gasPriceBid, _data);
}
```

---

## Function Explanation

`outboundTransfer(...)` is the main parent-chain entry point for starting a deposit into Arbitrum.

The user calls this function to bridge tokens from L2 to L3.

At a high level, this function usually:

- receives deposit parameters from the user
- selects the correct token/gateway path
- moves tokens into parent-chain escrow
- builds calldata for the L3 message
- creates the retryable ticket that will later execute on L3

The main security idea is:

```text
The L3 message must represent what actually happened on parent L2.
```

If the function creates an L3 message using wrong or unverified values, the destination chain may credit tokens that are not correctly backed by source-chain escrow.

---

## Important Logic Notes

### Token / Gateway Selection

If the function selects or resolves a child-chain token/gateway, this part defines which token or gateway will be used on the destination chain.

This matters because the bridge must preserve token identity across chains.

```text
parent-chain token -> correct child-chain token / gateway
```

If this mapping is wrong, the bridge may lock one asset on parent L2 but credit another asset on L3.

---

### Escrow Step

If the function calls something like:

```solidity
outboundEscrowTransfer(...);
```

this is the accounting boundary.

This is where the bridge should actually receive or lock the user's parent-chain tokens.

Important distinction:

```text
amount = what the user requested to send
actualReceived = what the bridge actually received
```

For standard ERC20 tokens, these values usually match.

For fee-on-transfer or non-standard tokens, they may differ.

The safest accounting idea is:

```text
actualReceived = balanceAfter - balanceBefore
```

If the bridge receives less than the amount later encoded for L3, the bridge can credit unbacked value on L3.

---

### Calldata Construction

If the function builds calldata using something like:

```solidity
getOutboundCalldata(...);
```

this is the message construction boundary.

This step decides which values will be sent to L2.

Important values:

- token
- recipient
- amount
- extra data

Calldata does not prove correctness by itself.

It only stores the values that the contract decided to encode.

So the important point is:

```text
The values must be correct before they are encoded.
```

---

### Retryable Ticket Creation

If the function calls something like:

```solidity
createRetryableTicket(...);
```

this is the L2 -> L3 message boundary.

This step sends the prepared calldata to Arbitrum for L2 execution.

The retryable ticket should target the correct child-chain gateway and carry the correct calldata.

A retryable ticket can be valid at the messaging layer but still contain wrong bridge data.

---

