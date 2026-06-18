# Function Review: finalizeInboundTransfer(...) / finalizeWithdrawal(...)

## Function Code

```solidity
function finalizeInboundTransfer(
    address _token,
    address _from,
    address _to,
    uint256 _amount,
    bytes calldata _data
) public payable virtual override onlyCounterpartGateway {
    // this function is marked as virtual so superclasses can override it to add modifiers
    (uint256 exitNum, bytes memory callHookData) = GatewayMessageHandler.parseToL1GatewayMsg(
        _data
    );

    if (callHookData.length != 0) {
        // callHookData should always be 0 since inboundEscrowAndCall is disabled
        callHookData = bytes("");
    }

    // we ignore the returned data since the callHook feature is now disabled
    (_to, ) = getExternalCall(exitNum, _to, callHookData);
    inboundEscrowTransfer(_token, _to, _amount);

    emit WithdrawalFinalized(_token, _from, _to, exitNum, _amount);
}
```

---

## Function Explanation

`finalizeInboundTransfer(...)` / `finalizeWithdrawal(...)` finalizes the withdrawal on parent L2.

This function is executed after the L3 -> L3 message is proven or accepted by the bridge system.

Main idea:

```text
Only an authentic L3 -> L2 withdrawal message should release funds on parent L2.
```

This is where message data becomes real parent-chain token release.

---

## Important Logic Notes

### Authentication Check

The function must verify that the message came from the expected child-chain gateway through the correct bridge path.

Weak authentication here can allow forged withdrawals.

---

### Decoded Transfer Values

The function usually receives or decodes:

- token
- sender
- recipient
- amount
- extra data

These values must match the message created on L3.

---

### Release Step

After verification, this function releases escrowed parent-chain tokens to the recipient.

The release amount should match the verified burned amount from L2.

---

