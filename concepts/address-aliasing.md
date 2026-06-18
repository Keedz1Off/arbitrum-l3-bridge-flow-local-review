# Address Aliasing In L2 -> L3 Messages

Address aliasing means that when a contract sends a message from the parent chain to the child chain, the child chain may see an aliased version of the sender address.

For L3 context:

```text
L2 contract sender -> aliased sender on L3
```

Simple meaning:

```text
The address seen on L3 may not be the raw L2 contract address.
```

This matters when a gateway checks who sent the cross-chain message.
