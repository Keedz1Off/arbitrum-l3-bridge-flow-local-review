# Function Review: outboundTransfer(...) / withdraw(...)

## Function Code

```solidity
function outboundTransfer(
    address _l1Token,
    address _to,
    uint256 _amount,
    uint256, /* _maxGas */
    uint256, /* _gasPriceBid */
    bytes calldata _data
) public payable virtual override returns (bytes memory res) {
    // This function is set as public and virtual so that subclasses can override
    // it and add custom validation for callers (ie only whitelisted users)

    // the function is marked as payable to conform to the inheritance setup
    // this particular code path shouldn't have a msg.value > 0
    // TODO: remove this invariant for execution markets
    require(msg.value == 0, "NO_VALUE");

    address _from;
    bytes memory _extraData;
    {
        if (isRouter(msg.sender)) {
            (_from, _extraData) = GatewayMessageHandler.parseFromRouterToGateway(_data);
        } else {
            _from = msg.sender;
            _extraData = _data;
        }
    }
    // the inboundEscrowAndCall functionality has been disabled, so no data is allowed
    require(_extraData.length == 0, "EXTRA_DATA_DISABLED");

    uint256 id;
    {
        address l2Token = calculateL2TokenAddress(_l1Token);
        require(l2Token.isContract(), "TOKEN_NOT_DEPLOYED");
        require(_isValidTokenAddress(_l1Token, l2Token), "NOT_EXPECTED_L1_TOKEN");

        _amount = outboundEscrowTransfer(l2Token, _from, _amount);
        id = triggerWithdrawal(_l1Token, _from, _to, _amount, _extraData);
    }
    return abi.encode(id);
}
```

---

## Function Explanation

`outboundTransfer(...)` / `withdraw(...)` is the main child-chain entry point for starting a withdrawal back to L1.

The user calls this function when they want to move value from L3 to L2.

At a high level, this function usually:

- receives withdrawal parameters from the user
- selects the correct token/gateway path
- burns tokens on L3
- builds calldata for L1 finalization
- creates the outbound L3 -> L3 message

Main idea:

```text
The L1 release message must represent what actually happened on L3.
```

---

## Important Logic Notes

### User Inputs

Important values usually include:

- token
- recipient
- amount
- extra data

These values must not corrupt the withdrawal message.

---

### Token / Gateway Selection

The selected child-chain token must map to the correct parent-chain token and gateway.

```text
child-chain token -> correct parent-chain token / gateway
```

Wrong mapping may release the wrong asset on parent L2.

---

### Burn Step

The function should burn the user's child-chain token balance before L1 release can happen.

```text
burned on L3 -> released on L2
```

If the message is created without a real burn, L1 may release unbacked funds.

---

