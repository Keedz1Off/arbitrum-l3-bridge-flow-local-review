# Withdrawal Break Think

## outboundTransfer(...) / withdraw(...)

```text
INVARIANT
1. L3 burned amount must equal L2 released amount.

2. The L3 token must map to the correct L2 token.

3. The tokens must go to the correct recipient.

CONSEQUENCES

1. The bridge may release more or less tokens on L2 than were actually burned on L3.

2. The token on L2 does not match the token on L3, so the user may receive the wrong token.

3. The tokens may be sent to the wrong recipient or lost.
```

## burn(...)

```text
INVARIANT
1. The burned amount must equal the released amount.

2. Failed burn must stop the withdrawal flow.

3. Tokens must be burned before L2 release.

CONSEQUENCES

1. The bridge may release more tokens on L2 than were actually burned on L3.

2. The withdrawal message may be created even though the burn failed.

3. This may lead to releasing tokens on L2 without burning tokens on L3.
```

## finalizeInboundTransfer(...) / finalizeWithdrawal(...)

```text
INVARIANT
1. Only an authentic L3 -> L2 bridge message may finalize a withdrawal.

2. The counterpart gateway must be the expected gateway.

3. Released amount must equal burned amount.

CONSEQUENCES

1. A fake message can release tokens on L2.

2. A wrong gateway can execute the wrong bridge message.

3. The bridge may release more tokens on L2 than were actually burned on L3.
```
