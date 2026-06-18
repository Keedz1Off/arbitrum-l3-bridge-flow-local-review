# Function Review: getOutboundCalldata(...)

## Function Code

```solidity
function getOutboundCalldata(
    address _token,
    address _from,
    address _to,
    uint256 _amount,
    bytes memory _data
) public view override returns (bytes memory outboundCalldata) {
    outboundCalldata = abi.encodeWithSelector(
        ITokenGateway.finalizeInboundTransfer.selector,
        _token,
        _from,
        _to,
        _amount,
        GatewayMessageHandler.encodeFromL2GatewayMsg(exitNum, _data)
    );

    return outboundCalldata;
}
```

---

## Function Explanation

`getOutboundCalldata(...)` builds the calldata / payload for the L3 -> L3 -> L2 withdrawal message.

This calldata will later be decoded on parent L2 during withdrawal finalization.

Main idea:

```text
The L1 finalization calldata must represent the real L3 -> L2 withdrawal.
```

---

## Important Logic Notes

### Encoding Step

If the function uses `abi.encode(...)` or `abi.encodeWithSelector(...)`, this is where withdrawal values become message data.

Important values usually include:

- parent-chain token
- child-chain token
- sender
- recipient
- amount
- extra data

---

### Amount in Calldata

The encoded amount should match the amount actually burned on L3.

If the encoded amount is larger than the burned amount, L1 may release too much.

---

### Encode / Decode Match

The L2 encode format must match the L1 decode format.

```text
L2 encode format = L1 decode format
```

If the formats differ, L1 may decode the wrong amount, token, or recipient.

---

