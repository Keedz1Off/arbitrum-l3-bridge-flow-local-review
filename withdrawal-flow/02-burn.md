# Function Review: burn(...)

## Function Code

```solidity
function outboundEscrowTransfer(
    address _l2Token,
    address _from,
    uint256 _amount
) internal virtual returns (uint256 amountBurnt) {
    // this method is virtual since different subclasses can handle escrow differently
    // user funds are escrowed on the gateway using this function
    // burns child-chain tokens in order to release escrowed parent-chain tokens
    IArbToken(_l2Token).bridgeBurn(_from, _amount);
    // by default we assume that the amount we send to bridgeBurn is the amount burnt
    // this might not be the case for every token
    return _amount;
}
```

---

## Function Explanation

`burn(...)` is the L2 accounting step in the withdrawal flow.

This function removes the user's child-chain token balance before the bridge requests release on parent L2.

Main idea:

```text
L1 must release only what was actually burned on L3.
```

If this step does not happen correctly, the bridge may release parent-chain escrow without proper L2 backing.

---

## Important Logic Notes

### Burn Logic

If the function burns tokens, it should reduce the user's L2 balance or token supply.

This creates the economic basis for releasing tokens on parent L2.

---

### Amount Used Downstream

The amount used in the L1 withdrawal message should match the real burned amount.

If burn fails or moves a different amount, the withdrawal message must not proceed with the wrong value.

---

